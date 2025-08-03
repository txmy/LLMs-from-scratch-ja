# ボーナス教材: KVキャッシュ



**このフォルダは、GPTモデルへのKVキャッシュの追加を実装しています。** 

&nbsp;
## 概要

簡単に言うと、KVキャッシュは推論中に再利用するために中間のキー（K）と値（V）の計算を保存し、応答生成時に大幅なスピードアップをもたらします。欠点は、コードに複雑さを追加し、メモリ使用量を増やし、トレーニング中には使用できないことです。しかし、推論速度の向上は、LLMをデプロイする際のコードの複雑さとメモリのトレードオフに見合うことが多いです。

&nbsp;
## 動作原理

LLMが何らかのテキストを生成していると想像してください。具体的には、LLMに次のプロンプトが与えられたとします：「Time flies」。

以下の図は、第3章の修正したグラフィックを使用して、キーと値のベクトルを強調表示したアテンションスコア計算の基礎的な部分を示しています：

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/kv-cache/kv-cache-attn-1.png?3" width=800>

さて、第2章と第4章で学んだように、LLMは一度に1つの単語（またはトークン）を生成します。LLMが「fast」という単語を生成し、次のラウンドのプロンプトが「Time flies fast」になったとします。これは次の図に示されています：

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/kv-cache/kv-cache-attn-2.png?3" width=800>

前の2つの図を比較してわかるように、最初の2つのトークンのキーと値のベクトルはまったく同じであり、次のトークンテキスト生成ラウンドごとにそれらを再計算するのは無駄です。

そこで、KVキャッシュのアイデアは、以前に生成されたキーと値のベクトルを再利用のために保存するキャッシュメカニズムを実装することで、不要な再計算を回避するのに役立ちます。

&nbsp;

## KVキャッシュの実装

KVキャッシュを実装する方法は多くありますが、主なアイデアは、各生成ステップで新しく生成されたトークンのキーと値のテンソルのみを計算することです。

私は、コードの可読性を重視したシンプルなものを選びました。実装方法を見るには、コードの変更をスクロールして見るのが最も簡単だと思います。

このフォルダには2つのファイルがあります：

1. [`gpt_ch04.py`](gpt_ch04.py)：第3章と第4章から取った自己完結型のコードで、LLMを実装し、シンプルなテキスト生成関数を実行します
2. [`gpt_with_kv_cache.py`](gpt_with_kv_cache.py)：上記と同じですが、KVキャッシュを実装するために必要な変更が加えられています。

次のいずれかができます：

a. [`gpt_with_kv_cache.py`](gpt_with_kv_cache.py)ファイルを開いて、新しい変更をマークする`# NEW`セクションを探す：

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/kv-cache/new-sections.png?3" width=800>

b. お好みのファイル差分ツールで2つのコードファイルをチェックして変更を比較する：

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/kv-cache/file-diff.png?3" width=800>

実装の詳細を要約すると、以下に短いウォークスルーがあります。

&nbsp;

### 1. キャッシュバッファの登録

`MultiHeadAttention`コンストラクタの内部で、ステップ間で連結されたキーと値を保持する2つの非永続バッファ`cache_k`と`cache_v`を追加します：

```python
self.register_buffer("cache_k", None, persistent=False)
self.register_buffer("cache_v", None, persistent=False)
```

&nbsp;

### 2. `use_cache`フラグを使用したフォワードパス

次に、`MultiHeadAttention`クラスの`forward`メソッドを拡張して`use_cache`引数を受け入れるようにします。新しいトークンのチャンクを`keys_new`、`values_new`、`queries`に投影した後、kvキャッシュを初期化するか、キャッシュに追加します：

```python
def forward(self, x, use_cache=False):
    b, num_tokens, d_in = x.shape

    keys_new = self.W_key(x)  # Shape: (b, num_tokens, d_out)
    values_new = self.W_value(x)
    queries = self.W_query(x)
    #...

    if use_cache:
        if self.cache_k is None:
            self.cache_k, self.cache_v = keys_new, values_new
        else:
            self.cache_k = torch.cat([self.cache_k, keys_new], dim=1)
            self.cache_v = torch.cat([self.cache_v, values_new], dim=1)
        keys, values = self.cache_k, self.cache_v
    else:
        keys, values = keys_new, values_new
        
    # ...
    
    num_tokens_Q = queries.shape[-2]
    num_tokens_K = keys.shape[-2]
    if use_cache:
        mask_bool = self.mask.bool()[
            self.ptr_current_pos:self.ptr_current_pos + num_tokens_Q, :num_tokens_K
        ]
        self.ptr_current_pos += num_tokens_Q
    else:
        mask_bool = self.mask.bool()[:num_tokens_Q, :num_tokens_K]
```

&nbsp;


### 3. キャッシュのクリア

テキストを生成する際、独立したシーケンス間（例えば、テキスト生成呼び出し間）で両方のバッファをリセットする必要があるため、`MultiHeadAttention`クラスにキャッシュリセットメソッドも追加します：

