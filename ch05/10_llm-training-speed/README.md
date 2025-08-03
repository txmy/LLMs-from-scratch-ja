# より高速なLLM学習のためのPyTorch性能ヒント

この本は教育目的で書かれているため、元のコードは意図的にシンプルに保たれています。これは可読性を向上させ、CPUやGPUを含む異なるハードウェア間での互換性を確保するためです。ただし、LLM学習をより性能的にするための、より高度なPyTorchとGPU機能について興味があるかもしれません。

このフォルダには、第5章で導入されたLLMと学習関数の性能最適化を実演する3つのコードファイルが含まれています：

1. [`00_orig.py`](00_orig.py): CPUと単一GPU学習のための元の第5章コード  
   ➤ 実行方法: `python 00_orig.py`

2. [`01_opt_single_gpu.py`](01_opt_single_gpu.py): 単一GPU学習のための最適化版  
   ➤ 実行方法: `python 01_opt_single_gpu.py`

3. [`02_opt_multi_gpu_ddp.py`](02_opt_multi_gpu_ddp.py): 分散データ並列処理（DDP）を使用したマルチGPU学習のための最適化版  
   ➤ 実行方法: `torchrun --nproc_per_node=4 02_opt_multi_gpu_ddp.py`  
   （**注意：** `01_opt_single_gpu.py`と比較して変更を最小限に抑えるため、このスクリプトは上記に示すように`torchrun`経由のマルチプロセッシングのみをサポートします。これは、`python 02_opt_multi_gpu_ddp.py`経由ではマルチGPUサポートが**サポートされていない**ことを意味します）

**これらの修正により、学習速度は12,525トークン/秒（単一A100）から142,156トークン/秒（単一A100）、419,259トークン/秒（4つのA100）になりました。**

将来、より詳細な書き込みで違いを拡張する予定です。現在のところ、コードに追加された改善点を確認する最も簡単な方法は、Visual Studio Codeでファイルを開き、「選択されたものを比較」機能を使用して違いを見ることです。

![VS compare](https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/llm-training-speed/vs-code-compare.png)

![PyTorch Tips](https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/pytorch-tips/pytorch-tips.webp?1)

&nbsp;
## 単一GPU速度比較

上記で述べたように、将来変更について詳しく説明する予定です。現在のところ、このセクションには各修正についてのトークン/秒の観点からの単純な性能概要が含まれています。すべての実験はA100 GPUで実行されました。

&nbsp;
### ベースライン

`00_orig.py`はベースラインとして機能し、以下を除いて重要な修正を含まず、第5章のコードをそのまま使用していることに注意してください：

- 4倍長いコンテキスト長（これは`00_orig.py`の比較的大きなメモリフットプリントを説明します）；
- 4倍のバッチサイズ変更（`00_orig.py`の比較的大きなメモリフットプリントのもう一つの原因）；
- 学習データサイズを増やすためのより大きな公有の書籍。

ハイパーパラメータは損失の最小化や過学習の削減に最適化されておらず、最後にLLMによって生成されるテキストはあまり洗練されていない可能性があります；ただし、これは主な要点が速度参照として機能する`tok/sec`メトリックであるため問題ではありません（高いほど良い）。

```bash
ubuntu@159-13-52-60:~$ python 00_orig.py
PyTorch version: 2.6.0+cu124
Using cuda
CUDA version: 12.4

Ep 1, Step 000000, Train: 9.535, Val: 9.609, Step tok/sec: 7238, Avg tok/sec: 0
Ep 1, Step 000015, Train: 6.201, Val: 6.152, Step tok/sec: 12545, Avg tok/sec: 12545
Ep 1, Step 000030, Train: 5.663, Val: 5.688, Step tok/sec: 12490, Avg tok/sec: 12517
Ep 1, Step 000045, Train: 5.316, Val: 5.362, Step tok/sec: 12541, Avg tok/sec: 12525
Every effort moves you, and's, and I am not be a

...

Ep 15, Step 000735, Train: 0.227, Val: 6.818, Step tok/sec: 11599, Avg tok/sec: 12248
Ep 15, Step 000750, Train: 0.300, Val: 6.895, Step tok/sec: 12530, Avg tok/sec: 12253
Ep 15, Step 000765, Train: 0.150, Val: 6.914, Step tok/sec: 12532, Avg tok/sec: 12259
Every effort moves you like best to think which he held in the room in him, the interest was the night, the realities of the affairs Bulstrode's duty, now!' the fact is another man, conquests

Allocated memory: 2.5069 GB
Reserved memory: 26.2617 GB
```

`01_opt_single_gpu.py`には以下に順次リストされているすべての修正が含まれていることに注意してください。

比較は常に前のセクションの最初のエポック後の平均tok/secと割り当てメモリに基づいています。

&nbsp;
### 1. カジュアルマスクをオンザフライで作成

- カジュアルマスクを保存する代わりに、メモリ使用量を削減するためにオンザフライでカジュアルマスクを作成します（ここでは最小限の効果しかありませんが、Llama 3.2のような131kトークン入力サポートを持つ長コンテキストサイズモデルでは蓄積される可能性があります）

変更前：
- `Avg tok/sec: 12525`
- `Reserved memory: 26.2617 GB`

変更後：
- `Avg tok/sec: 12526`
- `Reserved memory: 26.2422 GB`

