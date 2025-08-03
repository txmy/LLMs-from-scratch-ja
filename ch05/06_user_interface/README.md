# 事前学習済みLLMと対話するユーザーインターフェースの構築

このボーナスフォルダには、以下に示すように、第5章の事前学習済みLLMと対話するためのChatGPTライクなユーザーインターフェースを実行するためのコードが含まれています。

![Chainlit UI example](https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/chainlit/chainlit-orig.webp)

このユーザーインターフェースを実装するために、オープンソースの[Chainlit Pythonパッケージ](https://github.com/Chainlit/chainlit)を使用します。

&nbsp;
## ステップ1：依存関係のインストール

まず、以下のコマンドで`chainlit`パッケージをインストールします：

```bash
pip install chainlit
```

（または、`pip install -r requirements-extra.txt`を実行してください。）

&nbsp;
## ステップ2：`app`コードの実行

このフォルダには2つのファイルが含まれています：

1. [`app_orig.py`](app_orig.py): このファイルはOpenAIの元のGPT-2重みを読み込んで使用します。
2. [`app_own.py`](app_own.py): このファイルは第5章で生成したGPT-2重みを読み込んで使用します。これには最初に[`../01_main-chapter-code/ch05.ipynb`](../01_main-chapter-code/ch05.ipynb)ファイルを実行する必要があります。

（詳細を学ぶためにこれらのファイルを開いて調べてください。）

UIサーバーを開始するために、ターミナルから以下のコマンドのいずれかを実行してください：

```bash
chainlit run app_orig.py
```

または

```bash
chainlit run app_own.py
```

上記のコマンドのいずれかを実行すると、モデルと対話できる新しいブラウザタブが開くはずです。ブラウザタブが自動的に開かない場合は、ターミナルコマンドを調べて、ローカルアドレスをブラウザのアドレスバーにコピーしてください（通常、アドレスは`http://localhost:8000`です）。