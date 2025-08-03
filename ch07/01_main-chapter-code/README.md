# 第7章：指示に従うためのファインチューニング

### メインチャプターコード

- [ch07.ipynb](ch07.ipynb) には、章に登場するすべてのコードが含まれています
- [previous_chapters.py](previous_chapters.py) は、前章でコーディングして訓練したGPTモデルと多くのユーティリティ関数を含むPythonモジュールで、この章で再利用します
- [gpt_download.py](gpt_download.py) には、事前訓練されたGPTモデルの重みをダウンロードするためのユーティリティ関数が含まれています
- [exercise-solutions.ipynb](exercise-solutions.ipynb) には、この章の演習解答が含まれています


### オプションコード

- [load-finetuned-model.ipynb](load-finetuned-model.ipynb) は、この章で作成した指示ファインチューニングモデルを読み込むための独立したJupyterノートブックです

- [gpt_instruction_finetuning.py](gpt_instruction_finetuning.py) は、メイン章で説明されているように指示ファインチューニングモデルを訓練するための独立したPythonスクリプトです（ファインチューニング部分に焦点を当てた章の要約と考えてください）

使用方法:

```bash
python gpt_instruction_finetuning.py
```

```
matplotlib version: 3.9.0
tiktoken version: 0.7.0
torch version: 2.3.1
tqdm version: 4.66.4
tensorflow version: 2.16.1
--------------------------------------------------
Training set length: 935
Validation set length: 55
Test set length: 110
--------------------------------------------------
Device: cpu
--------------------------------------------------
File already exists and is up-to-date: gpt2/355M/checkpoint
File already exists and is up-to-date: gpt2/355M/encoder.json
File already exists and is up-to-date: gpt2/355M/hparams.json
File already exists and is up-to-date: gpt2/355M/model.ckpt.data-00000-of-00001
File already exists and is up-to-date: gpt2/355M/model.ckpt.index
File already exists and is up-to-date: gpt2/355M/model.ckpt.meta
File already exists and is up-to-date: gpt2/355M/vocab.bpe
Loaded model: gpt2-medium (355M)
--------------------------------------------------
Initial losses
   Training loss: 3.839039182662964
   Validation loss: 3.7619192123413088
Ep 1 (Step 000000): Train loss 2.611, Val loss 2.668
Ep 1 (Step 000005): Train loss 1.161, Val loss 1.131
Ep 1 (Step 000010): Train loss 0.939, Val loss 0.973
...
Training completed in 15.66 minutes.
Plot saved as loss-plot-standalone.pdf
--------------------------------------------------
Generating responses
100%|█████████████████████████████████████████████████████████| 110/110 [06:57<00:00,  3.80s/it]
Responses saved as instruction-data-with-response-standalone.json
Model saved as gpt2-medium355M-sft-standalone.pth
```

- [ollama_evaluate.py](ollama_evaluate.py) は、メイン章で説明されているようにファインチューニングモデルの応答を評価するための独立したPythonスクリプトです（評価部分に焦点を当てた章の要約と考えてください）

使用方法:

```bash
python ollama_evaluate.py --file_path instruction-data-with-response-standalone.json
```

```
Ollama running: True
Scoring entries: 100%|███████████████████████████████████████| 110/110 [01:08<00:00,  1.62it/s]
Number of scores: 110 of 110
Average score: 51.75
```

- [exercise_experiments.py](exercise_experiments.py) は、演習解答を実装するオプションのスクリプトです。詳細については[exercise-solutions.ipynb](exercise-solutions.ipynb)を参照してください