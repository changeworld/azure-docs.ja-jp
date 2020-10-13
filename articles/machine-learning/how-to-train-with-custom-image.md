---
title: カスタム Docker イメージを使用してモデルをトレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でカスタム Docker イメージを使用してモデルをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8239d037d6bd68638998cbb36c47c7dac4bce30d
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537618"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>カスタム Docker イメージを使用してモデルをトレーニングする

この記事では、Azure Machine Learning でモデルをトレーニングするときに、カスタム Docker イメージを使用する方法について説明します。 

この記事のサンプル スクリプトは、畳み込みニューラル ネットワークを作成してペットの画像を分類するために使用されます。 

Azure Machine Learning では規定の Docker ベース イメージを提供していますが、Azure Machine Learning 環境を使用して、保守されている [Azure ML ベース イメージ](https://github.com/Azure/AzureML-Containers)のセットの 1 つや独自の[カスタム イメージ](how-to-deploy-custom-docker-image.md#create-a-custom-base-image)など、特定のベース イメージを指定することもできます。 カスタム ベース イメージを使用すると、依存関係を詳細に管理し、トレーニング ジョブの実行時にコンポーネントのバージョンを厳密に制御できます。 

## <a name="prerequisites"></a>前提条件 
このコードは、次の環境のいずれかで実行してください。
* Azure Machine Learning コンピューティング インスタンス - ダウンロードやインストールは必要なし
    * 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    * Azure Machine Learning の[例のリポジトリ](https://github.com/Azure/azureml-examples)で、このディレクトリに向けて **[how-to-use-azureml] > [ml-frameworks] > [fastai] > [train-with-custom-docker]** の順に移動して、完了したノートブックを検索します 

* 独自の Jupyter Notebook サーバー
    * [ワークスペース構成ファイル](how-to-configure-environment.md#workspace)を作成します。
    * [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。 
    * インターネット上でアクセスできる [Azure Container Registry](/azure/container-registry) またはその他の Docker レジストリ。

## <a name="set-up-the-experiment"></a>実験を設定する 
このセクションでは、ワークスペースを初期化し、実験を作成し、トレーニング データとトレーニング スクリプトをアップロードすることで、トレーニング実験を設定します。

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する
[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>スクリプトを準備する
このチュートリアルのトレーニング スクリプト **train.py** は、[こちら](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py)で提供されています。 実際には、任意のカスタム トレーニング スクリプトをそのまま使用し、Azure Machine Learning で実行できます。

### <a name="define-your-environment"></a>環境を定義する
環境オブジェクトを作成し、Docker を有効にします。 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

次に示す指定されたベース イメージは、分散型ディープ ラーニング機能を可能にする fast.ai ライブラリをサポートしています。 詳細については、[fast.ai DockerHub](https://hub.docker.com/u/fastdotai) を参照してください。 

独自のカスタム Docker イメージを使用している場合は、既に Python 環境が適切に設定されている可能性があります。 その場合は、カスタム イメージの組み込みの python 環境を活用するために、`user_managed_dependencies` フラグを True に設定します。 既定では、指定した依存関係を含む Conda 環境が Azure ML で構築され、その環境で実行が行われます。基本イメージにインストールされている Python ライブラリは使用されません。

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

ご自分のワークスペース内にない プライベート コンテナー レジストリ のイメージを使用するには、`docker.base_image_registry` でリポジトリのアドレスとユーザー名とパスワードを指定する必要があります。

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

カスタム Dockerfile を使用することも可能です。 Python 以外のパッケージを依存関係としてインストールする必要がある場合は、この方法を使用し、必ずベース イメージを None に設定します。

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Azure ML 環境の作成と管理に関する詳細については、[ソフトウェア環境の作成と使用](how-to-use-environments.md)に関する記事を参照してください。 

### <a name="create-or-attach-existing-amlcompute"></a>既存の AmlCompute の作成またはアタッチ
モデルをトレーニングするには[コンピューティング先](concept-azure-machine-learning-architecture.md#compute-targets)を作成する必要があります。 このチュートリアルでは、トレーニング コンピューティング リソースとして AmlCompute を作成します。

AmlCompute の作成には約 5 分かかります。 その名前の AmlCompute がワークスペースに既にある場合、このコードの作成プロセスはスキップされます。

他の Azure サービスと同様に、Azure Machine Learning service に関連付けられている特定のリソース (例: AmlCompute) にも制限があります。 既定の上限とクォータの追加を要求する方法については[こちら](how-to-manage-quotas.md)の記事をお読みください。 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig を作成する
この ScriptRunConfig は、目的の[コンピューティング ターゲット](how-to-set-up-training-targets.md)で実行するジョブを構成します。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>実行を送信する
ScriptRunConfig オブジェクトを使用してトレーニング実行が送信されると、submit メソッドによって ScriptRun 型のオブジェクトが返されます。 返された ScriptRun オブジェクトを使用すると、トレーニング実行に関する情報へのプログラムによるアクセスが可能になります。 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning では、ソース ディレクトリ全体をコピーすることで、トレーニング スクリプトが実行されます。 アップロードしたくない機密データがある場合は、[.ignore ファイル](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)を使用するか、ソース ディレクトリに含めないようにします。 代わりに、[データストア](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)を使用してデータにアクセスしてください。

## <a name="next-steps"></a>次のステップ
この記事では、カスタム Docker イメージを使用してモデルをトレーニングしました。 Azure Machine Learning の詳細については、以下の他の記事をご覧ください。
* トレーニング中に[実行メトリックを追跡する](how-to-track-experiments.md)
* カスタム Docker イメージを使用して[モデルをデプロイする](how-to-deploy-custom-docker-image.md)
