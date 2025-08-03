# 付録A: PyTorchの紹介

### メイン章コード

- [code-part1.ipynb](code-part1.ipynb) には、章に記載されているセクションA.1からA.8のすべてのコードが含まれています
- [code-part2.ipynb](code-part2.ipynb) には、章に記載されているセクションA.9のGPUコードが含まれています
- [DDP-script.py](DDP-script.py) にはマルチGPU使用法を実演するスクリプトが含まれています（Jupyter Notebookは単一GPUのみをサポートするため、これはノートブックではなくスクリプトです）。`python DDP-script.py`として実行できます。お使いのマシンに2つ以上のGPUがある場合は、`CUDA_VISIBLE_DEVIVES=0,1 python DDP-script.py`として実行してください。
- [exercise-solutions.ipynb](exercise-solutions.ipynb) にはこの章の演習解答が含まれています

### オプションコード

- [DDP-script-torchrun.py](DDP-script-torchrun.py) は、`multiprocessing.spawn`を使用して複数のプロセスを手動で生成・管理する代わりに、PyTorchの`torchrun`コマンドで実行される`DDP-script.py`スクリプトのオプション版です。`torchrun`コマンドには、マルチノード連携を含む分散初期化を自動的に処理する利点があり、セットアップ処理を若干簡素化します。このスクリプトは`torchrun --nproc_per_node=2 DDP-script-torchrun.py`として使用できます