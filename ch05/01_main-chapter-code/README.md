# 第5章: ラベルなしデータで事前学習する

### メインチャプターコード

- [ch05.ipynb](ch05.ipynb) 章に記載されているすべてのコードを含む
- [previous_chapters.py](previous_chapters.py) 前章の`MultiHeadAttention`モジュールと`GPTModel`クラスを含むPythonモジュールで、[ch05.ipynb](ch05.ipynb)でGPTモデルを事前学習するためにインポートする
- [gpt_download.py](gpt_download.py) 事前学習済みGPTモデルの重みをダウンロードするためのユーティリティ関数を含む
- [exercise-solutions.ipynb](exercise-solutions.ipynb) この章の演習問題の解答を含む

### オプションコード

- [gpt_train.py](gpt_train.py) [ch05.ipynb](ch05.ipynb)で実装したGPTモデルをトレーニングするコードを含むスタンドアロンPythonスクリプトファイル（この章をまとめたコードファイルと考えることができます）
- [gpt_generate.py](gpt_generate.py) [ch05.ipynb](ch05.ipynb)で実装したOpenAIから事前学習済みモデルの重みを読み込んで使用するコードを含むスタンドアロンPythonスクリプトファイル