# オプションのセットアップ手順


このドキュメントでは、マシンのセットアップとこのリポジトリのコードを使用するためのさまざまなアプローチをリストアップしています。上から下まで各セクションを参照して、ニーズに最も適したアプローチを決定することをお勧めします。

&nbsp;

## クイックスタート

マシンにPythonがすでにインストールされている場合、最も素早く始める方法は、このコードリポジトリのルートディレクトリから以下のpipインストールコマンドを実行して、[../requirements.txt](../requirements.txt)ファイルからパッケージ要件をインストールすることです：

```bash
pip install -r requirements.txt
```

<br>

> **注：** Google Colabでノートブックを実行しており、依存関係をインストールしたい場合は、ノートブックの上部の新しいセルで以下のコードを実行するだけです：
> `pip install uv && uv pip install --system -r https://raw.githubusercontent.com/rasbt/LLMs-from-scratch/refs/heads/main/requirements.txt`



以下のビデオでは、私がコンピュータにPython環境をセットアップする個人的なアプローチを共有しています：

<br>
<br>

[![Link to the video](https://img.youtube.com/vi/yAcWnfsZhzo/0.jpg)](https://www.youtube.com/watch?v=yAcWnfsZhzo)


&nbsp;
# ローカルセットアップ

このセクションでは、本書のコードをローカルで実行するための推奨事項を提供します。本書のメインチャプターのコードは、通常のラップトップで妥当な時間内に実行できるように設計されており、専門的なハードウェアは必要ありません。私はすべてのメインチャプターをM3 MacBook Airラップトップでテストしました。さらに、ラップトップまたはデスクトップコンピュータにNVIDIA GPUがある場合、コードは自動的にそれを活用します。

&nbsp;
## Pythonのセットアップ

マシンにまだPythonをセットアップしていない場合、以下のディレクトリに私の個人的なPythonセットアップの好みについて書いています：

- [01_optional-python-setup-preferences](./01_optional-python-setup-preferences)
- [02_installing-python-libraries](./02_installing-python-libraries)

以下の*DevContainersの使用*セクションでは、マシンにプロジェクトの依存関係をインストールするための代替アプローチを概説しています。

&nbsp;

## Docker DevContainersの使用

上記の*Pythonのセットアップ*セクションの代替として、プロジェクトの依存関係と設定を分離する開発セットアップを好む場合、Dockerの使用は非常に効果的なソリューションです。このアプローチにより、ソフトウェアパッケージとライブラリを手動でインストールする必要がなくなり、一貫した開発環境が保証されます。DockerのセットアップとDevContainerの使用に関する詳細な手順は以下を参照してください：

- [03_optional-docker-environment](03_optional-docker-environment)

&nbsp;

## Visual Studio Codeエディタ

コードエディタには多くの良い選択肢があります。私の好みの選択は、多くの有用なプラグインと拡張機能で簡単に強化できる人気のあるオープンソース[Visual Studio Code (VSCode)](https://code.visualstudio.com)エディタです（詳細については以下の*VSCode拡張機能*セクションを参照）。macOS、Linux、Windowsのダウンロード手順は、[VSCodeのメインウェブサイト](https://code.visualstudio.com)で見つけることができます。

&nbsp;

## VSCode拡張機能

Visual Studio Code（VSCode）を主要なコードエディタとして使用している場合、`.vscode`サブフォルダに推奨される拡張機能があります。これらの拡張機能は、このリポジトリに役立つ強化された機能とツールを提供します。

これらをインストールするには、VSCodeでこの「setup」フォルダを開き（ファイル -> フォルダを開く...）、右下のポップアップメニューで「インストール」ボタンをクリックします。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/vs-code-extensions.webp?1" alt="1" width="700">

または、`.vscode`拡張機能フォルダをこのGitHubリポジトリのルートディレクトリに移動することもできます：

```bash
mv setup/.vscode ./
```

次に、`LLMs-from-scratch`メインフォルダを開くたびに、VSCodeは推奨される拡張機能がシステムにすでにインストールされているかどうかを自動的にチェックします。

&nbsp;

# クラウドリソース

このセクションでは、本書で提示されるコードを実行するためのクラウドの代替案について説明します。

コードは専用のGPUなしで通常のラップトップやデスクトップコンピュータで実行できますが、NVIDIA GPUを搭載したクラウドプラットフォームは、特に第5章から第7章でコードの実行時間を大幅に改善できます。

&nbsp;

## Lightning Studioの使用

クラウドでのスムーズな開発体験のために、[Lightning AI Studio](https://lightning.ai/)プラットフォームをお勧めします。これにより、ユーザーは永続的な環境を設定し、クラウドCPUとGPUでVSCodeとJupyter Labの両方を使用できます。

新しいStudioを開始したら、ターミナルを開いて以下のセットアップ手順を実行し、リポジトリをクローンして依存関係をインストールします：

```bash
git clone https://github.com/rasbt/LLMs-from-scratch.git
cd LLMs-from-scratch
pip install -r requirements.txt
```

（Google Colabとは対照的に、Lightning AI Studio環境は永続的なため、CPUマシンとGPUマシンを切り替えても、これらは一度実行するだけで済みます。）

次に、実行したいPythonスクリプトまたはJupyter Notebookに移動します。オプションで、第5章でLLMを事前学習したり、第6章と第7章でファインチューニングしたりする際に、コードの実行時間を短縮するためにGPUを簡単に接続することもできます。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/studio.webp" alt="1" width="700">

&nbsp;

## Google Colabの使用

クラウドでGoogle Colab環境を使用するには、[https://colab.research.google.com/](https://colab.research.google.com/)にアクセスし、GitHubメニューから該当する章のノートブックを開くか、下の図に示すようにノートブックを*アップロード*フィールドにドラッグします。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/colab_1.webp" alt="1" width="700">


また、下に示すように、関連するファイル（データセットファイルとノートブックがインポートする.pyファイル）もColab環境にアップロードしてください。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/colab_2.webp" alt="2" width="700">


以下の図に示すように、*ランタイム*を変更することで、オプションでGPUでコードを実行できます。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/colab_3.webp" alt="3" width="700">


&nbsp;

# 質問は？

ご質問がある場合は、このGitHubリポジトリの[Discussions](https://github.com/rasbt/LLMs-from-scratch/discussions)フォーラムで遠慮なくお問い合わせください。