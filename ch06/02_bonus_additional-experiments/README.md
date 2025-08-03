# 追加の分類ファインチューニング実験

以下の表は、様々な設計選択に関する追加の質問に答えるための実験を追加したものです。最初の行はメインチャプターと同じ設定を使用しており、参照として使用されます。
例えば、

- 行1と2を比較すると「最後のトークンか最初のトークンを学習する場合の性能差は何か？」という質問に答えます；
- 行1と3を比較すると「最後のブロックではなく最後のレイヤーのみを学習する場合の性能差は何か？」という質問に答えます；
- その他も同様です。

&nbsp;

|      | モデル             | 重み       | 学習可能トークン位置 | 学習可能レイヤー | コンテキスト長                                         | 訓練精度     | 検証精度       | テスト精度 | 訓練時間      | CPU/GPU |
| ---- | ------------------ | ---------- | -------------------- | ---------------- | ------------------------------------------------------ | ------------ | -------------- | ---------- | ------------- | ------- |
| 1    | gpt2-small (124M)  | 事前学習済み | last                 | last_block       | 最長訓練例 (120)                                      | 96.63%       | 99.33%         | 95.00%     | 0.28 min      | A100    |
| 2    | gpt2-small (124M)  | 事前学習済み | first                | last_block       | 最長訓練例 (120)                                      | 78.46%       | 80.54%         | 75.00%     | 0.28 min      | A100    |
| 3    | gpt2-small (124M)  | 事前学習済み | last                 | last_layer       | 最長訓練例 (120)                                      | 78.65%       | 79.87%         | 72.00%     | 0.25 min      | A100    |
| 4    | gpt2-small (124M)  | 事前学習済み | last                 | last_two_blocks  | 最長訓練例 (120)                                      | 98.85%       | 98.66%         | 98.33%     | 0.33 min      | A100    |
| 5    | gpt2-small (124M)  | 事前学習済み | last                 | all              | 最長訓練例 (120)                                      | 99.62%       | 96.64%         | 96.67%     | 0.69 min      | A100    |
| 6    | gpt2-medium (355M) | 事前学習済み | last                 | last_block       | 最長訓練例 (120)                                      | 87.50%       | 91.28%         | 84.67%     | 0.75 min      | A100    |
| 7    | gpt2-large (774M)  | 事前学習済み | last                 | last_block       | 最長訓練例 (120)                                      | 99.52%       | 98.66%         | 96.67%     | 1.50 min      | A100    |
| 8    | gpt2-xl (1558M)    | 事前学習済み | last                 | last_block       | 最長訓練例 (120)                                      | 99.81%       | 99.81%         | 98.33%     | 2.83 min      | A100    |
| 9    | gpt2-xl (1558M)    | 事前学習済み | last                 | all              | 最長訓練例 (120)                                      | 100.00%      | 98.66%         | 98.67%     | 8.12 min      | A100    |
| 10   | gpt2-small (124M)  | ランダム   | last                 | all              | 最長訓練例 (120)                                      | 100.00%      | 96.64%         | 93.67%     | 0.69 min      | A100    |
| 11   | gpt2-small (124M)  | 事前学習済み | last                 | LoRA             | 最長訓練例 (120)                                      | 100.00%      | 97.32%         | 96.67%     | 0.75 min      | A100    |
| 12   | gpt2-xl (1558M)    | 事前学習済み | last                 | LoRA             | 最長訓練例 (120)                                      | 100.00%      | 98.66%         | 98.33%     | 5.79 min      | A100    |
| 13   | gpt2-small (124M)  | 事前学習済み | last                 | last_block       | コンテキスト長 (1024)                                 | 83.08%       | 87.92%         | 78.33%     | 2.46 min      | A100    |
| 14   | gpt2-small (124M)  | 事前学習済み | last                 | last_block       | 可変: パディングなし (バッチサイズ 1)                 | 100.00%      | 98.66%         | 98.00%     | 1.75 min      | A100    |
| 15   | gpt2-small (124M)  | 事前学習済み | last                 | last_block       | 可変: パディングなし (バッチサイズ 8)                 | 99.33%       | 98.66%         | 98.33%     | 1.70 min      | A100    |
| 16   | gpt2-small (124M)  | 事前学習済み | last                 | last_block       | 柔軟 (最後の非パディング位置)                         | 99.42%       | 98.66%         | 98.33%     | 0.30 min      | A100    |
| 17   | gpt2-small (124M)  | 事前学習済み | last                 | last_block       | 最長訓練例 (120); ただし因果マスクなし                | 99.23%       | 98.66%         | 95.33%     | 0.29 min      | A100    |
| 18   | gpt2-small (124M)  | 事前学習済み | last                 | last_block       | 最長訓練例 (120) とパディング用`ignore_index`        | 96.63%       | 99.33%         | 95.00%     | 0.28 min      | A100    |
| 19   | gpt2-small (124M)  | 事前学習済み | last + プール埋め込み | last_block       | 最長訓練例 (120)                                      | 97.79%       | 99.33%         | 96.33%     | 0.32 min      | A100    |

