# Docker環境セットアップガイド

プロジェクトの依存関係と設定を分離する開発セットアップを好む場合、Dockerの使用は非常に効果的なソリューションです。このアプローチにより、ソフトウェアパッケージとライブラリを手動でインストールする必要がなくなり、一貫した開発環境が保証されます。

このガイドでは、[../01_optional-python-setup-preferences](../01_optional-python-setup-preferences)と[../02_installing-python-libraries](../02_installing-python-libraries)で説明されているcondaアプローチよりもこれを好む場合、本書のオプションのDocker環境をセットアップするプロセスを説明します。

<br>

## Dockerのダウンロードとインストール

Dockerを始める最も簡単な方法は、関連するプラットフォーム用の[Docker Desktop](https://docs.docker.com/desktop/)をインストールすることです。

Linux（Ubuntu）ユーザーは、代わりに[Docker Engine](https://docs.docker.com/engine/install/ubuntu/)をインストールし、[インストール後](https://docs.docker.com/engine/install/linux-postinstall/)の手順に従うことを好むかもしれません。

<br>

## Visual Studio CodeでDocker DevContainerを使用する

Docker DevContainer（開発コンテナ）は、開発者がDockerコンテナを完全な開発環境として使用できるようにするツールです。このアプローチにより、ユーザーはローカルマシンのセットアップに関係なく、一貫した開発環境で素早く開始できます。

DevContainersは他のIDEでも動作しますが、DevContainersで作業するために一般的に使用されるIDE/エディタはVisual Studio Code（VS Code）です。以下のガイドでは、VS Codeのコンテキストでこの本のDevContainerを使用する方法を説明しますが、同様のプロセスはPyCharmにも適用されるはずです。[インストール](https://code.visualstudio.com/download)していない場合は、使用したい場合はインストールしてください。

1. このGitHubリポジトリをクローンし、プロジェクトのルートディレクトリに`cd`します。

```bash
git clone https://github.com/rasbt/LLMs-from-scratch.git
cd LLMs-from-scratch
```

2. `.devcontainer`フォルダを`setup/03_optional-docker-environment/`から現在のディレクトリ（プロジェクトルート）に移動します。

```bash
mv setup/03_optional-docker-environment/.devcontainer ./
```

3. Docker Desktopで、**_desktop-linux_ builder**が実行されており、Dockerコンテナのビルドに使用されることを確認してください（_Docker Desktop_ -> _設定の変更_ -> _ビルダー_ -> _desktop-linux_ -> _..._ -> _使用_を参照）

4. [CUDA対応GPU](https://developer.nvidia.com/cuda-gpus)がある場合、トレーニングと推論を高速化できます：

    4.1 [こちら](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#installing-with-apt)で説明されているように**NVIDIA Container Toolkit**をインストールします。NVIDIA Container Toolkitは[こちら](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#nvidia-compute-software-support-on-wsl-2)に記載されているようにサポートされています。

    4.2 Docker Engineデーモン設定に_nvidia_をランタイムとして追加します（_Docker Desktop_ -> _設定の変更_ -> _Docker Engine_を参照）。設定にこれらの行を追加します：

    ```json
    "runtimes": {
        "nvidia": {
        "path": "nvidia-container-runtime",
        "runtimeArgs": []
    ```

    例えば、完全なDocker Engineデーモン設定のJSONコードは次のようになります：

    ```json
    {
      "builder": {
        "gc": {
          "defaultKeepStorage": "20GB",
          "enabled": true
        }
      },
      "experimental": false,
      "runtimes": {
        "nvidia": {
          "path": "nvidia-container-runtime",
          "runtimeArgs": []
        }
      }
    }
    ```

    そしてDocker Desktopを再起動します。

5. ターミナルで`code .`と入力してVS Codeでプロジェクトを開きます。または、VS Codeを起動してUIから開くプロジェクトを選択できます。

6. 左側のVS Code _拡張機能_メニューから**Remote Development**拡張機能をインストールします。

7. DevContainerを開きます。

`.devcontainer`フォルダがメインの`LLMs-from-scratch`ディレクトリに存在するため（`.`で始まるフォルダは設定によってはOSで非表示になる場合があります）、VS Codeは自動的にそれを検出し、プロジェクトをdevcontainerで開くかどうかを尋ねるはずです。そうでない場合は、単に`Ctrl + Shift + P`を押してコマンドパレットを開き、`dev containers`と入力してDevContainer固有のすべてのオプションのリストを表示します。

8. **コンテナで再度開く**を選択します。

Dockerは、`.devcontainer`設定で指定されたDockerイメージのビルドプロセスを開始します（以前にビルドされていない場合）、またはレジストリから利用可能な場合はイメージをプルします。

プロセス全体は自動化されており、システムとインターネット速度によっては数分かかる場合があります。オプションで、VS Codeの右下隅にある「Dev Containerを開始しています（ログを表示）」をクリックして、現在のビルドの進行状況を確認できます。

完了すると、VS Codeは自動的にコンテナに接続し、新しく作成されたDocker開発環境内でプロジェクトを再度開きます。ローカルマシンで実行されているかのようにコードを記述、実行、デバッグできますが、Dockerの分離と一貫性の追加の利点があります。

> **警告：**
> ビルドプロセス中にエラーが発生した場合、マシンに互換性のあるGPUがないため、NVIDIA container toolkitをサポートしていない可能性があります。この場合、`devcontainer.json`ファイルを編集して`"runArgs": ["--runtime=nvidia", "--gpus=all"],`行を削除し、「Dev Containerで再度開く」手順を再度実行してください。

9. 完了。

イメージがプルされてビルドされたら、プロジェクトがコンテナ内にマウントされ、すべてのパッケージがインストールされ、開発の準備が整っているはずです。

<br>

## Dockerイメージのアンインストール

以下は、Dockerコンテナとイメージを使用する予定がなくなった場合にアンインストールまたは削除する手順です。このプロセスはシステムからDocker自体を削除するのではなく、プロジェクト固有のDockerアーティファクトをクリーンアップします。

1. DevContainerに関連付けられたものを見つけるためにすべてのDockerイメージをリストします：

```bash
docker image ls
```

2. イメージIDまたは名前を使用してDockerイメージを削除します：

```bash
docker image rm [IMAGE_ID_OR_NAME]
```

<br>

## Dockerのアンインストール

Dockerが適していないと判断してアンインストールしたい場合は、特定のオペレーティングシステムの手順を概説した公式ドキュメント[こちら](https://docs.docker.com/desktop/uninstall/)を参照してください。