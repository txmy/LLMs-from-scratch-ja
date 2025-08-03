# `llms-from-scratch` PyPIパッケージ

このオプションのPyPIパッケージを使用すると、*Build a Large Language Model From Scratch*（スクラッチから大規模言語モデルを構築）の本の各章のコードを便利にインポートできます。

&nbsp;
## インストール

&nbsp;
### PyPIから

公式の[Python Package Index](https://pypi.org/project/llms-from-scratch/) (PyPI)から`llms-from-scratch`パッケージをインストールします：

```bash
pip install llms-from-scratch
```

> **注意:** [`uv`](https://github.com/astral-sh/uv)を使用している場合は、`pip`を`uv pip`に置き換えるか、`uv add`を使用してください：

```bash
uv add llms-from-scratch
```



&nbsp;
### GitHubからの編集可能インストール

コードを変更して、開発中にその変更を反映させたい場合：

```bash
git clone https://github.com/rasbt/LLMs-from-scratch.git
cd LLMs-from-scratch
pip install -e .
```

> **注意:** `uv`の場合は以下を使用してください：

```bash
uv add --editable . --dev
```



&nbsp;
## パッケージの使用

インストール後、次のようにして任意の章からコードをインポートできます：

```python
from llms_from_scratch.ch02 import GPTDatasetV1, create_dataloader_v1

from llms_from_scratch.ch03 import (
    SelfAttention_v1,
    SelfAttention_v2,
    CausalAttention,
    MultiHeadAttentionWrapper,
    MultiHeadAttention,
    PyTorchMultiHeadAttention # ボーナス: PyTorchのscaled_dot_product_attentionを使用した高速版
)

from llms_from_scratch.ch04 import (
    LayerNorm,
    GELU,
    FeedForward,
    TransformerBlock,
    GPTModel,
    GPTModelFast # ボーナス: PyTorchのscaled_dot_product_attentionを使用した高速版
    generate_text_simple
)

from llms_from_scratch.ch05 import (
    generate,
    train_model_simple,
    evaluate_model,
    generate_and_print_sample,
    assign,
    load_weights_into_gpt,
    text_to_token_ids,
    token_ids_to_text,
    calc_loss_batch,
    calc_loss_loader,
    plot_losses,
    download_and_load_gpt2
)

from llms_from_scratch.ch06 import (
    download_and_unzip_spam_data,
    create_balanced_dataset,
    random_split,
    SpamDataset,
    calc_accuracy_loader,
    evaluate_model,
    train_classifier_simple,
    plot_values,
    classify_review
)

from llms_from_scratch.ch07 import (
    download_and_load_file,
    format_input,
    InstructionDataset,
    custom_collate_fn,
    check_if_running,
    query_model,
    generate_model_scores
)

	
from llms_from_scratch.appendix_a import NeuralNetwork, ToyDataset

from llms_from_scratch.appendix_d import find_highest_gradient, train_model
```



&nbsp;

### GPT-2 KVキャッシュ版（ボーナス素材）

```python
from llms_from_scratch.kv_cache.gpt2 import GPTModel
from llms_from_scratch.kv_cache.generate import generate_text_simple
```

KVキャッシュの詳細については、[KVキャッシュREADME](../../ch04/03_kv-cache)をご覧ください。



&nbsp;

### Llama 3（ボーナス素材）

```python
from llms_from_scratch.llama3 import (
    Llama3Model,
    Llama3ModelFast,
    Llama3Tokenizer,
    ChatFormat,
    clean_text
)

# KVキャッシュのドロップイン置換
from llms_from_scratch.kv_cache.llama3 import Llama3Model
from llms_from_scratch.kv_cache.generate import generate_text_simple
```

`llms_from_scratch.llama3`の使用方法については、[このボーナスセクション](../../ch05/07_gpt_to_llama/README.md)をご覧ください。

KVキャッシュの詳細については、[KVキャッシュREADME](../../ch04/03_kv-cache)をご覧ください。


&nbsp;
### Qwen3（ボーナス素材）

```python
from llms_from_scratch.qwen3 import (
    Qwen3Model,
    Qwen3Tokenizer,
)

# KVキャッシュのドロップイン置換
from llms_from_scratch.kv_cache.qwen3 import Qwen3Model
from llms_from_scratch.kv_cache.generate import (
    generate_text_simple,
    generate_text_simple_stream
)

# バッチ推論サポート付きKVキャッシュのドロップイン置換
from llms_from_scratch.kv_cache_batched.generate import (
    generate_text_simple,
    generate_text_simple_stream
)
from llms_from_scratch.kv_cache_batched.qwen3 import Qwen3Model
```

`llms_from_scratch.qwen3`の使用方法については、[このボーナスセクション](../../ch05/11_qwen3/README.md)をご覧ください。

KVキャッシュの詳細については、[KVキャッシュREADME](../../ch04/03_kv-cache)をご覧ください。