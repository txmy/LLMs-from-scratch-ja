# GPTベースのスパム分類器と対話するユーザーインターフェースの構築

このボーナスフォルダには、以下に示すように、第6章のファインチューニング済みGPTベースのスパム分類器と対話するChatGPTライクなユーザーインターフェースを実行するためのコードが含まれています。

![Chainlit UI example](https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/chainlit/chainlit-spam.webp)

このユーザーインターフェースを実装するために、オープンソースの[Chainlit Pythonパッケージ](https://github.com/Chainlit/chainlit)を使用します。

&nbsp;
## ステップ1: 依存関係のインストール

まず、以下を実行して`chainlit`パッケージをインストールします

```bash
pip install chainlit
```

（または、`pip install -r requirements-extra.txt`を実行します。）

&nbsp;
## ステップ2: `app`コードの実行

[`app.py`](app.py)ファイルにはUIコードが含まれています。これらのファイルを開いて調べ、詳細を学習してください。

このファイルは第6章で生成したGPT-2分類器の重みを読み込んで使用します。これには、まず[`../01_main-chapter-code/ch06.ipynb`](../01_main-chapter-code/ch06.ipynb)ファイルを実行する必要があります。

UIサーバーを開始するために、ターミナルから以下のコマンドを実行します：

```bash
chainlit run app.py
```

上記のコマンドを実行すると、モデルと対話できる新しいブラウザタブが開くはずです。ブラウザタブが自動的に開かない場合は、ターミナルコマンドを調べて、ローカルアドレスをブラウザのアドレスバーにコピーしてください（通常、アドレスは`http://localhost:8000`です）。