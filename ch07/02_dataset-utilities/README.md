# 第7章：指示に従うためのファインチューニング

このフォルダには、指示データセットの準備に使用できるユーティリティコードが含まれています。

追加のパッケージ要件を以下の方法でインストールしてください：

```bash
pip install -r requirements-extra.txt
```




### ほぼ重複項目の検出

`find-near-duplicates.py`関数は、指示データセット内の重複項目とほぼ重複項目を識別するために使用できます。例：



```bash
python find-near-duplicates.py --json_file instruction-examples.json
```

```
scikit-learn version: 1.3.1


==================================================
Searching 'instruction' for duplicates ...
==================================================
Duplicate pair found with similarity 0.94:
1. Edit the following sentence to make it more formal.
2. Edit the sentence to make it more formal.

Duplicate pair found with similarity 1.00:
1. Name a dwarf planet in our solar system.
2. Name a dwarf planet in our solar system.

Duplicate pair found with similarity 0.91:
1. Change the sentences from active voice to passive voice.
2. Change the sentence from passive to active voice.



==================================================
Searching 'input' for duplicates ...
==================================================
No duplicates found


==================================================
Searching 'output' for duplicates ...
==================================================
Duplicate pair found with similarity 1.00:
1. One dwarf planet in our solar system is Pluto.
2. One dwarf planet in our solar system is Pluto.


```

&nbsp;
感度を下げたり上げたりするために、0から1の間の値で`--threshold`設定を使用できます。
デフォルトの閾値は0.9です。



&nbsp;
 ## 受動態エントリの作成

 - [create-passive-voice-entries.ipynb](create-passive-voice-entries.ipynb)ノートブックは、OpenAIのGPT-4を使用して指示データセット用の「受動態」エントリを作成します。以下の例のように表示されます

 ```python
 {  
    'instruction': 'Identify the verb in the following sentence',
    'input': 'The cat sleeps on the couch.',
    'output': 'The verb in the sentence is "sleeps."',
    'output_2': 'The sentence is "sleeps."'   #  <---- 新しく作成されたエントリ
 }  
 ```