# Qwen3をスクラッチから

このフォルダの[standalone-qwen3.ipynb](standalone-qwen3.ipynb) Jupyterノートブックには、Qwen3 0.6B、1.7B、4B、8B、32Bのスクラッチ実装が含まれています。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/qwen/qwen-overview.webp">

この[standalone-qwen3-moe.ipynb](standalone-qwen3-moe.ipynb)と[standalone-qwen3-moe-plus-kvcache.ipynb](standalone-qwen3-moe-plus-kvcache.ipynb) Jupyterノートブックには、Thinking、Instruct、Coderモデルバリアントを含む30B-A3B Mixture-of-Experts（MoE）のスクラッチ実装が含まれています。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/qwen/qwen3-coder-flash-overview.webp?123" width="430px">

&nbsp;
# `llms-from-scratch`パッケージを介したQwen3の使用

Qwen3のスクラッチ実装を簡単に使用するために、[pkg/llms_from_scratch](../../pkg/llms_from_scratch)のこのリポジトリのソースコードに基づく`llms-from-scratch` PyPIパッケージも使用できます。

&nbsp;
#### 1) インストール

```bash
pip install llms_from_scratch tokenizers
```

&nbsp;
#### 2) モデルとテキスト生成設定

使用するモデルを指定します：

```python
USE_REASONING_MODEL = False  # ベースモデル
USE_REASONING_MODEL = True   # "thinking"モデル


# 以下も使用可能
# USE_REASONING_MODEL = True
# Qwen3 Coder Flashモデルも同様
```

ユーザーが定義できる基本的なテキスト生成設定。150トークンで、0.6Bモデルは約1.5 GBのメモリが必要です。

```python
MAX_NEW_TOKENS = 150
TEMPERATURE = 0.
TOP_K = 1
```

&nbsp;
#### 3a) 0.6Bモデルの重みダウンロードと読み込み

以下は、上記のモデル選択（reasoningまたはbase）に基づいて重みファイルを自動的にダウンロードします。このセクションは0.6Bモデルに焦点を当てています。より大きなモデル（1.7B、4B、8B、または32B）を使用したい場合は、このセクションをスキップしてセクション3b)に進んでください。

```python
from llms_from_scratch.qwen3 import download_from_huggingface

repo_id = "rasbt/qwen3-from-scratch"

if USE_REASONING_MODEL:
    filename = "qwen3-0.6B.pth"
    local_dir = "Qwen3-0.6B"    
else:
    filename = "qwen3-0.6B-base.pth"   
    local_dir = "Qwen3-0.6B-Base"

download_from_huggingface(
    repo_id=repo_id,
    filename=filename,
    local_dir=local_dir
)
```

モデル重みは以下のように読み込まれます：

```python
from pathlib import Path
import torch

from llms_from_scratch.qwen3 import Qwen3Model, QWEN_CONFIG_06_B

model_file = Path(local_dir) / filename

model = Qwen3Model(QWEN_CONFIG_06_B)
model.load_state_dict(torch.load(model_file, weights_only=True, map_location="cpu"))

device = (
    torch.device("cuda") if torch.cuda.is_available() else
    torch.device("mps") if torch.backends.mps.is_available() else
    torch.device("cpu")
)
model.to(device);
```

&nbsp;
#### 3b) より大きなQwenモデルの重みダウンロードと読み込み

より大きなQwenモデル、例えば1.7B、4B、8B、または32Bを使用したい場合は、3a)のコードの代わりに以下のコードを使用してください。これには追加のコード依存関係が必要です：

```bash
pip install safetensors huggingface_hub
```

次に以下のコードを使用します（適切に変更して`USE_MODEL`を選択して希望するモデルサイズを選択）：