```python
def reset_cache(self):
    self.cache_k, self.cache_v = None, None
    self.ptr_current_pos = 0
```

&nbsp;

### 4. フルモデルでの`use_cache`の伝播

`MultiHeadAttention`クラスへの変更が完了したら、`GPTModel`クラスを修正します。まず、インストラクターにトークンインデックスの位置追跡を追加します：

```python
self.current_pos = 0
```

次に、ワンライナーブロック呼び出しを明示的なループに置き換え、各トランスフォーマーブロックを通して`use_cache`を渡します：

```python
def forward(self, in_idx, use_cache=False):
    # ...
 
    if use_cache:
        pos_ids = torch.arange(
            self.current_pos, self.current_pos + seq_len,            
            device=in_idx.device, dtype=torch.long
        )
        self.current_pos += seq_len
    else:
        pos_ids = torch.arange(
            0, seq_len, device=in_idx.device, dtype=torch.long
        )
    
    pos_embeds = self.pos_emb(pos_ids).unsqueeze(0)
    x = tok_embeds + pos_embeds
    # ...
    for blk in self.trf_blocks:
        x = blk(x, use_cache=use_cache)
```

上記の変更により、`TransformerBlock`クラスも`use_cache`引数を受け入れるように小さな修正が必要になります：
```python
    def forward(self, x, use_cache=False):
        # ...
        self.att(x, use_cache=use_cache)
```

最後に、便宜上、すべてのブロックキャッシュを一度にクリアするために`GPTModel`にモデルレベルのリセットを追加します：

```python
def reset_kv_cache(self):
    for blk in self.trf_blocks:
        blk.att.reset_cache()
    self.current_pos = 0
```

&nbsp;

### 5. 生成でのキャッシュの使用

`GPTModel`、`TransformerBlock`、`MultiHeadAttention`への変更により、最後に、シンプルなテキスト生成関数でKVキャッシュを使用する方法を以下に示します：

```python
def generate_text_simple_cached(model, idx, max_new_tokens, 
                                context_size=None, use_cache=True):
    model.eval()
    ctx_len = context_size or model.pos_emb.num_embeddings

    with torch.no_grad():
        if use_cache:
            # Init cache with full prompt
            model.reset_kv_cache()
            logits = model(idx[:, -ctx_len:], use_cache=True)

            for _ in range(max_new_tokens):
                # a) pick the token with the highest log-probability (greedy sampling)
                next_idx = logits[:, -1].argmax(dim=-1, keepdim=True)
                # b) append it to the running sequence
                idx = torch.cat([idx, next_idx], dim=1)
                # c) feed model only the new token
                logits = model(next_idx, use_cache=True)
        else:
            for _ in range(max_new_tokens):
                logits = model(idx[:, -ctx_len:], use_cache=False)
                next_idx = logits[:, -1].argmax(dim=-1, keepdim=True)
                idx = torch.cat([idx, next_idx], dim=1)

    return idx
```

c)で`logits = model(next_idx, use_cache=True)`を介してモデルに新しいトークンのみをフィードすることに注意してください。キャッシュなしでは、再利用する保存されたキーと値がないため、モデルに全体の入力`logits = model(idx[:, -ctx_len:], use_cache=False)`をフィードします。

&nbsp;

## シンプルなパフォーマンス比較

概念レベルでKVキャッシュをカバーした後、大きな質問は、小さな例で実際にどれだけうまく機能するかです。実装を試すために、前述の2つのコードファイルをPythonスクリプトとして実行できます。これにより、小さな124Mパラメータのゆを実行して200個の新しいトークンを生成します（開始するための4トークンのプロンプト「Hello, I am」が与えられています）：

```bash
pip install -r https://raw.githubusercontent.com/rasbt/LLMs-from-scratch/refs/heads/main/requirements.txt

python gpt_ch04.py

python gpt_with_kv_cache.py
```

M4チップ（CPU）を搭載したMac Miniでの結果は次のとおりです：

|                        | トークン/秒 |
| ---------------------- | ---------- |
| `gpt_ch04.py`          | 27         |
| `gpt_with_kv_cache.py` | 144        |

したがって、わかるように、小さな124Mパラメータモデルと短い200トークンのシーケンス長ですでに約5倍のスピードアップが得られます。（この実装はコードの可読性のために最適化されており、CUDAまたはMPSランタイム速度のために最適化されていないことに注意してください。これには、テンソルを再インスタンス化して連結する代わりに事前に割り当てる必要があります。）

**注：**モデルは両方のケースで「意味不明な」テキストを生成します。つまり、次のようなテキストです：

> Output text: Hello, I am Featureiman Byeswickattribute argue logger Normandy Compton analogous bore ITVEGIN ministriesysics Kle functional recountrictionchangingVirgin embarrassedgl ...

