# ネイティブuvによるPythonとパッケージ管理

このチュートリアルは、`uv pip`インターフェースよりも`uv`のネイティブコマンドを好む方のための、[README.md](./README.md)ドキュメントの*オプション1: uvの使用*の代替です。`uv pip`は純粋な`pip`よりも高速ですが、`uv`のネイティブインターフェースは、オーバーヘッドが少なく、PyPyパッケージ依存関係管理のレガシーサポートを処理する必要がないため、`uv pip`よりもさらに高速です。

以下の表は、さまざまな依存関係とパッケージ管理アプローチの速度の比較を示しています。速度比較は特にインストール中のパッケージ依存関係の解決を指し、インストールされたパッケージの実行時パフォーマンスではありません。このプロジェクトではパッケージのインストールは一度だけのプロセスであるため、インストール速度だけでなく、全体的な利便性で好みのアプローチを選択するのが妥当です。


| コマンド              | 速度比較 |
|-----------------------|-----------------|
| `conda install <pkg>` | 最も遅い（ベースライン） |
| `pip install <pkg>`   | 上記より2-10倍高速 |
| `uv pip install <pkg>`| 上記より5-10倍高速 |
| `uv add <pkg>`        | 上記より2-5倍高速 |

このチュートリアルは`uv add`に焦点を当てています。


それ以外は、[README.md](./README.md)の*オプション1: uvの使用*と同様に、このチュートリアルでは`uv`を使用したPythonのセットアップとパッケージのインストール手順をガイドします。

このチュートリアルでは、macOSを実行するコンピュータを使用していますが、このワークフローはLinuxマシンでも同様で、他のオペレーティングシステムでも機能する可能性があります。


&nbsp;
## 1. uvのインストール

Uvは、オペレーティングシステムに応じて以下のようにインストールできます。

<br>

**macOSとLinux**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

または

```bash
wget -qO- https://astral.sh/uv/install.sh | sh
```

<br>

**Windows**

```bash
powershell -c "irm https://astral.sh/uv/install.ps1 | more"
```

&nbsp;

> **注：**
> その他のインストールオプションについては、公式の[uvドキュメント](https://docs.astral.sh/uv/getting-started/installation/#standalone-installer)を参照してください。

&nbsp;
## 2. Pythonパッケージと依存関係のインストール

`pyproject.toml`ファイル（このGitHubリポジトリのトップレベルにあるようなもの）から必要なすべてのパッケージをインストールするには、ファイルがターミナルセッションと同じディレクトリにあると仮定して、以下のコマンドを実行します：

```bash
uv sync --dev --python 3.11
```

> **注：**
> システムにPython 3.11が利用できない場合、uvがダウンロードしてインストールします。
> PyTorchの互換性を確保するために、最新リリースより少なくとも1-3バージョン古いPythonバージョンを使用することをお勧めします。例えば、最新バージョンがPython 3.13の場合、バージョン3.10、3.11、3.12の使用をお勧めします。最新のPythonバージョンは[python.org](https://www.python.org/downloads/)で確認できます。

> **注：**
> 特定の依存関係のために上記のコマンドで問題が発生した場合（例えば、Windowsを使用している場合）、いつでも通常のpipにフォールバックできます：
> `uv add pip`
> `uv run python -m pip install -U -r requirements.txt`
>
> TensorFo




上記の`uv sync`コマンドは、`.venv`サブフォルダを介して別の仮想環境を作成することに注意してください。（最初からやり直すために仮想環境を削除したい場合は、単に`.venv`フォルダを削除できます。）

`pyproject.toml`に指定されていない新しいパッケージは`uv add`でインストールできます。例えば：

```bash
uv add packaging
```

そして、`uv remove`でパッケージを削除できます。例えば：

```bash
uv remove packaging
```



&nbsp;
## 3. Pythonコードの実行

<br>

これで、リポジトリのコードを実行する環境が準備されているはずです。

オプションで、このリポジトリの`python_environment_check.py`スクリプトを実行して環境チェックを実行できます：

```bash
uv run python setup/02_installing-python-libraries/python_environment_check.py
```



<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/uv-setup/uv-run-check.png?1" width="700" height="auto" alt="Uv install">


<br>

**JupyterLabの起動**

以下でJupyterLabインスタンスを起動できます：

```bash
uv run jupyter lab
```

**`uv run`コマンドのスキップ**

`uv run`と入力するのが面倒な場合は、以下に説明するように手動で仮想環境をアクティベートできます。

macOS/Linux：

```bash
source .venv/bin/activate
```

Windows（PowerShell）：

```bash
.venv\Scripts\activate
```

その後、スクリプトを実行できます

```bash
python script.py
```

JupyterLabを起動するには

```bash
jupyter lab
```

&nbsp;
> **注：**
> jupyter labコマンドで問題が発生した場合は、仮想環境内のフルパスを使用して起動することもできます。例えば、Linux/macOSでは`.venv/bin/jupyter lab`、Windowsでは`.venv\Scripts\jupyter-lab`を使用します。

&nbsp;


&nbsp;

## オプション：仮想環境を手動で管理

代わりに、`uv pip install`を使用してリポジトリから直接依存関係をインストールすることもできます。ただし、これは`uv add`のように`uv.lock`ファイルに依存関係を記録しないことに注意してください。また、仮想環境を手動で作成してアクティベートする必要があります：

<br>

**1. 新しい仮想環境の作成**

以下のコマンドを実行して新しい仮想環境を手動で作成します。これは新しい`.venv`サブフォルダとして保存されます：

```bash
uv venv --python=python3.10
```

<br>

**2. 仮想環境のアクティベート**

次に、この新しい仮想環境をアクティベートする必要があります。

macOS/Linux：

```bash
source .venv/bin/activate
```

Windows（PowerShell）：

```bash
.venv\Scripts\activate
```

<br>

**3. 依存関係のインストール**

最後に、`uv pip`インターフェースを使用してリモートロケーションから依存関係をインストールできます：

```bash
uv pip install -U -r https://raw.githubusercontent.com/rasbt/LLMs-from-scratch/refs/heads/main/requirements.txt
```



---

ご質問がございますか？お気軽に[ディスカッションフォーラム](https://github.com/rasbt/LLMs-from-scratch/discussions)でお問い合わせください。