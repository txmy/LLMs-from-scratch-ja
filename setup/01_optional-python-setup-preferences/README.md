# Pythonセットアップのヒント



Pythonをインストールしてコンピューティング環境をセットアップする方法はいくつかあります。ここでは、私の個人的な好みを共有します。

<br>

> **注：** 
> Google Colabでノートブックを実行しており、依存関係をインストールしたい場合は、ノートブックの上部の新しいセルで以下のコードを実行して、このチュートリアルの残りをスキップしてください：
> `pip install uv && uv pip install --system -r https://raw.githubusercontent.com/rasbt/LLMs-from-scratch/refs/heads/main/requirements.txt`

以下のセクションでは、ローカルマシンでPython環境とパッケージを管理する方法について説明します。

私は長年[Conda](https://anaconda.org/anaconda/conda)と[pip](https://pypi.org/project/pip/)のユーザーでしたが、最近、[uv](https://github.com/astral-sh/uv)パッケージが、パッケージのインストールと依存関係の解決をより高速で効率的に行う方法を提供するため、大きな注目を集めています。

2025年のより現代的なアプローチである*オプション1: uvの使用*から始めることをお勧めします。*オプション1*で問題が発生した場合は、*オプション2: Condaの使用*を検討してください。

このチュートリアルでは、macOSを実行するコンピュータを使用していますが、このワークフローはLinuxマシンでも同様で、他のオペレーティングシステムでも機能する可能性があります。


&nbsp;
# オプション1: uvの使用

このセクションでは、`uv pip`インターフェースを介して`uv`を使用したPythonのセットアップとパッケージのインストール手順を説明します。`uv pip`インターフェースは、ネイティブの`uv`コマンドよりも、以前にpipを使用したことのあるほとんどのPythonユーザーにとってより馴染みやすいかもしれません。

&nbsp;
> **注：**
> Pythonをインストールして`uv`を使用する代替方法があります。例えば、`uv`を介して直接Pythonをインストールし、さらに高速なパッケージ管理のために`uv pip install`の代わりに`uv add`を使用できます。
>
> macOSまたはLinuxユーザーでネイティブの`uv`コマンドを好む場合は、[./native-uv.md チュートリアル](./native-uv.md)を参照してください。また、公式の[`uv`ドキュメント](https://docs.astral.sh/uv/)を確認することもお勧めします。
>
> `uv add`構文はWindowsユーザーにも適用されます。しかし、`pyproject.toml`の一部の依存関係がWindowsで問題を引き起こすことがわかりました。そのため、Windowsユーザーには、`uv add`のような`pixi add`ワークフローを持つ`pix`を代わりにお勧めします。詳細については、[./native-pixi.md チュートリアル](./native-pixi.md)を参照してください。
>
> `uv add`と`pixi add`は追加のスピードの利点を提供しますが、`uv pip`は少しユーザーフレンドリーだと思うので、初心者にとって良い出発点となります。しかし、Pythonパッケージ管理に慣れていない場合、ネイティブの`uv`インターフェースも最初から学ぶ絶好の機会です。私も今は`uv`をそのように使用していますが、`pip`や`conda`から来る場合、参入障壁が少し高いことは理解しています。




&nbsp;
## 1. Pythonのインストール（未インストールの場合）

システムに手動でPythonをインストールしたことがない場合は、インストールすることを強くお勧めします。これにより、問題につながる可能性のあるオペレーティングシステムの組み込みPythonインストールとの潜在的な競合を防ぐのに役立ちます。

ただし、以前にシステムにPythonをインストールしたことがある場合でも、ターミナルで以下のコードを実行して、最新バージョンのPython（3.10以降を推奨）がインストールされているかどうかを確認してください：

```bash
python --version
```
3.10以降が返される場合、それ以上のアクションは必要ありません。

&nbsp;
> **注：**
> `python --version`がPythonバージョンがインストールされていないことを示す場合、システムが代わりに`python3`コマンドを使用するように設定されている可能性があるため、`python3 --version`もチェックすることをお勧めします。

&nbsp;
> **注：**
> PyTorchの互換性を確保するために、最新リリースより少なくとも2バージョン古いPythonバージョンをインストールすることをお勧めします。例えば、最新バージョンがPython 3.13の場合、バージョン3.10または3.11をインストールすることをお勧めします。

それ以外の場合、Pythonがインストールされていないか、古いバージョンの場合は、以下に説明するようにオペレーティングシステム用にインストールできます。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/uv-setup/python-not-found.png" width="500" height="auto" alt="No Python Found">

<br>

**Linux (Ubuntu/Debian)**

```bash
sudo apt update
sudo apt install python3.10 python3.10-venv python3.10-dev
```

<br>

**macOS**

Homebrewを使用している場合は、以下でPythonをインストールします：

```bash
brew install python@3.10
```

または、公式ウェブサイトからインストーラーをダウンロードして実行します：[https://www.python.org/downloads/](https://www.python.org/downloads/)。


<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/uv-setup/python-version.png" width="700" height="auto" alt="Python version">

<br>

**Windows**

公式ウェブサイトからインストーラーをダウンロードして実行します：[https://www.python.org/downloads/](https://www.python.org/downloads/)。


&nbsp;

## 2. 仮想環境の作成

OSが依存する可能性のあるシステム全体のパッケージを変更しないように、別の仮想環境にPythonパッケージをインストールすることを強くお勧めします。現在のフォルダに仮想環境を作成するには、以下の3つのステップに従ってください。

<br>

**1. uvのインストール**

```bash
pip install uv
```

<br>

**2. 仮想環境の作成**

```bash
uv venv --python=python3.10
```

<br>

**3. 仮想環境のアクティベート**

```bash
source .venv/bin/activate
```

&nbsp;
> **注：**
> Windowsを使用している場合は、上記のコマンドを`source .venv/Scripts/activate`または`.venv/Scripts/activate`に置き換える必要があるかもしれません。



新しいターミナルセッションを開始するたびに仮想環境をアクティベートする必要があることに注意してください。例えば、ターミナルまたはコンピュータを再起動して翌日プロジェクトの作業を続けたい場合は、プロジェクトフォルダで`source .venv/bin/activate`を実行して仮想環境を再アクティベートするだけです。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/uv-setup/venv-activate-1.png" width="600" height="auto" alt="Venv activated">

オプションで、`deactivate`コマンドを実行して環境を非アクティブ化できます。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/uv-setup/venv-activate-2.png" width="800" height="auto" alt="Venv deactivated">

&nbsp;
## 3. パッケージのインストール

仮想環境をアクティベートした後、`uv`を使用してPythonパッケージをインストールできます。例えば：

```bash
uv pip install packaging
```

`requirements.txt`ファイル（このGitHubリポジトリのトップレベルにあるようなもの）から必要なすべてのパッケージをインストールするには、ファイルがターミナルセッションと同じディレクトリにあると仮定して、以下のコマンドを実行します：

```bash
uv pip install -r requirements.txt
```

または、リポジトリから最新の依存関係を直接インストールします：

```bash
uv pip install -r https://raw.githubusercontent.com/rasbt/LLMs-from-scratch/refs/heads/main/requirements.txt
```


<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/uv-setup/uv-install.png" width="700" height="auto" alt="Uv install">

&nbsp;

> **注：**
> 特定の依存関係のために上記のコマンドで問題が発生した場合（例えば、Windowsを使用している場合）、いつでも通常のpipを使用することに戻すことができます：
> `pip install -r requirements.txt`
> または
> `pip install -U -r https://raw.githubusercontent.com/rasbt/LLMs-from-scratch/refs/heads/main/requirements.txt`

<br>

**セットアップの完了**

以上です！これで、リポジトリのコードを実行するために環境が準備されているはずです。

オプションで、このリポジトリの`python_environment_check.py`スクリプトを実行して環境チェックを実行できます：

```bash
python setup/02_installing-python-libraries/python_environment_check.py
```

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/uv-setup/env-check.png" width="700" height="auto" alt="Environment check">

特定のパッケージで問題が発生した場合は、以下を使用して再インストールしてみてください：

```bash
uv pip install packagename
```

（ここで、`packagename`は、問題のあるパッケージ名に置き換える必要があるプレースホルダー名です。）

問題が続く場合は、GitHubで[ディスカッションを開く](https://github.com/rasbt/LLMs-from-scratch/discussions)か、以下の*オプション2: Condaの使用*セクションを実行することを検討してください。

<br>

**コードでの作業を開始**

すべてがセットアップされたら、コードファイルでの作業を開始できます。例えば、以下を実行して[JupyterLab](https://jupyterlab.readthedocs.io/en/latest/)を起動します：

```bash
jupyter lab
```

&nbsp;
> **注：**
> jupyter labコマンドで問題が発生した場合は、仮想環境内のフルパスを使用して起動することもできます。例えば、Linux/macOSでは`.venv/bin/jupyter lab`、Windowsでは`.venv\Scripts\jupyter-lab`を使用します。

&nbsp;

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/uv-setup/jupyter.png" width="900" height="auto" alt="Uv install">

&nbsp;
<br>
<br>
&nbsp;

# オプション2: Condaの使用



このセクションでは、[miniforge](https://github.com/conda-forge/miniforge)を介して[`conda`](https://www.google.com/search?client=safari&rls=en&q=conda&ie=UTF-8&oe=UTF-8)を使用したPythonのセットアップとパッケージのインストール手順を説明します。

このチュートリアルでは、macOSを実行するコンピュータを使用していますが、このワークフローはLinuxマシンでも同様で、他のオペレーティングシステムでも機能する可能性があります。


&nbsp;
## 1. Miniforgeのダウンロードとインストール

GitHubリポジトリ[こちら](https://github.com/conda-forge/miniforge)からminiforgeをダウンロードします。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/01_optional-python-setup-preferences/download.png" alt="download" width="600px">

オペレーティングシステムに応じて、`.sh`（macOS、Linux）または`.exe`ファイル（Windows）がダウンロードされるはずです。

`.sh`ファイルの場合、コマンドラインターミナルを開いて以下のコマンドを実行します

```bash
sh ~/Desktop/Miniforge3-MacOSX-arm64.sh
```

ここで`Desktop/`は、Miniforgeインストーラーがダウンロードされたフォルダです。お使いのコンピュータでは、`Downloads/`に置き換える必要があるかもしれません。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/01_optional-python-setup-preferences/miniforge-install.png" alt="miniforge-install" width="600px">

次に、「Enter」で確認しながらダウンロード手順を進めます。


&nbsp;
## 2. 新しい仮想環境の作成

インストールが正常に完了した後、以下を実行して`LLMs`という新しい仮想環境を作成することをお勧めします

```bash
conda create -n LLMs python=3.10
```

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/01_optional-python-setup-preferences/new-env.png" alt="new-env" width="600px">

> 多くの科学計算ライブラリは、Pythonの最新バージョンをすぐにはサポートしません。したがって、PyTorchをインストールする際は、1つまたは2つ前のリリースのPythonバージョンを使用することをお勧めします。例えば、Pythonの最新バージョンが3.13の場合、Python 3.10または3.11の使用が推奨されます。

次に、新しい仮想環境をアクティベートします（新しいターミナルウィンドウまたはタブを開くたびに行う必要があります）：

```bash
conda activate LLMs
```

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/01_optional-python-setup-preferences/activate-env.png" alt="activate-env" width="600px">


&nbsp;
## オプション：ターミナルのスタイリング

どの仮想環境がアクティブかがわかるように、私のようにターミナルをスタイルしたい場合は、[Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh)プロジェクトをチェックしてください。

&nbsp;
## 3. 新しいPythonライブラリのインストール



新しいPythonライブラリをインストールするには、`conda`パッケージインストーラーを使用できるようになりました。例えば、[JupyterLab](https://jupyter.org/install)と[watermark](https://github.com/rasbt/watermark)を以下のようにインストールできます：

```bash
conda install jupyterlab watermark
```

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/01_optional-python-setup-preferences/conda-install.png" alt="conda-install" width="600px">



ライブラリをインストールするために`pip`を使用することもできます。デフォルトでは、`pip`は新しい`LLms` conda環境にリンクされているはずです：

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/01_optional-python-setup-preferences/check-pip.png" alt="check-pip" width="600px">

&nbsp;
## 4. PyTorchのインストール

PyTorchは、pipを使用して他のPythonライブラリやパッケージと同じようにインストールできます。例えば：

```bash
pip install torch
```

ただし、PyTorchはCPUおよびGPU互換のコードを含む包括的なライブラリであるため、インストールには追加の設定と説明が必要な場合があります（詳細については本書の*A.1.3 PyTorchのインストール*を参照してください）。

また、[https://pytorch.org](https://pytorch.org)の公式PyTorchウェブサイトのインストールガイドメニューを参照することを強くお勧めします。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/01_optional-python-setup-preferences/pytorch-installer.jpg" width="600px">

&nbsp;
## 5. 本書で使用されるPythonパッケージとライブラリのインストール

必要なライブラリのインストール方法については、[本書で使用されるPythonパッケージとライブラリのインストール](../02_installing-python-libraries/README.md)ドキュメントを参照してください。

<br>

---




ご質問がございますか？お気軽に[ディスカッションフォーラム](https://github.com/rasbt/LLMs-from-scratch/discussions)でお問い合わせください。