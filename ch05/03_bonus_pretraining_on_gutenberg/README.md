# Project GutenbergデータセットでのGPT事前学習

このディレクトリのコードには、Project Gutenbergが提供する無料の書籍で小さなGPTモデルを学習するためのコードが含まれています。

Project Gutenbergのウェブサイトに記載されているように、「Project Gutenberg電子書籍の大部分は米国では公有に属している」です。

Project Gutenbergが提供するリソースの使用について詳しくは、[Project Gutenberg許可、ライセンス、その他の一般的な要求](https://www.gutenberg.org/policy/permission.html)ページをお読みください。

&nbsp;
## このコードの使用方法

&nbsp;

### 1) データセットのダウンロード

このセクションでは、[`pgcorpus/gutenberg`](https://github.com/pgcorpus/gutenberg) GitHubリポジトリのコードを使用してProject Gutenbergから書籍をダウンロードします。

この記事の執筆時点では、これには約50 GBのディスク容量が必要で、約10〜15時間かかりますが、それ以降のProject Gutenbergの成長によってはより多くの時間がかかる可能性があります。

&nbsp;
#### LinuxおよびmacOSユーザー向けのダウンロード手順

LinuxおよびmacOSユーザーは、これらの手順に従ってデータセットをダウンロードできます（Windowsユーザーの場合は、下記の注記をご覧ください）：

1. `03_bonus_pretraining_on_gutenberg`フォルダを作業ディレクトリとして設定し、このフォルダ内に`gutenberg`リポジトリをローカルにクローンします（これは提供されたスクリプト`prepare_dataset.py`と`pretraining_simple.py`を実行するために必要です）。例えば、`LLMs-from-scratch`リポジトリのフォルダにいる場合、以下のコマンドで*03_bonus_pretraining_on_gutenberg*フォルダに移動します：
```bash
cd ch05/03_bonus_pretraining_on_gutenberg
```

2. そこに`gutenberg`リポジトリをクローンします：
```bash
git clone https://github.com/pgcorpus/gutenberg.git
```

3. ローカルにクローンした`gutenberg`リポジトリのフォルダに移動します：
```bash
cd gutenberg
```

4. `gutenberg`リポジトリのフォルダから*requirements.txt*に定義されている必要なパッケージをインストールします：
```bash
pip install -r requirements.txt
```

5. データをダウンロードします：
```bash
python get_data.py
```

6. `03_bonus_pretraining_on_gutenberg`フォルダに戻ります：
```bash
cd ..
```

&nbsp;
#### Windowsユーザー向けの特別な手順

[`pgcorpus/gutenberg`](https://github.com/pgcorpus/gutenberg)コードはLinuxとmacOSの両方と互換性があります。ただし、Windowsユーザーは`subprocess`呼び出しに`shell=True`を追加したり、`rsync`を置き換えるなどの小さな調整を行う必要があります。

または、このコードをWindowsで実行する簡単な方法は、"Windows Subsystem for Linux"（WSL）機能を使用することです。これにより、WindowsでUbuntuを使用してLinux環境を実行できます。詳細については、[Microsoftの公式インストール手順](https://learn.microsoft.com/en-us/windows/wsl/install)と[チュートリアル](https://learn.microsoft.com/en-us/training/modules/wsl-introduction/)をお読みください。

WSLを使用する場合、Python 3がインストールされていることを確認してください（`python3 --version`で確認するか、例えばPython 3.10用に`sudo apt-get install -y python3.10`でインストール）し、そこに以下のパッケージをインストールしてください：

```bash
sudo apt-get update && \
sudo apt-get upgrade -y && \
sudo apt-get install -y python3-pip && \
sudo apt-get install -y python-is-python3 && \
sudo apt-get install -y rsync
```

> **注意：**
> Pythonの設定とパッケージのインストール方法については、[オプションのPython設定設定](../../setup/01_optional-python-setup-preferences/README.md)と[Pythonライブラリのインストール](../../setup/02_installing-python-libraries/README.md)を参照してください。
>
> オプションで、このリポジトリにはUbuntuを実行するDockerイメージが提供されています。提供されたDockerイメージでコンテナを実行する方法については、[オプションのDocker環境](../../setup/03_optional-docker-environment/README.md)を参照してください。

&nbsp;
### 2) データセットの準備

次に、`prepare_dataset.py`スクリプトを実行します。これは（この記事の執筆時点で60,173の）テキストファイルをより効率的に転送およびアクセスできるように、より少ない大きなファイルに連結します：

```bash
python prepare_dataset.py \
  --data_dir gutenberg/data/raw \
  --max_size_mb 500 \
  --output_dir gutenberg_preprocessed
```

```
...
Skipping gutenberg/data/raw/PG29836_raw.txt as it does not contain primarily English text.                                     Skipping gutenberg/data/raw/PG16527_raw.txt as it does not contain primarily English text.                                     100%|██████████████████████████████████████████████████████████| 57250/57250 [25:04<00:00, 38.05it/s]
42 file(s) saved in /Users/sebastian/Developer/LLMs-from-scratch/ch05/03_bonus_pretraining_on_gutenberg/gutenberg_preprocessed
```

> **ヒント：**
> 生成されたファイルは平文形式で保存され、簡潔性のため事前にトークン化されていないことに注意してください。ただし、データセットをより頻繁に使用したり、複数のエポックで学習を計画している場合は、計算時間を節約するためにデータセットを事前にトークン化された形式で保存するようにコードを更新することをお勧めします。詳細については、このページの下部にある*設計決定と改善*を参照してください。

> **ヒント：**
> より小さなファイルサイズ（例：50 MB）を選択できます。これによりより多くのファイルが生成されますが、テスト目的で少数のファイルでより迅速な事前学習を実行する場合に便利です。

&nbsp;
### 3) 事前学習スクリプトの実行

以下のように事前学習スクリプトを実行できます。なお、追加のコマンドライン引数は説明目的でデフォルト値で示されています：

```bash
python pretraining_simple.py \
  --data_dir "gutenberg_preprocessed" \
  --n_epochs 1 \
  --batch_size 4 \
  --output_dir model_checkpoints
```

出力は以下のように整形されます：

> Total files: 3
> Tokenizing file 1 of 3: data_small/combined_1.txt
> Training ...
> Ep 1 (Step 0): Train loss 9.694, Val loss 9.724
> Ep 1 (Step 100): Train loss 6.672, Val loss 6.683
> Ep 1 (Step 200): Train loss 6.543, Val loss 6.434
> Ep 1 (Step 300): Train loss 5.772, Val loss 6.313
> Ep 1 (Step 400): Train loss 5.547, Val loss 6.249
> Ep 1 (Step 500): Train loss 6.182, Val loss 6.155
> Ep 1 (Step 600): Train loss 5.742, Val loss 6.122
> Ep 1 (Step 700): Train loss 6.309, Val loss 5.984
> Ep 1 (Step 800): Train loss 5.435, Val loss 5.975
> Ep 1 (Step 900): Train loss 5.582, Val loss 5.935
> ...
> Ep 1 (Step 31900): Train loss 3.664, Val loss 3.946
> Ep 1 (Step 32000): Train loss 3.493, Val loss 3.939
> Ep 1 (Step 32100): Train loss 3.940, Val loss 3.961
> Saved model_checkpoints/model_pg_32188.pth
> Book processed 3h 46m 55s
> Total time elapsed 3h 46m 55s
> ETA for remaining books: 7h 33m 50s
> Tokenizing file 2 of 3: data_small/combined_2.txt
> Training ...
> Ep 1 (Step 32200): Train loss 2.982, Val loss 4.094
> Ep 1 (Step 32300): Train loss 3.920, Val loss 4.097
> ...

&nbsp;
> **ヒント：**
> 実際には、macOSまたはLinuxを使用している場合、ログ出力をターミナルに印刷するのに加えて`log.txt`ファイルに保存するために`tee`コマンドを使用することをお勧めします：

```bash
python -u pretraining_simple.py | tee log.txt
```

&nbsp;
> **警告：**
> `gutenberg_preprocessed`フォルダ内の約500 MbのテキストファイルのうちV1つでの学習には、V100 GPUで約4時間かかることに注意してください。
> フォルダには47ファイルが含まれており、完了するまでに約200時間（1週間以上）かかります。より少ない数のファイルで実行することをお勧めします。

&nbsp;
## 設計決定と改善

このコードは教育目的でシンプルで最小限に保つことに焦点を当てていることに注意してください。コードは以下の方法でモデリング性能と学習効率を改善するために改良できます：

1. `prepare_dataset.py`スクリプトを変更して、各書籍ファイルからGutenbergの定型文を削除する。
2. データ準備および読み込みユーティリティを更新して、データセットを事前にトークン化し、トークン化された形式で保存することで、事前学習スクリプトを呼び出すたびに再トークン化する必要がないようにする。
3. `train_model_simple`スクリプトを[付録D：学習ループへのベルと笛の追加](../../appendix-D/01_main-chapter-code/appendix-D.ipynb)で導入された機能、つまりコサインディケイ、線形ウォームアップ、勾配クリッピングを追加して更新する。
4. 事前学習スクリプトを更新してオプティマイザーの状態を保存し（第5章の*5.4 PyTorchでの重みの読み込みと保存*セクションを参照；[ch05.ipynb](../../ch05/01_main-chapter-code/ch05.ipynb)）、既存のモデルとオプティマイザーのチェックポイントを読み込んで学習の実行が中断された場合に学習を継続するオプションを追加する。
5. より高度なロガー（例：Weights and Biases）を追加して、損失と検証曲線をライブで表示する。
6. 分散データ並列処理（DDP）を追加し、複数のGPUでモデルを学習する（付録Aの*A.9.3 複数GPUでの学習*セクションを参照；[DDP-script.py](../../appendix-A/01_main-chapter-code/DDP-script.py)）。
7. `previous_chapter.py`スクリプトの自作`MultiheadAttention`クラスを、PyTorchの`nn.functional.scaled_dot_product_attention`関数を介してFlash Attentionを使用する[効率的なマルチヘッドアテンション実装](../../ch03/02_bonus_efficient-multihead-attention/mha-implementations.ipynb)ボーナスセクションで実装された効率的な`MHAPyTorchScaledDotProduct`クラスと置き換える。
8. [torch.compile](https://pytorch.org/tutorials/intermediate/torch_compile_tutorial.html)（`model = torch.compile`）または[thunder](https://github.com/Lightning-AI/lightning-thunder)（`model = thunder.jit(model)`）を介してモデルを最適化することで学習を高速化する。
9. 事前学習プロセスをさらに高速化するためにGradient Low-Rank Projection（GaLore）を実装する。これは、`AdamW`オプティマイザーを[GaLore Pythonライブラリ](https://github.com/jiaweizzhao/GaLore)で提供される`GaLoreAdamW`と置き換えるだけで実現できます。