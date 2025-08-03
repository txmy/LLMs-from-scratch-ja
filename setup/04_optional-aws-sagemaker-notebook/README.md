# AWS CloudFormationテンプレート：LLMs-from-scratchリポジトリを含むJupyterノートブック

このCloudFormationテンプレートは、Amazon SageMakerでGPU対応のJupyterノートブックを作成し、実行ロールとLLMs-from-scratch GitHubリポジトリを設定します。

## 機能：

1. SageMakerノートブックインスタンスに必要な権限を持つIAMロールを作成します。
2. ノートブックインスタンスを暗号化するためのKMSキーとエイリアスを作成します。
3. ノートブックインスタンスのライフサイクル設定スクリプトを構成します：
   - ユーザーのホームディレクトリに別のMinicondaインストールをインストールします。
   - CUDAサポート付きのTensorFlow 2.15.0とPyTorch 2.1.0を含むカスタムPython環境を作成します。
   - Jupyter Lab、Matplotlib、その他の有用なライブラリなどの追加パッケージをインストールします。
   - カスタム環境をJupyterカーネルとして登録します。
4. 指定された構成（GPU対応インスタンスタイプ、実行ロール、デフォルトコードリポジトリを含む）でSageMakerノートブックインスタンスを作成します。

## 使用方法：

1. CloudFormationテンプレートファイル（`cloudformation-template.yml`）をダウンロードします。
2. AWSマネジメントコンソールで、CloudFormationサービスに移動します。
3. 新しいスタックを作成し、テンプレートファイルをアップロードします。
4. ノートブックインスタンスの名前を指定します（例：「LLMsFromScratchNotebook」）（デフォルトはLLMs-from-scratch GitHubリポジトリ）。
5. テンプレートのパラメータを確認して承認し、スタックを作成します。
6. スタックの作成が完了すると、SageMakerノートブックインスタンスがSageMakerコンソールで利用可能になります。
7. ノートブックインスタンスを開き、事前設定された環境を使用してLLMs-from-scratchプロジェクトの作業を開始します。

## 重要なポイント：

- テンプレートは50GBのストレージを持つGPU対応（`ml.g4dn.xlarge`）ノートブックインスタンスを作成します。
- CUDAサポート付きのTensorFlow 2.15.0とPyTorch 2.1.0を含むカスタムMiniconda環境をセットアップします。
- カスタム環境はJupyterカーネルとして登録され、ノートブックで使用できるようになります。
- テンプレートは、ノートブックインスタンスを暗号化するためのKMSキーと必要な権限を持つIAMロールも作成します。