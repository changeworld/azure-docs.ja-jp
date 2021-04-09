---
title: カスタム Docker イメージを使用してモデルをトレーニングする
titleSuffix: Azure Machine Learning
description: お客様独自の Docker イメージ、または Microsoft の厳選されたイメージを使用して、Azure Machine Learning でモデルをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 03400173dd35660d6e7a98500b831a7c6aa4ebd7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521172"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>カスタム Docker イメージを使用してモデルをトレーニングする

この記事では、Azure Machine Learning でモデルをトレーニングするときに、カスタム Docker イメージを使用する方法について説明します。 この記事のサンプル スクリプトは、畳み込みニューラル ネットワークを作成してペットの画像を分類するために使用します。 

Azure Machine Learning では既定の Docker 基本イメージを提供します。 Azure Machine Learning 環境を使用して、保守されている [Azure Machine Learning 基本イメージ](https://github.com/Azure/AzureML-Containers)の 1 つや独自の[カスタム イメージ](how-to-deploy-custom-docker-image.md#create-a-custom-base-image)など、さまざまなの基本イメージを指定することもできます。 カスタム基本イメージを使用すると、依存関係を詳細に管理し、トレーニング ジョブの実行時にコンポーネントのバージョンを厳密に制御できます。

## <a name="prerequisites"></a>前提条件

コードは、次の環境のいずれかで実行してください。

* Azure Machine Learning コンピューティング インスタンス (ダウンロードやインストールは必要なし):
  * [環境とワークスペースの設定](tutorial-1st-experiment-sdk-setup.md)に関するチュートリアルを完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
  * Azure Machine Learning の [例のリポジトリ](https://github.com/Azure/azureml-examples)で、 **[notebooks]**  >  **[fastai]**  >  **[train-pets-resnet34.ipynb]** ディレクトリに移動して完了したノートブックを検索します。 
* 独自の Jupyter Notebook サーバー:
  * [ワークスペース構成ファイル](how-to-configure-environment.md#workspace)を作成します。
  * [Azure Machine Learning SDK](/python/api/overview/azure/ml/install) をインストールします。 
  * インターネット上で使用可能な [Azure コンテナー レジストリ](../container-registry/index.yml) またはその他の Docker レジストリを作成します。

## <a name="set-up-a-training-experiment"></a>トレーニング実験を設定する

このセクションでは、ワークスペースを初期化し、環境を定義し、コンピューティング先を構成することによって、トレーニング実験を設定します。

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件](#prerequisites)として作成した config.json ファイルから `Workspace` オブジェクトを作成します。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>環境を定義する

`Environment` オブジェクトを作成し、Docker を有効にします。

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

次のコードで指定された基本イメージは、分散型ディープ ラーニング機能を可能にする fast.ai ライブラリをサポートしています。 詳細については、[fast.ai の Docker Hub リポジトリ](https://hub.docker.com/u/fastdotai)を参照してください。 

独自のカスタム Docker イメージを使用している場合は、既に Python 環境が適切に設定されている可能性があります。 その場合は、カスタム イメージの組み込みの Python 環境を使用するために、`user_managed_dependencies` フラグを `True` に設定します。 既定では、Azure Machine Learning では、指定した依存関係を持つ Conda 環境が構築されます。 サービスは、基本イメージにインストールした Python ライブラリを使用するのではなく、その環境でスクリプトを実行します。

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>プライベート コンテナー レジストリを作成する (省略可能)

ご自分のワークスペース内にないプライベート コンテナー レジストリのイメージを使用するには、`docker.base_image_registry` でリポジトリのアドレスとユーザー名とパスワードを指定します。

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>カスタム Dockerfile を使用する (省略可能)

カスタム Dockerfile を使用することも可能です。 Python 以外のパッケージを依存関係としてインストールする必要がある場合は、この方法を使用します。 基本イメージは必ず `None` に設定してください。

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning では、次のソフトウェアを提供する Docker イメージのみがサポートされています。
> * Ubuntu 16.04 以上
> * Conda 4.5.# 以上
> * Python 3.5 以降

Azure Machine Learning 環境の作成と管理の詳細について詳しくは、[ソフトウェア環境を作成して使用する](how-to-use-environments.md)方法に関するページを参照してください。 

### <a name="create-or-attach-a-compute-target"></a>コンピューティング先を作成またはアタッチする

モデルをトレーニングするには[コンピューティング先](concept-azure-machine-learning-architecture.md#compute-targets)を作成する必要があります。 このチュートリアルでは、トレーニング コンピューティング リソースとして `AmlCompute` を作成します。

`AmlCompute` の作成には数分かかります。 `AmlCompute` リソースがワークスペースに既にある場合、このコードの作成プロセスはスキップされます。

他の Azure サービスと同様に、Azure Machine Learning サービスに関連付けられている特定のリソース (たとえば `AmlCompute`) にも制限があります。 詳細については、[既定の制限と高いクォータを要求する方法](how-to-manage-quotas.md)に関するページを参照してください。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>トレーニング ジョブを構成する

このチュートリアルでは、[GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py) 上のトレーニング スクリプト *train.py* を使用します。 実際には、任意のカスタム トレーニング スクリプトを使用し、そのまま Azure Machine Learning で実行できます。

`ScriptRunConfig` リソースを作成し、目的の[コンピューティング先](how-to-set-up-training-targets.md)で実行されるようにジョブを構成します。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>トレーニング ジョブを送信する

`ScriptRunConfig` オブジェクトを使用してトレーニングの実行を送信すると、`submit` メソッドによって `ScriptRun` 型のオブジェクトが返されます。 返された `ScriptRun` オブジェクトを使用すると、トレーニング実行に関する情報へのプログラムによるアクセスが可能になります。 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning では、ソース ディレクトリ全体をコピーすることで、トレーニング スクリプトが実行されます。 アップロードしたくない機密データがある場合は、[.ignore ファイル](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)を使用するか、ソース ディレクトリに含めないようにします。 代わりに、[データストア](/python/api/azureml-core/azureml.data)を使用してデータにアクセスしてください。

## <a name="next-steps"></a>次の手順
この記事では、カスタム Docker イメージを使用してモデルをトレーニングしました。 Azure Machine Learning の詳細については、以下の他の記事をご覧ください。
* トレーニング中に[実行メトリックを追跡する](how-to-track-experiments.md)。
* カスタム Docker イメージを使用して[モデルをデプロイする](how-to-deploy-custom-docker-image.md)。
