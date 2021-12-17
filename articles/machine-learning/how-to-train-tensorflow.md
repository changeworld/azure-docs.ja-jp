---
title: TensorFlow モデルのトレーニングとデプロイ
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でエラスティック クラウド コンピューティング リソースを使用して、TensorFlow のトレーニング ジョブをスケールアウトする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: how-to
ms.openlocfilehash: 8e53d67dacff8337d4a6832fc5febe3b83ec6126
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606828"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して大規模な TensorFlow モデルをトレーニングする

この記事では、Azure Machine Learning を使用して、大規模な [TensorFlow](https://www.tensorflow.org/overview) トレーニング スクリプトを実行する方法について説明します。

この例では、ディープ ニューラル ネットワーク (DNN) を使用して手書きの数字を分類するための TensorFlow モデルをトレーニングして登録します。

TensorFlow モデルを一から開発する場合でも、[既存のモデル](how-to-deploy-and-where.md)をクラウドに取り込む場合でも、Azure Machine Learning を使用してオープンソースのトレーニング ジョブをスケールアウトし、運用グレードのモデルの構築、デプロイ、バージョン管理、監視を行うことができます。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。

 - Azure Machine Learning コンピューティング インスタンス - ダウンロードやインストールは必要なし

     - [クイック スタート: Azure Machine Learning の利用の開始](quickstart-create-resources.md)を完了して、SDK およびサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - ノートブック サーバー上のディープ ラーニングの samples フォルダーで、**how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow** とディレクトリを移動して、完成した展開済みノートブックを見つけます。 
 
 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK (1.15.0 以上) をインストールします](/python/api/overview/azure/ml/install)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。
    - [サンプル スクリプト ファイル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` および `utils.py` をダウンロードする
     
    このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)は、GitHub サンプル ページにもあります。 このノートブックには、インテリジェントなハイパーパラメーター調整、モデル デプロイ、およびノートブックのウィジェットを示す展開済みセクションが含まれています。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、コンピューティング先を作成し、トレーニング環境を定義することで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、必要な Python ライブラリをインポートします。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
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

### <a name="create-a-file-dataset"></a>ファイル データセットの作成

`FileDataset` オブジェクトでは、ご利用のワークスペース データストアまたはパブリック URL 内の 1 つまたは複数のファイルが参照されます。 ファイルは任意の形式にすることができ、クラスには、ファイルのダウンロードまたはご利用のコンピューティングへのファイルのマウントを行うための機能が用意されています。 `FileDataset` を作成することにより、データソースの場所への参照を作成します。 データセットに変換を適用した場合は、それらの変換もデータセットに格納されます。 データは既存の場所に残るので、追加のストレージ コストは発生しません。 詳細については、`Dataset` パッケージの[攻略](./how-to-create-register-datasets.md)ガイドを参照してください。

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

他のユーザーと共有したり、さまざまな実験で再利用したり、トレーニング スクリプト内で名前で参照したりできるように、データセットをご利用のワークスペースに登録するには、`register()` メソッドを使います。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

TensorFlow ジョブを実行するためのコンピューティング先を作成します。 この例では、GPU 対応の Azure Machine Learning コンピューティング クラスターを作成します。

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
独自のイメージを作成しない場合には、事前に構築され、キュレーションされた環境が Azure ML によって提供されます。 Azure ML には、異なるバージョンの TensorFlow に対応する TensorFlow 用にキュレーションされた CPU および GPU 環境がいくつか用意されています。 詳細については、[ここ](resource-curated-environments.md)を参照してください。

キュレーションが行われた環境を使用する場合は、代わりに次のコマンドを実行できます。

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

キュレーションされた環境に含まれるパッケージを確認するために、conda の依存関係をディスクに書き出すことができます。
```python
tf_env.save_to_directory(path=curated_env_name)
```

キュレーションされた環境に、トレーニング スクリプトに必要なすべての依存関係が含まれていることを確認します。 そうでない場合は、環境を変更し、不足している依存関係を含める必要があります。 環境が変更された場合は、キュレーションされた環境用に "AzureML" プレフィックスが予約されているため、新しい名前を指定する必要があることに注意してください。 conda の依存関係 YAML ファイルを変更した場合は、それから、新しい名前で新しい環境を作成できます。次に例を示します。
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

そうではなく、キュレーションされた環境のオブジェクトを直接変更した場合は、新しい名前でその環境をクローンすることができます。
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
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
  - tensorflow-gpu==2.2.0
```

この conda 環境仕様から Azure ML 環境を作成します。 環境は、実行時に Docker コンテナーにパッケージ化されます。

ベース イメージが指定されていない場合、Azure ML では既定により CPU イメージ `azureml.core.environment.DEFAULT_CPU_IMAGE` がベース イメージとして使用されます。 この例では GPU クラスター上でトレーニングを実行するため、必要な GPU ドライバーと依存関係が含まれる GPU ベース イメージを指定する必要があります。 Azure ML では、Microsoft Container Registry (MCR) に公開された、お客様が使用できる 1 組のベース イメージを保持しています。詳細については、GitHub リポジトリ「[Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers)」を参照してください。

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> 必要に応じて、カスタムの Docker イメージまたは Dockerfile にすべての依存関係を直接キャプチャするだけで、それから環境を作成できます。 詳細については、[カスタム イメージを使用したトレーニング](how-to-train-with-custom-image.md)に関するページを参照してください。

環境の作成と使用の詳細について詳しくは、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」を参照してください。

## <a name="configure-and-submit-your-training-run"></a>トレーニングの実行を構成して送信する

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig を作成する

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) オブジェクトを作成して、トレーニング スクリプト、使用する環境、実行対象のコンピューティング先など、トレーニング ジョブの構成の詳細を指定します。 トレーニング スクリプトへの引数は、`arguments` パラメーターで指定されている場合、すべてコマンド ラインで渡されます。

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning では、ソース ディレクトリ全体をコピーすることで、トレーニング スクリプトが実行されます。 アップロードしたくない機密データがある場合は、[.ignore ファイル](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)を使用するか、ソース ディレクトリに含めないようにします。 代わりに、Azure ML [データセット](how-to-train-with-datasets.md)を使用してデータにアクセスします。