```python
USE_MODEL = "1.7B"

if USE_MODEL == "1.7B":
    from llms_from_scratch.qwen3 import QWEN3_CONFIG_1_7B as QWEN3_CONFIG
elif USE_MODEL == "4B":
    from llms_from_scratch.qwen3 import QWEN3_CONFIG_4B as QWEN3_CONFIG
elif USE_MODEL == "8B":
    from llms_from_scratch.qwen3 import QWEN3_CONFIG_8B as QWEN3_CONFIG
elif USE_MODEL == "14B":
    from llms_from_scratch.qwen3 import QWEN3_CONFIG_14B as QWEN3_CONFIG
elif USE_MODEL == "32B":
    from llms_from_scratch.qwen3 import QWEN3_CONFIG_32B as QWEN3_CONFIG
elif USE_MODEL == "30B-A3B":
    from llms_from_scratch.qwen3 import QWEN3_CONFIG_30B_A3B as QWEN3_CONFIG
else:
    raise ValueError("Invalid USE_MODEL name.")
    
repo_id = f"Qwen/Qwen3-{USE_MODEL}"
local_dir = f"Qwen3-{USE_MODEL}"

if not USE_REASONING_MODEL:
  repo_id = f"{repo_id}-Base"
  local_dir = f"{local_dir}-Base"
```

次に、重みを`model`にダウンロードして読み込みます：

```python
from llms_from_scratch.qwen3 import (
    Qwen3Model,
    download_from_huggingface_from_snapshots,
    load_weights_into_qwen
)

device = (
    torch.device("cuda") if torch.cuda.is_available() else
    torch.device("mps") if torch.backends.mps.is_available() else
    torch.device("cpu")
)

with device:
    model = Qwen3Model(QWEN3_CONFIG)

weights_dict = download_from_huggingface_from_snapshots(
    repo_id=repo_id,
    local_dir=local_dir
)
load_weights_into_qwen(model, QWEN3_CONFIG, weights_dict)
model.to(device)  # MoEモデルのみ必要
del weights_dict  # ディスク容量を解放するために重み辞書を削除
```

&nbsp;

#### 4) トークナイザーの初期化

以下のコードでトークナイザーをダウンロードして初期化します：

```python
from llms_from_scratch.qwen3 import Qwen3Tokenizer

if USE_REASONING_MODEL:
    tok_filename = "tokenizer.json"    
else:
    tok_filename = "tokenizer-base.json"   

tokenizer = Qwen3Tokenizer(
    tokenizer_file_path=tok_filename,
    repo_id=repo_id,
    add_generation_prompt=USE_REASONING_MODEL,
    add_thinking=USE_REASONING_MODEL
)
```

&nbsp;

#### 5) テキストの生成

最後に、以下のコードでテキストを生成できます：

```python
prompt = "Give me a short introduction to large language models."
input_token_ids = tokenizer.encode(prompt)
```

```python
from llms_from_scratch.ch05 import generate
import time

torch.manual_seed(123)

start = time.time()

output_token_ids = generate(
    model=model,
    idx=torch.tensor(input_token_ids, device=device).unsqueeze(0),
    max_new_tokens=150,
    context_size=QWEN_CONFIG_06_B["context_length"],
    top_k=1,
    temperature=0.
)

total_time = time.time() - start
print(f"Time: {total_time:.2f} sec")
print(f"{int(len(output_token_ids[0])/total_time)} tokens/sec")

if torch.cuda.is_available():
    max_mem_bytes = torch.cuda.max_memory_allocated()
    max_mem_gb = max_mem_bytes / (1024 ** 3)
    print(f"Max memory allocated: {max_mem_gb:.2f} GB")

output_text = tokenizer.decode(output_token_ids.squeeze(0).tolist())

print("\n\nOutput text:\n\n", output_text + "...")
```

Qwen3 0.6B reasoningモデルを使用した場合、出力は以下のようになるはずです（これはA100で実行されました）：

```
Time: 6.35 sec
25 tokens/sec
Max memory allocated: 1.49 GB


Output text:

 <|im_start|>user
Give me a short introduction to large language models.<|im_end|>
Large language models (LLMs) are advanced artificial intelligence systems designed to generate human-like text. They are trained on vast amounts of text data, allowing them to understand and generate coherent, contextually relevant responses. LLMs are used in a variety of applications, including chatbots, virtual assistants, content generation, and more. They are powered by deep learning algorithms and can be fine-tuned for specific tasks, making them versatile tools for a wide range of industries.<|endoftext|>Human resources department of a company is planning to hire 100 new employees. The company has a budget of $100,000 for the recruitment process. The company has a minimum wage of $10 per hour. The company has a total of...
```

より大きなモデルの場合、各トークンが生成されるとすぐに印刷するストリーミングバリアントを好むかもしれません：