&nbsp;

### 使用方法

以下のコードを使用して実験を再現できます：

- 行1: `python additional_experiments.py`
- 行2: `python additional_experiments.py --trainable_token_pos first`
- 行3: `python additional_experiments.py --trainable_layers last_layer`
- 行4: `python additional_experiments.py --trainable_layers last_two_blocks`
- 行5: `python additional_experiments.py --trainable_layers all`
- 行6: `python additional_experiments.py --model_size "gpt2-medium (355M)"`
- 行7: `python additional_experiments.py --model_size "gpt2-large (774M)"`
- 行8: `python additional_experiments.py --model_size "gpt2-xl (1558M)"`
- 行9: `python additional_experiments.py --model_size "gpt2-xl (1558M)"--trainable_layers all`
- 行10: `python additional_experiments.py --weights random --trainable_layers all`
- 行11: `python additional_experiments.py --trainable_layers lora --lora_rank 16 --lora_alpha 16`
- 行12: `python additional_experiments.py --trainable_layers lora --lora_rank 16 --lora_alpha 8 --model_size "gpt2-xl (1558M)"`
- 行13: `python additional_experiments.py --context_length "model_context_length"`
- 行14: `python additional_experiments.py --no_padding --batch_size 1`
- 行15: `python additional_experiments.py --no_padding --batch_size 1 --accumulation_steps 8`
- 行16: `python additional_experiments.py --trainable_token_pos "flexible"`
- 行17: `python additional_experiments.py --disable_causal_mask`
- 行18: `python additional_experiments.py --ignore_index 50256`
- 行19: `python additional_experiments.py --average_embeddings`

LLMとデータセットを意図的に小さく保っているため、GPUにアクセスできない場合でも、MacBook Air M3のような通常のラップトップで約15分（デフォルト設定の場合）で訓練を実行できます。

&nbsp;

### 解釈

