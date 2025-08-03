※このリポジトリは [rasbt
LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch)を機械翻訳したものです。

# 大規模言語モデルを作る（ゼロから）

このリポジトリには、GPT ライクな LLM の開発、事前学習、ファインチューニングのためのコードが含まれており、書籍[大規模言語モデルを作る（ゼロから）](https://amzn.to/4fqvn0D)の公式コードリポジトリです。

<br>
<br>

<a href="https://amzn.to/4fqvn0D"><img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/cover.jpg?123" width="250px"></a>

<br>

[_大規模言語モデルを作る（ゼロから）_](http://mng.bz/orYv)では、大規模言語モデル（LLM）を一からコーディングすることで、その内部動作を学習し理解します。本書では、明確なテキスト、図、例を使って各段階を説明しながら、独自の LLM の作成方法をガイドします。

本書で説明されている、教育目的で独自の小規模ながら機能的なモデルを訓練・開発する方法は、ChatGPT の背後にあるような大規模な基盤モデルの作成に使用されるアプローチを反映しています。さらに、本書にはファインチューニング用により大きな事前学習済みモデルの重みを読み込むコードも含まれています。

- 公式[ソースコードリポジトリ](https://github.com/rasbt/LLMs-from-scratch)へのリンク
- [Manning（出版社ウェブサイト）の書籍ページへのリンク](http://mng.bz/orYv)
- [Amazon.com の書籍ページへのリンク](https://www.amazon.com/gp/product/1633437167)
- ISBN 9781633437166

<a href="http://mng.bz/orYv#reviews"><img src="https://sebastianraschka.com//images/LLMs-from-scratch-images/other/reviews.png" width="220px"></a>

<br>
<br>

このリポジトリのコピーをダウンロードするには、[Download ZIP](https://github.com/rasbt/LLMs-from-scratch/archive/refs/heads/main.zip)ボタンをクリックするか、ターミナルで以下のコマンドを実行してください：

```bash
git clone --depth 1 https://github.com/rasbt/LLMs-from-scratch.git
```

<br>

（Manning ウェブサイトからコードバンドルをダウンロードした場合は、最新のアップデートのために[https://github.com/rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch)の GitHub 上の公式コードリポジトリを訪問することをご検討ください。）

<br>
<br>

# 目次

この`README.md`ファイルは Markdown（`.md`）ファイルであることにご注意ください。Manning ウェブサイトからこのコードバンドルをダウンロードしてローカルコンピュータで表示している場合は、適切に表示するために Markdown エディタまたはプレビューアの使用をお勧めします。まだ Markdown エディタをインストールしていない場合、[Ghostwriter](https://ghostwriter.kde.org)は良い無料オプションです。

または、ブラウザで[https://github.com/rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch)の GitHub でこのファイルや他のファイルを表示することもできます。GitHub は自動的に Markdown をレンダリングします。

<br>
<br>

> **ヒント：**
> Python と python パッケージのインストール、コード環境のセットアップに関するガイダンスをお探しの場合は、[setup](setup)ディレクトリにある[README.md](setup/README.md)ファイルを読むことをお勧めします。

<br>
<br>

[![Code tests Linux](https://github.com/rasbt/LLMs-from-scratch/actions/workflows/basic-tests-linux-uv.yml/badge.svg)](https://github.com/rasbt/LLMs-from-scratch/actions/workflows/basic-tests-linux-uv.yml)
[![Code tests Windows](https://github.com/rasbt/LLMs-from-scratch/actions/workflows/basic-tests-windows-uv-pip.yml/badge.svg)](https://github.com/rasbt/LLMs-from-scratch/actions/workflows/basic-tests-windows-uv-pip.yml)
[![Code tests macOS](https://github.com/rasbt/LLMs-from-scratch/actions/workflows/basic-tests-macos-uv.yml/badge.svg)](https://github.com/rasbt/LLMs-from-scratch/actions/workflows/basic-tests-macos-uv.yml)

<br>

| 章タイトル                                                | メインコード（クイックアクセス用）                                                                                                                                                                                                                                                                                              | 全コード + 補足              |
| --------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| [セットアップ推奨事項](setup)                             | -                                                                                                                                                                                                                                                                                                                               | -                            |
| 第 1 章: 大規模言語モデルを理解する                       | コードなし                                                                                                                                                                                                                                                                                                                      | -                            |
| 第 2 章: テキストデータを扱う                             | - [ch02.ipynb](ch02/01_main-chapter-code/ch02.ipynb)<br/>- [dataloader.ipynb](ch02/01_main-chapter-code/dataloader.ipynb) (要約)<br/>- [exercise-solutions.ipynb](ch02/01_main-chapter-code/exercise-solutions.ipynb)                                                                                                           | [./ch02](./ch02)             |
| 第 3 章: アテンション機構をコーディングする               | - [ch03.ipynb](ch03/01_main-chapter-code/ch03.ipynb)<br/>- [multihead-attention.ipynb](ch03/01_main-chapter-code/multihead-attention.ipynb) (要約) <br/>- [exercise-solutions.ipynb](ch03/01_main-chapter-code/exercise-solutions.ipynb)                                                                                        | [./ch03](./ch03)             |
| 第 4 章: GPT モデルをゼロから実装する                     | - [ch04.ipynb](ch04/01_main-chapter-code/ch04.ipynb)<br/>- [gpt.py](ch04/01_main-chapter-code/gpt.py) (要約)<br/>- [exercise-solutions.ipynb](ch04/01_main-chapter-code/exercise-solutions.ipynb)                                                                                                                               | [./ch04](./ch04)             |
| 第 5 章: ラベルなしデータで事前学習する                   | - [ch05.ipynb](ch05/01_main-chapter-code/ch05.ipynb)<br/>- [gpt_train.py](ch05/01_main-chapter-code/gpt_train.py) (要約) <br/>- [gpt_generate.py](ch05/01_main-chapter-code/gpt_generate.py) (要約) <br/>- [exercise-solutions.ipynb](ch05/01_main-chapter-code/exercise-solutions.ipynb)                                       | [./ch05](./ch05)             |
| 第 6 章: テキスト分類のためのファインチューニング         | - [ch06.ipynb](ch06/01_main-chapter-code/ch06.ipynb) <br/>- [gpt_class_finetune.py](ch06/01_main-chapter-code/gpt_class_finetune.py) <br/>- [exercise-solutions.ipynb](ch06/01_main-chapter-code/exercise-solutions.ipynb)                                                                                                      | [./ch06](./ch06)             |
| 第 7 章: 指示に従うためのファインチューニング             | - [ch07.ipynb](ch07/01_main-chapter-code/ch07.ipynb)<br/>- [gpt_instruction_finetuning.py](ch07/01_main-chapter-code/gpt_instruction_finetuning.py) (要約)<br/>- [ollama_evaluate.py](ch07/01_main-chapter-code/ollama_evaluate.py) (要約)<br/>- [exercise-solutions.ipynb](ch07/01_main-chapter-code/exercise-solutions.ipynb) | [./ch07](./ch07)             |
| 付録 A: PyTorch 入門                                      | - [code-part1.ipynb](appendix-A/01_main-chapter-code/code-part1.ipynb)<br/>- [code-part2.ipynb](appendix-A/01_main-chapter-code/code-part2.ipynb)<br/>- [DDP-script.py](appendix-A/01_main-chapter-code/DDP-script.py)<br/>- [exercise-solutions.ipynb](appendix-A/01_main-chapter-code/exercise-solutions.ipynb)               | [./appendix-A](./appendix-A) |
| 付録 B: 参考文献とさらなる読み物                          | コードなし                                                                                                                                                                                                                                                                                                                      | -                            |
| 付録 C: 演習の解答                                        | コードなし                                                                                                                                                                                                                                                                                                                      | -                            |
| 付録 D: トレーニングループに機能を追加する                | - [appendix-D.ipynb](appendix-D/01_main-chapter-code/appendix-D.ipynb)                                                                                                                                                                                                                                                          | [./appendix-D](./appendix-D) |
| 付録 E: LoRA によるパラメータ効率的なファインチューニング | - [appendix-E.ipynb](appendix-E/01_main-chapter-code/appendix-E.ipynb)                                                                                                                                                                                                                                                          | [./appendix-E](./appendix-E) |

<br>
&nbsp;

以下のメンタルモデルは、本書で扱う内容を要約しています。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/mental-model.jpg" width="650px">

<br>
&nbsp;

## 前提条件

最も重要な前提条件は、Python プログラミングの強固な基盤です。
この知識があれば、LLM の魅力的な世界を探索し、本書で提示される概念とコード例を理解する準備が整います。

深層ニューラルネットワークの経験がある場合、LLM はこれらのアーキテクチャに基づいて構築されているため、特定の概念がより馴染みやすいかもしれません。

本書では、外部の LLM ライブラリを使用せずに、PyTorch を使用してコードをゼロから実装します。PyTorch の習熟は前提条件ではありませんが、PyTorch の基本に精通していることは確かに有用です。PyTorch が初めての場合、付録 A が PyTorch の簡潔な紹介を提供します。また、[PyTorch in One Hour: From Tensors to Training Neural Networks on Multiple GPUs](https://sebastianraschka.com/teaching/pytorch-1h/)という私の本も、基本を学ぶのに役立つかもしれません。

<br>
&nbsp;

## ハードウェア要件

本書の主要な章のコードは、通常のラップトップで妥当な時間内に実行できるように設計されており、専門的なハードウェアを必要としません。このアプローチにより、幅広い読者が教材に取り組むことができます。さらに、コードは利用可能な場合、自動的に GPU を使用します。（追加の推奨事項については、[setup](https://github.com/rasbt/LLMs-from-scratch/blob/main/setup/README.md)ドキュメントをご覧ください。）

&nbsp;

## ビデオコース

[17 時間 15 分のコンパニオンビデオコース](https://www.manning.com/livevideo/master-and-build-large-language-models)では、本書の各章をコーディングで進めていきます。コースは本書の構造を反映した章とセクションに編成されているため、本書の独立した代替として、または補完的なコードアロングリソースとして使用できます。

<a href="https://www.manning.com/livevideo/master-and-build-large-language-models"><img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/video-screenshot.webp?123" width="350px"></a>

&nbsp;

## 演習

本書の各章にはいくつかの演習が含まれています。解答は付録 C にまとめられており、対応するコードノートブックはこのリポジトリの主要な章フォルダで利用できます（例：[./ch02/01_main-chapter-code/exercise-solutions.ipynb](./ch02/01_main-chapter-code/exercise-solutions.ipynb)）。

コード演習に加えて、Manning ウェブサイトから 170 ページの無料 PDF [Test Yourself On Build a Large Language Model (From Scratch)](https://www.manning.com/books/test-yourself-on-build-a-large-language-model-from-scratch)をダウンロードできます。各章約 30 のクイズ問題と解答が含まれており、理解度をテストするのに役立ちます。

<a href="https://www.manning.com/books/test-yourself-on-build-a-large-language-model-from-scratch"><img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/test-yourself-cover.jpg?123" width="150px"></a>

&nbsp;

## ボーナス教材

いくつかのフォルダには、関心のある読者のためのボーナスとしてオプションの教材が含まれています：

- **セットアップ**
  - [Python セットアップのヒント](setup/01_optional-python-setup-preferences)
  - [本書で使用される Python パッケージとライブラリのインストール](setup/02_installing-python-libraries)
  - [Docker 環境セットアップガイド](setup/03_optional-docker-environment)
- **第 2 章: テキストデータを扱う**
  - [バイトペアエンコーディング（BPE）トークナイザをゼロから](ch02/05_bpe-from-scratch/bpe-from-scratch.ipynb)
  - [さまざまなバイトペアエンコーディング（BPE）実装の比較](ch02/02_bonus_bytepair-encoder)
  - [埋め込み層と線形層の違いを理解する](ch02/03_bonus_embedding-vs-matmul)
  - [シンプルな数値でデータローダーの直感を理解](ch02/04_bonus_dataloader-intuition)
- **第 3 章: アテンション機構をコーディングする**
  - [効率的なマルチヘッドアテンション実装の比較](ch03/02_bonus_efficient-multihead-attention/mha-implementations.ipynb)
  - [PyTorch バッファを理解する](ch03/03_understanding-buffers/understanding-buffers.ipynb)
- **第 4 章: GPT モデルをゼロから実装する**
  - [FLOPS 分析](ch04/02_performance-analysis/flops-analysis.ipynb)
  - [KV キャッシュ](ch04/03_kv-cache)
- **第 5 章: ラベルなしデータで事前学習する：**
  - [代替の重みローディング方法](ch05/02_alternative_weight_loading/)
  - [プロジェクト・グーテンベルクデータセットで GPT を事前学習する](ch05/03_bonus_pretraining_on_gutenberg)
  - [トレーニングループに機能を追加する](ch05/04_learning_rate_schedulers)
  - [事前学習のためのハイパーパラメータを最適化する](ch05/05_bonus_hparam_tuning)
  - [事前学習済み LLM とやり取りするユーザーインターフェースを構築する](ch05/06_user_interface)
  - [GPT を Llama に変換する](ch05/07_gpt_to_llama)
  - [Llama 3.2 をゼロから](ch05/07_gpt_to_llama/standalone-llama32.ipynb)
  - [Qwen3 Dense と Mixture-of-Experts（MoE）をゼロから](ch05/11_qwen3/)
  - [メモリ効率的なモデル重みのローディング](ch05/08_memory_efficient_weight_loading/memory-efficient-state-dict.ipynb)
  - [Tiktoken BPE トークナイザを新しいトークンで拡張する](ch05/09_extending-tokenizers/extend-tiktoken.ipynb)
  - [より高速な LLM トレーニングのための PyTorch パフォーマンスのヒント](ch05/10_llm-training-speed)
- **第 6 章: 分類のためのファインチューニング**
  - [異なる層のファインチューニングとより大きなモデルの使用に関する追加実験](ch06/02_bonus_additional-experiments)
  - [50k IMDB ムービーレビューデータセットで異なるモデルをファインチューニング](ch06/03_bonus_imdb-classification)
  - [GPT ベースのスパム分類器とやり取りするユーザーインターフェースを構築する](ch06/04_user_interface)
- **第 7 章: 指示に従うためのファインチューニング**
  - [ほぼ重複を見つけて受動態エントリを作成するためのデータセットユーティリティ](ch07/02_dataset-utilities)
  - [OpenAI API と Ollama を使用して指示応答を評価する](ch07/03_model-evaluation)
  - [指示ファインチューニング用のデータセットを生成する](ch07/05_dataset-generation/llama3-ollama.ipynb)
  - [指示ファインチューニング用のデータセットを改善する](ch07/05_dataset-generation/reflection-gpt4.ipynb)
  - [Llama 3.1 70B と Ollama で優先データセットを生成する](ch07/04_preference-tuning-with-dpo/create-preference-data-ollama.ipynb)
  - [LLM アライメントのための直接選好最適化（DPO）](ch07/04_preference-tuning-with-dpo/dpo-from-scratch.ipynb)
  - [指示ファインチューニング済み GPT モデルとやり取りするユーザーインターフェースを構築する](ch07/06_user_interface)

<br>
&nbsp;

## 質問、フィードバック、およびこのリポジトリへの貢献

あらゆる種類のフィードバックを歓迎します。[Manning Forum](https://livebook.manning.com/forum?product=raschka&page=1)または[GitHub Discussions](https://github.com/rasbt/LLMs-from-scratch/discussions)で共有することをお勧めします。同様に、質問がある場合や、単に他の人とアイデアを交換したい場合は、遠慮なくフォーラムに投稿してください。

このリポジトリは印刷された本に対応するコードを含んでいるため、現在、主要な章のコードの内容を拡張する貢献は受け付けることができません。物理的な本からの逸脱が生じてしまうためです。一貫性を保つことで、すべての人にとってスムーズな体験を確保できます。

&nbsp;

## 引用

本書またはコードがあなたの研究に役立つ場合は、引用をご検討ください。

シカゴスタイルの引用：

> Raschka, Sebastian. _Build A Large Language Model (From Scratch)_. Manning, 2024. ISBN: 978-1633437166.

BibTeX エントリ：

```
@book{build-llms-from-scratch-book,
  author       = {Sebastian Raschka},
  title        = {Build A Large Language Model (From Scratch)},
  publisher    = {Manning},
  year         = {2024},
  isbn         = {978-1633437166},
  url          = {https://www.manning.com/books/build-a-large-language-model-from-scratch},
  github       = {https://github.com/rasbt/LLMs-from-scratch}
}
```