&nbsp;
### 2. テンサーコアの使用

- テンサーコアを使用（A100のようなAmpere GPU以降でのみ動作）

変更前：
- `Avg tok/sec: 12526`
- `Reserved memory: 26.2422 GB`

変更後：
- `Avg tok/sec: 27648`
- `Reserved memory: 26.2422 GB`

&nbsp;
### 3. 融合AdamWオプティマイザー

- `fused=True`を設定して`AdamW`の融合カーネルを使用

変更前：
- `Avg tok/sec: 27648`
- `Reserved memory: 26.2422 GB`

変更後：
- `Avg tok/sec: 28399`
- `Reserved memory: 26.2422 GB`

&nbsp;
### 4. データローダーでのピン留めメモリ

- データローダーで`pin_memory=True`を使用してGPUメモリを事前割り当てして再利用

変更前：
- `Avg tok/sec: 28399`
- `Reserved memory: 26.2422 GB`

変更後：
- `Avg tok/sec: 28402`
- `Reserved memory: 26.2422 GB`

&nbsp;
### 5. bfloat16精度の使用

- 32ビット浮動小数点から16ビットブレイン浮動小数点（bfloat16）精度に切り替え（このトピックについて詳しくは、[こちらの記事](https://magazine.sebastianraschka.com/p/the-missing-bits-llama-2-weights)を参照）

変更前：
- `Avg tok/sec: 28402`
- `Reserved memory: 26.2422 GB`

変更後：
- `Avg tok/sec: 45486`
- `Reserved memory: 13.7871 GB`

&nbsp;
### 6. 自作コードをPyTorchクラスで置き換え

- LayerNormとGeLUの自作実装をPyTorchのネイティブ実装で置き換え

変更前：
- `Avg tok/sec: 45486`
- `Reserved memory: 13.7871 GB`

変更後：
- `Avg tok/sec: 55256`
- `Reserved memory: 11.5645 GB`

&nbsp;
### 7. FlashAttentionの使用

- 自作マルチヘッドアテンション実装の代わりにFlashAttentionを使用したPyTorchの自己アテンション関数を使用

変更前：
- `Avg tok/sec: 55256`
- `Reserved memory: 11.5645 GB`

変更後：
- `Avg tok/sec: 91901`
- `Reserved memory: 5.9004 GB`

&nbsp;
### 8. `pytorch.compile`の使用

- `torch.compile(model)`を使用。最初の反復は速度が上がる前に常に遅いことに注意してください。`Avg tok/sec`測定は平均計算の最初の行のみを含むため、ここではエポック1の終わりの`Step tok/sec`を使用します。

変更前：
- `Avg tok/sec: 91901`
- `Reserved memory: 5.9004 GB`

変更後：
- `Step tok/sec: 112046`
- `Reserved memory: 6.1875 GB`

&nbsp;
### 9. 語彙パディング

- ここでは、語彙サイズを50,257から50,304に少し増加させます。これは64の最も近い倍数です。このヒントは私の元同僚Carlos Mocholiによって提案されました。彼は元々Andrej Karpathy（おそらく[この投稿](https://x.com/karpathy/status/1621578354024677377)から）からのものだと述べています。Karpathyの推奨は、[Bertrand Maher](https://www.linkedin.com/feed/update/urn:li:activity:7309569006057795584?commentUrn=urn%3Ali%3Acomment%3A%28activity%3A7309569006057795584%2C7309754284185669632%29&dashCommentUrn=urn%3Ali%3Afsd_comment%3A%287309754284185669632%2Curn%3Ali%3Aactivity%3A7309569006057795584%29)によって言及されているように、`torch.compile`についてのアドバイスを与えたPyTorchチームとの相互作用に基づいています。これについての良いリソースは[NVIDIAのテンサー形状に関するガイドライン](https://docs.nvidia.com/deeplearning/performance/mixed-precision-training/index.html#tensor-core-shape)で、バッチサイズと線形層の次元は一般的に特定の値の倍数として選択されます。さらに、語彙パディングのトリックは、NVIDIAのMegatronチームによって長い間説明されていました（2019年の[Megatron-LM: Model Parallelismを使用したマルチビリオンパラメータ言語モデルの学習](https://arxiv.org/abs/1909.08053)論文を参照）。

変更前：
- `Step tok/sec: 112046`
- `Reserved memory: 6.1875 GB`

変更後：
- `Step tok/sec: 127345`
- `Reserved memory: 5.8906 GB`

&nbsp;
### 10. バッチサイズの増加

- 最後に、バッチサイズをGPUでサポートされる最大の2の累乗に増加

変更前：
- `Step tok/sec: 127345`
- `Reserved memory: 5.8906 GB`

変更後：
- `Step tok/sec: 142156`
- `Reserved memory: 22.5078 GB`

&nbsp;
## マルチGPU速度比較

これは1つではなく4つのGPUを使用するため、完全に公平な比較ではない可能性がありますが、分散データ並列処理（学習がGPUメモリ不足によってボトルネックになっていない場合に使用できる最速のマルチGPU技術）を使用することで、当然顕著な速度向上をもたらすことができます：

変更前（単一GPU）：
- `Step tok/sec: 142156`
- `Reserved memory: 22.5078 GB`

変更後（4つのGPU）：
- `Step tok/sec: 419259`
- `Reserved memory: 22.7969 GB`