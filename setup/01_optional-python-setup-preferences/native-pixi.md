# ネイティブpixiによるPythonとパッケージ管理

このチュートリアルは、`conda`や`pip`のような従来の環境およびパッケージマネージャーよりも`pixi`のネイティブコマンドを好む方のための[`./native-uv.md`](native-uv.md)ドキュメントの代替です。

pixiは[`./native-uv.md`](native-uv.md)で説明されているように、内部で`uv add`を使用していることに注意してください。

PixiとuvはどちらもPythonの最新のパッケージおよび環境管理ツールですが、pixiはPythonだけでなく他の言語も管理するために設計された多言語パッケージマネージャー（condaに似ています）であり、uvは超高速の依存関係解決とパッケージインストールに最適化されたPython固有のツールです。

複数の言語（Pythonだけでなく）をサポートする多言語パッケージマネージャーが必要な場合、またはcondaに似た宣言的環境管理アプローチを好む場合は、uvよりもpixiを選ぶかもしれません。詳細については、公式の[pixiドキュメント](https://pixi.sh/latest/)をご覧ください。

このチュートリアルでは、macOSを実行するコンピュータを使用していますが、このワークフローはLinuxマシンでも同様で、他のオペレーティングシステムでも機能する可能性があります。

&nbsp;
## 1. pixiのインストール

Pixiは、オペレーティングシステムに応じて以下のようにインストールできます。

<br>

**macOSとLinux**

```bash
curl -fsSL https://pixi.sh/install.sh | sh
```

または

```bash
wget -qO- https://pixi.sh/install.sh | sh
```

<br>

**Windows**

```powershell
powershell -ExecutionPolicy ByPass -c "irm -useb https://pixi.sh/install.ps1 | iex"
```

> **注：**
> その他のインストールオプションについては、公式の[pixiドキュメント](https://pixi.sh/latest/)を参照してください。


&nbsp;
## 2. Pythonのインストール

pixiを使用してPythonをインストールできます：

```bash
pixi add python=3.10
```

> **注：**
> PyTorchの互換性を確保するために、最新リリースより少なくとも2バージョン古いPythonバージョンをインストールすることをお勧めします。例えば、最新バージョンがPython 3.13の場合、バージョン3.10または3.11をインストールすることをお勧めします。最新のPythonバージョンは[python.org](https://www.python.org)で確認できます。

&nbsp;
## 3. Pythonパッケージと依存関係のインストール

`pixi.toml`ファイル（このGitHubリポジトリのトップレベルにあるようなもの）から必要なすべてのパッケージをインストールするには、ファイルがターミナルセッションと同じディレクトリにあると仮定して、以下のコマンドを実行します：

```bash
pixi install
```

> **注：**
> 依存関係で問題が発生した場合（例えば、Windowsを使用している場合）、いつでもpipにフォールバックできます：`pixi run pip install -U -r requirements.txt`

デフォルトでは、`pixi install`はプロジェクト固有の別の仮想環境を作成します。

`pixi.toml`に指定されていない新しいパッケージは`pixi add`でインストールできます。例えば：

```bash
pixi add packaging
```

そして、`pixi remove`でパッケージを削除できます。例えば：

```bash
pixi remove packaging
```

&nbsp;
## 4. Pythonコードの実行

これで、リポジトリのコードを実行する環境が準備されているはずです。

オプションで、このリポジトリの`python_environment_check.py`スクリプトを実行して環境チェックを実行できます：

```bash
pixi run python setup/02_installing-python-libraries/python_environment_check.py
```

<br>

**JupyterLabの起動**

以下でJupyterLabインスタンスを起動できます：

```bash
pixi run jupyter lab
```


---

ご質問がございますか？お気軽に[ディスカッションフォーラム](https://github.com/rasbt/LLMs-from-scratch/discussions)でお問い合わせください。