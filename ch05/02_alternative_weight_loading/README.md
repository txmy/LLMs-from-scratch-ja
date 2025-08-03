# 事前学習済み重みの代替読み込み手法

このフォルダには、OpenAIから重みが利用できなくなった場合の代替重み読み込み戦略が含まれています。

- [weight-loading-pytorch.ipynb](weight-loading-pytorch.ipynb): （推奨）元のTensorFlow重みを変換して作成したPyTorch state dictsから重みを読み込むコードが含まれています

- [weight-loading-hf-transformers.ipynb](weight-loading-hf-transformers.ipynb): `transformers`ライブラリを介してHugging Face Model Hubから重みを読み込むコードが含まれています

- [weight-loading-hf-safetensors.ipynb](weight-loading-hf-safetensors.ipynb): `safetensors`ライブラリを直接使用してHugging Face Model Hubから重みを読み込むコードが含まれています（Hugging Face transformerモデルのインスタンス化をスキップ）