# 本書で使用されるPythonパッケージとライブラリのインストール

このドキュメントでは、インストールされたPythonのバージョンとパッケージの再確認に関する詳細情報を提供します。（Pythonとpythonパッケージのインストールの詳細については、[../01_optional-python-setup-preferences](../01_optional-python-setup-preferences)フォルダを参照してください。）

本書では、[こちら](https://github.com/rasbt/LLMs-from-scratch/blob/main/requirements.txt)にリストされている以下のライブラリを使用しました。これらのライブラリの新しいバージョンも互換性がある可能性があります。ただし、コードで問題が発生した場合は、フォールバックとしてこれらのライブラリバージョンを試すことができます。



> **注：**
> [オプション1: uvの使用](../01_optional-python-setup-preferences/README.md)で説明されているように`uv`を使用している場合、以下のコマンドで`pip`を`uv pip`に置き換えることができます。例えば、`pip install -r requirements.txt`は`uv pip install -r requirements.txt`になります。



これらの要件を最も便利にインストールするには、このコードリポジトリのルートディレクトリにある`requirements.txt`ファイルを使用して、以下のコマンドを実行できます：

```bash
pip install -r requirements.txt
```

または、GitHub URLを介して以下のようにインストールできます：

```bash
pip install -r https://raw.githubusercontent.com/rasbt/LLMs-from-scratch/main/requirements.txt
```


インストールが完了したら、以下を使用してすべてのパッケージがインストールされ、最新であることを確認してください

```bash
python python_environment_check.py
```

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/02_installing-python-libraries/check_1.jpg" width="600px">

また、このディレクトリの`python_environment_check.ipynb`を実行してJupyterLabでバージョンを確認することをお勧めします。理想的には上記と同じ結果が得られるはずです。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/02_installing-python-libraries/check_2.jpg" width="500px">

以下の問題が表示される場合、JupyterLabインスタンスが間違ったconda環境に接続されている可能性があります：

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/02_installing-python-libraries/jupyter-issues.jpg" width="450px">

この場合、`--conda`フラグを使用して`watermark`を使用し、正しいconda環境でJupyterLabインスタンスを開いたかどうかを確認することができます：

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/02_installing-python-libraries/watermark.jpg" width="350px">


&nbsp;
## PyTorchのインストール

PyTorchは、pipを使用して他のPythonライブラリやパッケージと同じようにインストールできます。例えば：

```bash
pip install torch
```

ただし、PyTorchはCPUおよびGPU互換のコードを含む包括的なライブラリであるため、インストールには追加の設定と説明が必要な場合があります（詳細については本書の*A.1.3 PyTorchのインストール*を参照してください）。

また、[https://pytorch.org](https://pytorch.org)の公式PyTorchウェブサイトのインストールガイドメニューを参照することを強くお勧めします。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/02_installing-python-libraries/pytorch-installer.jpg" width="600px">

<br>

---




ご質問がございますか？お気軽に[ディスカッションフォーラム](https://github.com/rasbt/LLMs-from-scratch/discussions)でお問い合わせください。