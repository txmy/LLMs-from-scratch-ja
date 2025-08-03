# GPTからLlamaへの変換

このフォルダには、第4章と第5章のGPT実装をMeta AIのLlamaアーキテクチャに変換するコードが以下の推奨読み順で含まれています：

- [converting-gpt-to-llama2.ipynb](converting-gpt-to-llama2.ipynb): GPTをLlama 2 7Bに段階的に変換し、Meta AIの事前学習済み重みを読み込むコードが含まれています
- [converting-llama2-to-llama3.ipynb](converting-llama2-to-llama3.ipynb): Llama 2モデルをLlama 3、Llama 3.1、Llama 3.2に変換するコードが含まれています
- [standalone-llama32.ipynb](standalone-llama32.ipynb): Llama 3.2を実装するスタンドアロンノートブック

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/gpt-to-llama/gpt-and-all-llamas.webp">

&nbsp;
### `llms-from-scratch`パッケージを介したLlama 3.2の使用

Llama 3.2 1Bおよび3Bモデルを簡単に使用するために、[pkg/llms_from_scratch](../../pkg/llms_from_scratch)のこのリポジトリのソースコードに基づく`llms-from-scratch` PyPIパッケージも使用できます。

&nbsp;
#### 1) インストール

```bash
pip install llms_from_scratch blobfile
```

（`blobfile`はトークナイザーの読み込みに必要です。）

&nbsp;
#### 2) モデルとテキスト生成設定

使用するモデルを指定します：

```python
MODEL_FILE = "llama3.2-1B-instruct.pth"
# MODEL_FILE = "llama3.2-1B-base.pth"
# MODEL_FILE = "llama3.2-3B-instruct.pth"
# MODEL_FILE = "llama3.2-3B-base.pth"
```

ユーザーが定義できる基本的なテキスト生成設定。推奨される8192トークンのコンテキストサイズには、テキスト生成例で約3 GBのVRAMが必要です。

```python
# テキスト生成設定
if "instruct" in MODEL_FILE:
    PROMPT = "What do llamas eat?"
else:
    PROMPT = "Llamas eat"

MAX_NEW_TOKENS = 150
TEMPERATURE = 0.
TOP_K = 1
```

&nbsp;
#### 3) 重みのダウンロードと読み込み

これにより、上記のモデル選択に基づいて重みファイルが自動的にダウンロードされます：

```python
import os
import urllib.request

url = f"https://huggingface.co/rasbt/llama-3.2-from-scratch/resolve/main/{MODEL_FILE}"

if not os.path.exists(MODEL_FILE):
    urllib.request.urlretrieve(url, MODEL_FILE)
    print(f"Downloaded to {MODEL_FILE}")
```

モデル重みは以下のように読み込まれます：

```python
import torch
from llms_from_scratch.llama3 import Llama3Model

if "1B" in MODEL_FILE:
    from llms_from_scratch.llama3 import LLAMA32_CONFIG_1B as LLAMA32_CONFIG
elif "3B" in MODEL_FILE:
    from llms_from_scratch.llama3 import LLAMA32_CONFIG_3B as LLAMA32_CONFIG
else:
    raise ValueError("Incorrect model file name")

model = Llama3Model(LLAMA32_CONFIG)
model.load_state_dict(torch.load(MODEL_FILE, weights_only=True, map_location="cpu"))

device = (
    torch.device("cuda") if torch.cuda.is_available() else
    torch.device("mps") if torch.backends.mps.is_available() else
    torch.device("cpu")
)
model.to(device)
```

&nbsp;
#### 4) トークナイザーの初期化

以下のコードでトークナイザーをダウンロードして初期化します：

```python
from llms_from_scratch.llama3 import Llama3Tokenizer, ChatFormat, clean_text

TOKENIZER_FILE = "tokenizer.model"

url = f"https://huggingface.co/rasbt/llama-3.2-from-scratch/resolve/main/{TOKENIZER_FILE}"

if not os.path.exists(TOKENIZER_FILE):
    urllib.request.urlretrieve(url, TOKENIZER_FILE)
    print(f"Downloaded to {TOKENIZER_FILE}")
    
tokenizer = Llama3Tokenizer("tokenizer.model")

if "instruct" in MODEL_FILE:
    tokenizer = ChatFormat(tokenizer)
```

&nbsp;
#### 5) テキストの生成

最後に、以下のコードでテキストを生成できます：

```python
import time

from llms_from_scratch.ch05 import (
    generate,
    text_to_token_ids,
    token_ids_to_text
)

torch.manual_seed(123)

start = time.time()

token_ids = generate(
    model=model,
    idx=text_to_token_ids(PROMPT, tokenizer).to(device),
    max_new_tokens=MAX_NEW_TOKENS,
    context_size=LLAMA32_CONFIG["context_length"],
    top_k=TOP_K,
    temperature=TEMPERATURE
)

total_time = time.time() - start
print(f"Time: {total_time:.2f} sec")
print(f"{int(len(token_ids[0])/total_time)} tokens/sec")

if torch.cuda.is_available():
    max_mem_bytes = torch.cuda.max_memory_allocated()
    max_mem_gb = max_mem_bytes / (1024 ** 3)
    print(f"Max memory allocated: {max_mem_gb:.2f} GB")

output_text = token_ids_to_text(token_ids, tokenizer)

if "instruct" in MODEL_FILE:
    output_text = clean_text(output_text)

print("\n\nOutput text:\n\n", output_text)
```