```python
from llms_from_scratch.generate import generate_text_simple_stream

input_token_ids_tensor = torch.tensor(input_token_ids, device=device).unsqueeze(0)

for token in generate_text_simple_stream(
    model=model,
    token_ids=input_token_ids_tensor,
    max_new_tokens=150,
    eos_token_id=tokenizer.eos_token_id
):
    token_id = token.squeeze(0).tolist()
    print(
        tokenizer.decode(token_id),
        end="",
        flush=True
    )
```

```
 <|im_start|>user
Give me a short introduction to large language models.<|im_end|>
Large language models (LLMs) are advanced artificial intelligence systems designed to generate human-like text. They are trained on vast amounts of text data, allowing them to understand and generate coherent, contextually relevant responses. LLMs are used in a variety of applications, including chatbots, virtual assistants, content generation, and more. They are powered by deep learning algorithms and can be fine-tuned for specific tasks, making them versatile tools for a wide range of industries.<|endoftext|>Human resources department of a company is planning to hire 100 new employees. The company has a budget of $100,000 for the recruitment process. The company has a minimum wage of $10 per hour. The company has a total of...
```

&nbsp;

#### プロのヒント1：コンパイルで推論を高速化

最大4倍の高速化のために、

```python
model.to(device)
```

を以下に置き換えます：

```python
model.to(device)
model = torch.compile(model)
```

注意：コンパイル時に数分間の大きな初期コストがあり、高速化は最初の`generate`呼び出し後に有効になります。

以下の表は、後続の`generate`呼び出しでのA100での性能比較を示しています：

|                          | Hardware        | Tokens/sec | Memory   |
| ------------------------ | ----------------|----------- | -------- |
| Qwen3Model 0.6B          | Nvidia A100 GPU | 25         | 1.49 GB  |
| Qwen3Model 0.6B compiled | Nvidia A100 GPU | 107        | 1.99 GB  |

&nbsp;
#### プロのヒント2：KVキャッシュで推論を高速化

