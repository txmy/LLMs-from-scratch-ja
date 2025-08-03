# より効率的なマルチヘッドアテンション実装

- [mha-implementations.ipynb](mha-implementations.ipynb) マルチヘッドアテンションの異なる実装を含み比較する



### 要約

以下の図は、パフォーマンスベンチマークをまとめたものです（低い方が良い）。


&nbsp;
#### フォワードパスのみ

<a href="mha-implementations.ipynb"><img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/mha-benchmark/1_forward-only.webp?1" width="500px"></a>

&nbsp;
#### フォワードとバックワードパス

<a href="mha-implementations.ipynb"><img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/mha-benchmark/2_forward-and-backward.webp?1" width="500px"></a>

&nbsp;
#### コンパイル後のフォワードとバックワードパス

<a href="mha-implementations.ipynb"><img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/mha-benchmark/3_forward-and-backward-compiled.webp?1" width="500px"></a>