Llama 3.2 1B Instructモデルを使用した場合、出力は以下のようになるはずです：

```
Time: 3.17 sec
50 tokens/sec
Max memory allocated: 2.91 GB


Output text:

 Llamas are herbivores, which means they primarily eat plants. Their diet consists mainly of:

1. Grasses: Llamas love to graze on various types of grasses, including tall grasses and grassy meadows.
2. Hay: Llamas also eat hay, which is a dry, compressed form of grass or other plants.
3. Alfalfa: Alfalfa is a legume that is commonly used as a hay substitute in llama feed.
4. Other plants: Llamas will also eat other plants, such as clover, dandelions, and wild grasses.

It's worth noting that the specific diet of llamas can vary depending on factors such as the breed,
```

&nbsp;
#### プロのヒント1：FlashAttentionで推論を高速化

`Llama3Model`の代わりに、`Llama3ModelFast`をドロップイン置換として使用できます。詳細については、[pkg/llms_from_scratch/llama3.py](../../pkg/llms_from_scratch/llama3.py)コードの調査をお勧めします。

`Llama3ModelFast`は、`GroupedQueryAttention`モジュールの自作スケールドドット積コードを、Ampere GPU以降で`FlashAttention`を使用するPyTorchの`scaled_dot_product`関数で置き換えます。

以下の表は、A100での性能比較を示しています：

|                 | Tokens/sec | Memory  |
| --------------- | ---------- | ------- |
| Llama3Model     | 42         | 2.91 GB |
| Llama3ModelFast | 54         | 2.91 GB |

&nbsp;
#### プロのヒント2：コンパイルで推論を高速化

最大4倍の高速化のために、

```python
model.to(device)
```

を以下に置き換えます：

```python
model = torch.compile(model)
model.to(device)
```

注意：コンパイル時に数分間の大きな初期コストがあり、高速化は最初の`generate`呼び出し後に有効になります。

以下の表は、後続の`generate`呼び出しでのA100での性能比較を示しています：

|                 | Tokens/sec | Memory  |
| --------------- | ---------- | ------- |
| Llama3Model     | 170        | 3.12 GB |
| Llama3ModelFast | 177        | 3.61 GB |

&nbsp;
#### プロのヒント3：コンパイルで推論を高速化

CPUでモデルを実行する際に、KVキャッシュ`Llama3Model`ドロップイン置換を使用して推論性能を大幅に向上させることができます。（KVキャッシュについて詳しくは、私の[LLMにおけるKVキャッシュをスクラッチから理解してコーディングする](https://magazine.sebastianraschka.com/p/coding-the-kv-cache-in-llms)記事を参照してください。）

```python
from llms_from_scratch.kv_cache.llama3 import Llama3Model
from llms_from_scratch.kv_cache.generate import generate_text_simple

model = Llama3Model(LLAMA32_CONFIG)
# ...
token_ids = generate_text_simple(
    model=model,
    idx=text_to_token_ids(PROMPT, tokenizer).to(device),
    max_new_tokens=MAX_NEW_TOKENS,
    context_size=LLAMA32_CONFIG["context_length"],
)
```

ピークメモリ使用量は計算が容易なNvidia CUDAデバイスのみに表示されていることに注意してください。ただし、他のデバイスでのメモリ使用量は、類似の精度形式を使用するため似ている可能性があり、KVキャッシュストレージにより、生成された150トークンテキストではここでもより低いメモリ使用量となります（ただし、異なるデバイスは行列乗算を異なって実装する可能性があり、異なるピークメモリ要件をもたらす可能性があります；KVキャッシュメモリはより長いコンテキスト長では法外に増加する可能性があります）。

| Model       | Mode              | Hardware        | Tokens/sec | GPU Memory (VRAM) |
| ----------- | ----------------- | --------------- | ---------- | ----------------- |
| Llama3Model | Regular           | Mac Mini M4 CPU | 1          | -                 |
| Llama3Model | Regular compiled  | Mac Mini M4 CPU | 1          | -                 |
| Llama3Model | KV cache          | Mac Mini M4 CPU | 68         | -                 |
| Llama3Model | KV cache compiled | Mac Mini M4 CPU | 86         | -                 |
|             |                   |                 |            |                   |
| Llama3Model | Regular           | Mac Mini M4 GPU | 15         | -                 |
| Llama3Model | Regular compiled  | Mac Mini M4 GPU | Error      | -                 |
| Llama3Model | KV cache          | Mac Mini M4 GPU | 62         | -                 |
| Llama3Model | KV cache compiled | Mac Mini M4 GPU | Error      | -                 |
|             |                   |                 |            |                   |
| Llama3Model | Regular           | Nvidia A100 GPU | 42         | 2.91 GB           |
| Llama3Model | Regular compiled  | Nvidia A100 GPU | 170        | 3.12 GB           |
| Llama3Model | KV cache          | Nvidia A100 GPU | 58         | 2.87 GB           |
| Llama3Model | KV cache compiled | Nvidia A100 GPU | 161        | 3.61 GB           |

上記のすべての設定は同じテキスト出力を生成することがテストされています。