CPUでモデルを実行する際に、KVキャッシュ`Qwen3Model`ドロップイン置換を使用して推論性能を大幅に向上させることができます。（KVキャッシュについて詳しくは、私の[LLMにおけるKVキャッシュをスクラッチから理解してコーディングする](https://magazine.sebastianraschka.com/p/coding-the-kv-cache-in-llms)記事を参照してください。）

```python
from llms_from_scratch.kv_cache.qwen3 import Qwen3Model
from llms_from_scratch.kv_cache.generate import generate_text_simple

model = Qwen3Model(QWEN_CONFIG_06_B)
# ...
token_ids = generate_text_simple(
    model=model,
    idx=text_to_token_ids(PROMPT, tokenizer).to(device),
    max_new_tokens=MAX_NEW_TOKENS,
    context_size=QWEN_CONFIG_06_B["context_length"],
)
```

ピークメモリ使用量は計算が容易なNvidia CUDAデバイスのみに表示されていることに注意してください。ただし、他のデバイスでのメモリ使用量は、類似の精度形式を使用するため似ている可能性があり、KVキャッシュストレージにより、生成された150トークンテキストではここでもより低いメモリ使用量となります（ただし、異なるデバイスは行列乗算を異なって実装する可能性があり、異なるピークメモリ要件をもたらす可能性があります；KVキャッシュメモリはより長いコンテキスト長では法外に増加する可能性があります）。

| Model           | Mode              | Hardware        | Tokens/sec | GPU Memory (VRAM) |
| --------------- | ----------------- | --------------- | ---------- | ----------------- |
| Qwen3Model 0.6B | Regular           | Mac Mini M4 CPU | 1          | -                 |
| Qwen3Model 0.6B | Regular compiled  | Mac Mini M4 CPU | 1          | -                 |
| Qwen3Model 0.6B | KV cache          | Mac Mini M4 CPU | 80         | -                 |
| Qwen3Model 0.6B | KV cache compiled | Mac Mini M4 CPU | 137        | -                 |
|                 |                   |                 |            |                   |
| Qwen3Model 0.6B | Regular           | Mac Mini M4 GPU | 21         | -                 |
| Qwen3Model 0.6B | Regular compiled  | Mac Mini M4 GPU | Error      | -                 |
| Qwen3Model 0.6B | KV cache          | Mac Mini M4 GPU | 28         | -                 |
| Qwen3Model 0.6B | KV cache compiled | Mac Mini M4 GPU | Error      | -                 |
|                 |                   |                 |            |                   |
| Qwen3Model 0.6B | Regular           | Nvidia A100 GPU | 26         | 1.49 GB           |
| Qwen3Model 0.6B | Regular compiled  | Nvidia A100 GPU | 107        | 1.99 GB           |
| Qwen3Model 0.6B | KV cache          | Nvidia A100 GPU | 25         | 1.47 GB           |
| Qwen3Model 0.6B | KV cache compiled | Nvidia A100 GPU | 90         | 1.48 GB           |

上記のすべての設定は同じテキスト出力を生成することがテストされています。

&nbsp;

#### プロのヒント3：バッチ推論

バッチ推論を介してスループットをさらに向上させることができます。これは同等の比較ではありませんが、入力シーケンスの数が多くなると、メモリ使用量の増加とのトレードオフでトークン/秒のスループットが向上します。

これには、プロンプトの準備に関して小さなコード修正が必要です。例えば、以下のバッチプロンプトを考えてください：

```python
from llms_from_scratch.ch04 import generate_text_simple
from llms_from_scratch.qwen3 import Qwen3Model, QWEN_CONFIG_06_B
# ...

prompts = [
    "Give me a short introduction to neural networks.",
    "Give me a short introduction to machine learning.",
    "Give me a short introduction to deep learning models.",
    "Give me a short introduction to natural language processing.",
    "Give me a short introduction to generative AI systems.",
    "Give me a short introduction to transformer architectures.",
    "Give me a short introduction to supervised learning methods.",
    "Give me a short introduction to unsupervised learning.",
]

tokenized_prompts = [tokenizer.encode(p) for p in prompts]
max_len = max(len(t) for t in tokenized_prompts)
padded_token_ids = [
    t + [tokenizer.pad_token_id] * (max_len - len(t)) for t in tokenized_prompts
]
input_tensor = torch.tensor(padded_token_ids).to(device)

output_token_ids = generate_text_simple(
    model=model,
    idx=input_tensor,
    max_new_tokens=150,
    context_size=QWEN_CONFIG_06_B["context_length"],
)
```

KVキャッシュバージョンのコードは類似していますが、これらのドロップイン置換を使用する必要があります：

```python
from llms_from_scratch.kv_cache_batched.generate import generate_text_simple
from llms_from_scratch.kv_cache_batched.qwen3 import Qwen3Model
```

以下の実験はバッチサイズ8で実行されています。

| Model            | Mode              | Hardware        | Batch size | Tokens/sec | GPU Memory (VRAM) |
| ---------------- | ----------------- | --------------- | ---------- | ---------- | ----------------- |
| Qwen3Model  0.6B | Regular           | Mac Mini M4 CPU | 8          | 2          | -                 |
| Qwen3Model 0.6B  | Regular compiled  | Mac Mini M4 CPU | 8          | -          | -                 |
| Qwen3Model 0.6B  | KV cache          | Mac Mini M4 CPU | 8          | 92         | -                 |
| Qwen3Model 0.6B  | KV cache compiled | Mac Mini M4 CPU | 8          | 128        | -                 |
|                  |                   |                 |            |            |                   |
| Qwen3Model 0.6B  | Regular           | Mac Mini M4 GPU | 8          | 36         | -                 |
| Qwen3Model 0.6B  | Regular compiled  | Mac Mini M4 GPU | 8          | -          | -                 |
| Qwen3Model 0.6B  | KV cache          | Mac Mini M4 GPU | 8          | 61         | -                 |
| Qwen3Model 0.6B  | KV cache compiled | Mac Mini M4 GPU | 8          | -          | -                 |
|                  |                   |                 |            |            |                   |
| Qwen3Model 0.6B  | Regular           | Nvidia A100 GPU | 8          | 184        | 2.19 GB           |
| Qwen3Model 0.6B  | Regular compiled  | Nvidia A100 GPU | 8          | 351        | 2.19 GB           |
| Qwen3Model 0.6B  | KV cache          | Nvidia A100 GPU | 8          | 140        | 3.13 GB           |
| Qwen3Model 0.6B  | KV cache compiled | Nvidia A100 GPU | 8          | 280        | 1.75 GB           |