ScriptRunConfig を使用したジョブの構成の詳細については、[トレーニングの実行の構成と送信](how-to-set-up-training-targets.md)に関する記事をご覧ください。

> [!WARNING]
> 以前に TensorFlow Estimator を使用して TensorFlow トレーニング ジョブを構成していた場合は、1.19.0 SDK のリリース以降では、Estimator が非推奨になっていることに注意してください。 Azure ML SDK 1.15.0 以降では、ディープ ラーニング フレームワークを使用するものを含めて、ScriptRunConfig がトレーニング ジョブを構成する場合に推奨される方法です。 移行に関する一般的な質問については、[Estimator から ScriptRunConfig への移行](how-to-migrate-from-estimators-to-scriptrunconfig.md)に関するガイドを参照してください。

### <a name="submit-a-run"></a>実行を送信する

[実行オブジェクト](/python/api/azureml-core/azureml.core.run%28class%29)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>実行実施中の動作
実行は、以下の段階を経て実施されます。

- **準備**:Docker イメージは、定義されている環境に従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。 代わりに、キュレーションされた環境が指定されている場合は、そのキュレーションされた環境を補足するキャッシュ済みのイメージが使用されます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **[実行中]** : スクリプト フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされて、`script` が実行されます。 stdout からの出力と **./logs** フォルダーが実行履歴にストリーミングされるので、実行の監視のために使用できます。

- **後処理**:実行の **./outputs** フォルダーが実行履歴にコピーされます。

## <a name="register-or-download-a-model"></a>モデルを登録またはダウンロードする

モデルのトレーニングが終わったら、それをワークスペースに登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。 必要に応じてパラメーター `model_framework`、`model_framework_version`、および `resource_configuration` を指定することによって、コードなしのモデル デプロイが使用可能になります。 これにより、登録済みのモデルからモデルを Web サービスとして直接デプロイできるようになり、`ResourceConfiguration` オブジェクトによって Web サービスのコンピューティング リソースが定義されます。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

実行オブジェクトを使用してモデルのローカル コピーをダウンロードすることもできます。 トレーニング スクリプトの `tf_mnist.py` では、TensorFlow セーバー オブジェクトによってモデルがローカル フォルダー (コンピューティング先に対するローカル) に永続化されます。 実行オブジェクトを使用してコピーをダウンロードすることができます。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>分散トレーニング

Azure Machine Learning では、トレーニング ワークロードをスケーリングできるように、マルチノードの分散 TensorFlow ジョブもサポートされています。 分散 TensorFlow ジョブは簡単に実行でき、オーケストレーションの管理は Azure ML によって自動的に行われます。

Azure ML では、Horovod と TensorFlow の両方について、組み込みの分散トレーニング API を使用した分散 TensorFlow ジョブの実行がサポートされています。

分散トレーニングの詳細については、「[分散 GPU トレーニング ガイド](how-to-train-distributed-gpu.md)」を参照してください。

## <a name="deploy-a-tensorflow-model"></a>TensorFlow モデルをデプロイする

デプロイ方法にはモデルの登録に関するセクションが含まれていますが、登録済みのモデルが既にあるため、デプロイのために[コンピューティング先の作成](how-to-deploy-and-where.md#choose-a-compute-target)に直接スキップできます。

### <a name="preview-no-code-model-deployment"></a>(プレビュー) コードなしのモデル デプロイ

従来のデプロイ ルートの代わりに、コードなしのデプロイ機能 (プレビュー) を TensorFlow に使用することもできます。 `model_framework`、`model_framework_version`、および `resource_configuration` パラメーターを使用して前に示したようにモデルを登録することにより、単純に `deploy()` 静的関数を使用してモデルをデプロイできます。

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

完全な[方法](how-to-deploy-and-where.md)では、Azure Machine Learning を使用したデプロイがさらに詳細に説明されています。

## <a name="next-steps"></a>次のステップ

この記事では、TensorFlow モデルをトレーニングして登録し、デプロイのオプションについて学習しました。 Azure Machine Learning の詳細については、以下の他の記事をご覧ください。

* [トレーニング中に実行メトリクスを追跡する](how-to-log-view-metrics.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