これは、まだモデルをトレーニングしていないためです。次の章でモデルをトレーニングし、トレーニング済みモデルでKVキャッシュを使用できます（ただし、KVキャッシュは推論中にのみ使用されることを意図しています）。ここでは、コードをシンプルに保つために未トレーニングのモデルを使用しています。

より重要なのは、`gpt_ch04.py`と`gpt_with_kv_cache.py`の実装が両方ともまったく同じテキストを生成することです。これは、KVキャッシュが正しく実装されていることを示しています - 異なる結果につながる可能性のあるインデックス作成のミスを犯しやすいです。


&nbsp;

## KVキャッシュの利点と欠点

シーケンス長が増加するにつれて、KVキャッシュの利点と欠点は次のようにより顕著になります：

- [良い] **計算効率が向上する**：キャッシュなしでは、ステップ*t*でのアテンションは新しいクエリを*t*個の以前のキーと比較する必要があるため、累積作業は二次的にスケールします、O(n²)。キャッシュを使用すると、各キーと値は一度計算されてから再利用され、ステップごとの総複雑度が線形、O(n)に削減されます。

- [悪い] **メモリ使用量が線形に増加する**：新しいトークンはそれぞれKVキャッシュに追加されます。長いシーケンスとより大きなLLMの場合、累積KVキャッシュはより大きくなり、（GPU）メモリの大量または禁止的な量を消費する可能性があります。回避策として、KVキャッシュを切り捨てることができますが、これはさらに複雑さを追加します（しかし、繰り返しになりますが、LLMをデプロイする際には価値があるかもしれません。）



&nbsp;
## KVキャッシュ実装の最適化

上記のKVキャッシュの概念的な実装は明確性に役立ち、主にコードの可読性と教育目的に向けられていますが、実際のシナリオ（特により大きなモデルとより長いシーケンス長）でデプロイするには、より慎重な最適化が必要です。

&nbsp;
### キャッシュをスケーリングする際の一般的な落とし穴

- **メモリの断片化と繰り返し割り当て**：前に示したように`torch.cat`を介してテンソルを継続的に連結すると、頻繁なメモリ割り当てと再割り当てのためにパフォーマンスのボトルネックにつながります。

- **メモリ使用量の線形成長**：適切な処理なしでは、KVキャッシュサイズは非常に長いシーケンスには実用的ではなくなります。

&nbsp;
#### ヒント1：メモリの事前割り当て

テンソルを繰り返し連結するのではなく、予想される最大シーケンス長に基づいて十分に大きなテンソルを事前に割り当てることができます。これにより、一貫したメモリ使用が保証され、オーバーヘッドが削減されます。疑似コードでは、次のようになります：

```python
# キーと値の事前割り当ての例
max_seq_len = 1024  # 予想される最大シーケンス長
cache_k = torch.zeros((batch_size, num_heads, max_seq_len, head_dim), device=device)
cache_v = torch.zeros((batch_size, num_heads, max_seq_len, head_dim), device=device)
```

推論中、これらの事前割り当てされたテンソルのスライスに単純に書き込むことができます。

&nbsp;
#### ヒント2：スライディングウィンドウによるキャッシュの切り捨て

GPUメモリの爆発を避けるために、動的切り捨てを伴うスライディングウィンドウアプローチを実装できます。スライディングウィンドウを介して、キャッシュに最後の`window_size`トークンのみを維持します：


```python
# スライディングウィンドウキャッシュの実装
window_size = 512
cache_k = cache_k[:, :, -window_size:, :]
cache_v = cache_v[:, :, -window_size:, :]
```

&nbsp;
#### 実践での最適化

これらの最適化は[`gpt_with_kv_cache_optimized.py`](gpt_with_kv_cache_optimized.py)ファイルで見つけることができます。


M4チップ（CPU）を搭載したMac Miniで、200トークンの生成とコンテキスト長に等しいウィンドウサイズ（同じ結果を保証するため）を使用した場合、コードのランタイムは次のように比較されます：

|                                  | トークン/秒 |
| -------------------------------- | ---------- |
| `gpt_ch04.py`                    | 27         |
| `gpt_with_kv_cache.py`           | 144        |
| `gpt_with_kv_cache_optimized.py` | 166        |

残念ながら、CUDAデバイスではこれは小さなモデルであるため、速度の利点は消えてしまいます。この小さなモデルでは、デバイス転送と通信がKVキャッシュの利点を上回ります。


&nbsp;
## 追加リソース

1. [Qwen3 from-scratch KVキャッシュベンチマーク](../../ch05/11_qwen3#pro-tip-2-speed-up-inference-with-compilation)
2. [Llama 3 from-scratch KVキャッシュベンチマーク](../../ch05/07_gpt_to_llama/README.md#pro-tip-3-speed-up-inference-with-compilation)
3. [LLMのKVキャッシュをゼロから理解しコーディングする](https://magazine.sebastianraschka.com/p/coding-the-kv-cache-in-llms) -- このREADMEのより詳細な説明