1. **最後と最初の出力トークン位置の学習（行1 vs 2）**: 最後の出力トークン位置を学習することで、最初の位置と比較して大幅に良い性能が得られます。この改善は因果自己注意マスクによるものです。
2. **最後のTransformerブロックと最後のレイヤーの学習（行1 vs 3）**: 最後のTransformerブロック全体を学習することも、最後のレイヤーのみを学習するよりも大幅に良い結果をもたらします。
3. **最後と最後の2つのTransformerブロックの学習（行1 vs 4）**: 最後のブロックのみではなく最後の2つのTransformerブロックを学習することで、目立つ3.33%の精度向上が得られます。
4. **最後のTransformerブロックと全レイヤーの学習（行1 vs 5）**: 全レイヤーを学習することで、最後のTransformerブロックのみの学習と比較して約2%の控えめな改善が見られますが、訓練時間は約3倍長くなります。また、12個のTransformerブロックのうち最後の2つのみを学習する場合と比較すると性能が劣ります。
5. **より大きな事前学習済みモデルの使用（行1 vs 6、および行1 vs 7と8）**: 3倍大きな事前学習済みモデルを使用すると結果が悪くなります。しかし、5倍大きなモデルを使用すると予想通り初期モデルと比較して性能が向上します。同様に、12倍大きなモデルは予測性能をさらに向上させます。（mediumモデルは事前学習が十分でないか、特定のファインチューニング設定がこのモデルでうまく機能しない可能性があります。）
6. **ランダム重みと事前学習済み重みを持つモデルの使用（行1と5 vs 10）**: ランダム重みを持つモデルを使用すると、事前学習済み重みを使用する場合と比較してわずかに悪い結果（3%と1.3%）が得られます。
7. **LoRA（Low-Rank Adaptation）と全レイヤー学習の使用（行11 vs 5、および行12 vs 9）**: モデルを凍結し、学習可能なLoRAレイヤーを追加する（詳細は[付録E](../../appendix-E/01_main-chapter-code/appendix-E.ipynb)を参照）ことは、全モデルパラメータを学習する実行可能な代替手段であり、1%ポイント（行11 vs 5）性能を向上させます。LoRAを使用する際の訓練精度と検証精度の差が約1%小さいことから、これは過学習の軽減によるものと考えられます。さらに、LoRAは更新する必要のあるパラメータが少ないため、よりメモリ効率的です。より大きなモデルを学習する場合（行12 vs 9）、LoRAの方がはるかに高速に学習できることもわかります（8.12分ではなく5.79分）。
8. **最長訓練例と完全コンテキスト長へのパディング（行1 vs 13）**: 完全サポートコンテキスト長への入力パディングは大幅に悪い結果となります。
9. **パディングありとパディングなし（行1 vs 14と15、および16）**: `--no_padding`オプションはデータセットのパディングを無効にし、入力が可変長のためバッチサイズ1でモデルを学習する必要があります。これによりテスト精度は向上しますが、学習時間が長くなります。行15では、他の実験と同じバッチサイズを達成するために8ステップの勾配蓄積を追加で有効にし、過学習を軽減してテストセット精度をわずかに向上させます。行16では、パディングが適用されますが、トークン位置は最後の非パディングトークンに基づいて選択されます。行16は数学的には勾配蓄積を使用する行15と類似しているはずです。しかし、不等なトークン数の場合の勾配蓄積に関するいくつかの課題により、小さな差異がある可能性があります（これは[この](https://unsloth.ai/blog/gradient)ブログ記事で議論されています）。
10. **因果注意マスクの無効化（行1 vs 17）**: マルチヘッド注意モジュールで使用される因果注意マスクを無効化します。これにより、すべてのトークンが他のすべてのトークンに注意を向けることができます。モデル精度は因果マスクを持つGPTモデルと比較してわずかに改善されます。
11. **損失と逆伝播でのパディングインデックスの無視（行1 vs 18）**: `--ignore_index 50256`を設定すると、PyTorchの`cross_entropy`損失関数で`<|endoftext|>`パディングトークンが除外されます。この場合、二項分類例ではトークンIDが0または1になるように出力レイヤーを置き換えたため、効果はありません。しかし、この設定は第7章でモデルの指示ファインチューニングを行う際に有用です。
12. **全トークンでの埋め込み平均化（行1 vs 19）**: `--average_embeddings`を設定すると、すべてのトークンで埋め込みが平均化されます。このオプションを使用しない場合（デフォルト）、選択されたトークン位置（`--trainable_token_pos`で指定）の出力埋め込みのみが考慮されます；例えば、最後のトークンの埋め込み。`--average_embeddings`を有効にすると、すべてのトークンの埋め込みが`--trainable_token_pos`で選択された位置（デフォルトでは最後のトークン）に平均プールされます。見ての通り、これにより性能が95.00%から96.33%に向上し、実行時間の増加は最小限（0.28分から0.32分）であり、実際には検討する価値があるかもしれません。