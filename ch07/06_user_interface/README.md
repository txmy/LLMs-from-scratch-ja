# 指示ファインチューニングされたGPTモデルとの対話ユーザーインターフェイスの構築



このボーナスフォルダには、以下に示すように第7章の指示ファインチューニングされたGPTとの対話用ChatGPT風ユーザーインターフェイスを実行するコードが含まれています。



![Chainlit UI example](https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/chainlit/chainlit-sft.webp?2)



このユーザーインターフェイスを実装するために、オープンソースの[Chainlit Pythonパッケージ](https://github.com/Chainlit/chainlit)を使用しています。

&nbsp;
## ステップ1：依存関係のインストール

最初に、以下の方法で`chainlit`パッケージをインストールします

```bash
pip install chainlit
```

（または、`pip install -r requirements-extra.txt`を実行してください。）

&nbsp;
## ステップ2：`app`コードの実行

[`app.py`](app.py)ファイルには、UIコードが含まれています。これらのファイルを開いて詳細を学んでください。

このファイルは、第7章で生成したGPT-2の重みを読み込んで使用します。これには、最初に[`../01_main-chapter-code/ch07.ipynb`](../01_main-chapter-code/ch07.ipynb)ファイルを実行する必要があります。

UIサーバーを開始するために、ターミナルから以下のコマンドを実行してください：

```bash
chainlit run app.py
```

上記のコマンドを実行すると、モデルとの対話ができる新しいブラウザタブが開きます。ブラウザタブが自動的に開かない場合は、ターミナルコマンドを確認し、ローカルアドレスをブラウザのアドレスバーにコピーしてください（通常、アドレスは`http://localhost:8000`です）。