---
title: ディープ ラーニング PyTorch モデルをトレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して、PyTorch トレーニング スクリプトをエンタープライズ規模で実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: how-to
ms.openlocfilehash: 6891da994955359dcc10b8e994d8c2dd05436fce
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606765"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して PyTorch モデルを大規模にトレーニングする

この記事では、Azure Machine Learning を使用して、[PyTorch](https://pytorch.org/) トレーニング スクリプトをエンタープライズ規模で実行する方法について説明します。

この記事のサンプル スクリプトは、PyTorch の転移学習[チュートリアル](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)に基づいてニワトリと七面鳥の画像を分類し、ディープ ラーニング ニューラル ネットワーク (DNN) を構築するために使用されます。 転移学習は、ある問題を解決することで得られた知識を、異なるが関連している問題に適用する手法です。 これは、最初からトレーニングするよりも必要なデータ、時間、コンピューティング リソースが少なくなるので、トレーニング プロセスを短縮します。 転移学習の詳細については、[ディープ ラーニングと機械学習](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning)に関する記事を参照してください。

PyTorch のディープ ラーニング モデルを一からトレーニングする場合でも、既存のモデルをクラウドに持ち込む場合でも、Azure Machine Learning のエラスティック クラウド コンピューティング リソースを使用して、オープンソースのトレーニング ジョブをスケールアウトできます。 Azure Machine Learning を使用して、運用レベルのモデルをビルド、デプロイ、バージョン管理、および監視することができます。 

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。

- Azure Machine Learning コンピューティング インスタンス - ダウンロードやインストールは必要なし

    - [クイック スタート: Azure Machine Learning の利用の開始](quickstart-create-resources.md)を完了して、SDK およびサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - ノートブック サーバー上のディープ ラーニングの samples フォルダーで、**how-to-use-azureml > ml-frameworks > pytorch > train-hyperparameter-tune-deploy-with-pytorch** とディレクトリを移動して、完成した展開済みノートブックを見つけます。 
 
 - 独自の Jupyter Notebook サーバー
    - [Azure Machine Learning SDK (1.15.0 以上) をインストールします](/python/api/overview/azure/ml/install)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。
    - [サンプル スクリプト ファイルをダウンロードする](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb)は、GitHub サンプル ページにもあります。 このノートブックには、インテリジェントなハイパーパラメーター調整、モデル デプロイ、およびノートブックのウィジェットを示す展開済みセクションが含まれています。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、コンピューティング先を作成し、トレーニング環境を定義することで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、必要な Python ライブラリをインポートします。

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>データを取得する

データセットは、七面鳥と鶏のそれぞれについて約 120 のトレーニング画像で構成され、クラスごとに 100 の検証用画像があります。 トレーニング スクリプト `pytorch_train.py` の一部として、データセットをダウンロードして展開します。 画像は [Open Images v5 Dataset](https://storage.googleapis.com/openimages/web/index.html) のサブセットです。

### <a name="prepare-training-script"></a>トレーニング スクリプトを準備する

このチュートリアルでは、トレーニング スクリプトの `pytorch_train.py` は既に用意されています。 実際には、任意のカスタム トレーニング スクリプトをそのまま使用し、Azure Machine Learning で実行できます。

トレーニング スクリプト用のフォルダーを作成します。

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

PyTorch ジョブを実行するためのコンピューティング先を作成します。 この例では、GPU 対応の Azure Machine Learning コンピューティング クラスターを作成します。

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

### <a name="define-your-environment"></a>環境を定義する

トレーニング スクリプトの依存関係をカプセル化する Azure ML [環境](concept-environments.md)を定義するには、カスタム環境を定義するか、Azure ML のキュレーションされた環境を使用します。

#### <a name="use-a-curated-environment"></a>選別された環境を使用する
独自のイメージを作成しない場合には、事前に構築され、キュレーションされた環境が Azure ML によって提供されます。 Azure ML には、異なるバージョンの PyTorch に対応する PyTorch 用にキュレーションされた CPU および GPU 環境がいくつか用意されています。 詳細については、[ここ](resource-curated-environments.md)を参照してください。

キュレーションが行われた環境を使用する場合は、代わりに次のコマンドを実行できます。

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

キュレーションされた環境に含まれるパッケージを確認するために、conda の依存関係をディスクに書き出すことができます。
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

キュレーションされた環境に、トレーニング スクリプトに必要なすべての依存関係が含まれていることを確認します。 そうでない場合は、環境を変更し、不足している依存関係を含める必要があります。 環境が変更された場合は、キュレーションされた環境用に "AzureML" プレフィックスが予約されているため、新しい名前を指定する必要があることに注意してください。 conda の依存関係 YAML ファイルを変更した場合は、それから、新しい名前で新しい環境を作成できます。次に例を示します。
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

そうではなく、キュレーションされた環境のオブジェクトを直接変更した場合は、新しい名前でその環境をクローンすることができます。
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>カスタム環境を作成する

トレーニング スクリプトの依存関係をカプセル化する独自の Azure ML 環境を作成することもできます。

最初に、YAML ファイルで conda の依存関係を定義します (この例では、ファイルの名前は `conda_dependencies.yml` です)。

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

この conda 環境仕様から Azure ML 環境を作成します。 環境は、実行時に Docker コンテナーにパッケージ化されます。

ベース イメージが指定されていない場合、Azure ML では既定により CPU イメージ `azureml.core.environment.DEFAULT_CPU_IMAGE` がベース イメージとして使用されます。 この例では GPU クラスター上でトレーニングを実行するため、必要な GPU ドライバーと依存関係が含まれる GPU ベース イメージを指定する必要があります。 Azure ML では、Microsoft Container Registry (MCR) に公開された、お客様が使用できる 1 組のベース イメージを保持しています。詳細については、GitHub リポジトリ「[Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers)」を参照してください。

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> 必要に応じて、カスタムの Docker イメージまたは Dockerfile にすべての依存関係を直接キャプチャするだけで、それから環境を作成できます。 詳細については、[カスタム イメージを使用したトレーニング](how-to-train-with-custom-image.md)に関するページを参照してください。

環境の作成と使用の詳細について詳しくは、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」を参照してください。

## <a name="configure-and-submit-your-training-run"></a>トレーニングの実行を構成して送信する

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig を作成する

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) オブジェクトを作成して、トレーニング スクリプト、使用する環境、実行対象のコンピューティング先など、トレーニング ジョブの構成の詳細を指定します。 トレーニング スクリプトへの引数は、`arguments` パラメーターで指定されている場合、すべてコマンド ラインで渡されます。 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning では、ソース ディレクトリ全体をコピーすることで、トレーニング スクリプトが実行されます。 アップロードしたくない機密データがある場合は、[.ignore ファイル](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)を使用するか、ソース ディレクトリに含めないようにします。 代わりに、Azure ML [データセット](how-to-train-with-datasets.md)を使用してデータにアクセスします。

ScriptRunConfig を使用したジョブの構成の詳細については、[トレーニングの実行の構成と送信](how-to-set-up-training-targets.md)に関する記事をご覧ください。

> [!WARNING]
> 以前に PyTorch Estimator を使用して PyTorch トレーニング ジョブを構成していた場合は、1.19.0 SDK のリリース以降では、Estimator が非推奨になっていることに注意してください。 Azure ML SDK 1.15.0 以降では、ディープ ラーニング フレームワークを使用するものを含めて、ScriptRunConfig がトレーニング ジョブを構成する場合に推奨される方法です。 移行に関する一般的な質問については、[Estimator から ScriptRunConfig への移行](how-to-migrate-from-estimators-to-scriptrunconfig.md)に関するガイドを参照してください。

## <a name="submit-your-run"></a>実行を送信する

[実行オブジェクト](/python/api/azureml-core/azureml.core.run%28class%29)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>実行実施中の動作
実行は、以下の段階を経て実施されます。

- **準備**:Docker イメージは、定義されている環境に従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。 代わりに、キュレーションされた環境が指定されている場合は、そのキュレーションされた環境を補足するキャッシュ済みのイメージが使用されます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **[実行中]** : スクリプト フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされて、`script` が実行されます。 stdout からの出力と **./logs** フォルダーが実行履歴にストリーミングされるので、実行の監視のために使用できます。

- **後処理**:実行の **./outputs** フォルダーが実行履歴にコピーされます。

## <a name="register-or-download-a-model"></a>モデルを登録またはダウンロードする

モデルのトレーニングが終わったら、それをワークスペースに登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> デプロイ方法にはモデルの登録に関するセクションが含まれていますが、登録済みのモデルが既にあるため、デプロイのために[コンピューティング先の作成](how-to-deploy-and-where.md#choose-a-compute-target)に直接スキップできます。

実行オブジェクトを使用してモデルのローカル コピーをダウンロードすることもできます。 トレーニング スクリプトの `pytorch_train.py` では、PyTorch 保存オブジェクトによってモデルがローカル フォルダー (コンピューティング先に対するローカル) に永続化されます。 実行オブジェクトを使用してコピーをダウンロードすることができます。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>分散トレーニング

Azure Machine Learning では、トレーニング ワークロードをスケーリングできるように、マルチノードの分散 PyTorch ジョブもサポートされています。 分散 PyTorch ジョブは簡単に実行でき、オーケストレーションの管理は Azure ML によって自動的に行われます。

Azure ML では、Horovod と PyTorch の両方について、DistributedDataParallel 組み込みモジュールを使用した分散 PyTorch ジョブの実行がサポートされています。

分散トレーニングの詳細については、「[分散 GPU トレーニング ガイド](how-to-train-distributed-gpu.md)」を参照してください。

## <a name="export-to-onnx"></a>ONNX にエクスポートする

[ONNX Runtime](concept-onnx.md) での推論を最適化するために、トレーニングされた PyTorch モデルを ONNX 形式に変換します。 推論、つまりモデル スコアリングとは、通常は運用環境のデータに基づいて、デプロイしたモデルを使用して予測を行うフェーズです。 例については、[チュートリアル](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Machine Learning で PyTorch を使用して、ディープ ラーニング ニューラル ネットワークをトレーニングして登録しました。 モデルをデプロイする方法を学習するには、モデル デプロイの記事に進んでください。

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)
* [トレーニング中に実行メトリクスを追跡する](how-to-log-view-metrics.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
