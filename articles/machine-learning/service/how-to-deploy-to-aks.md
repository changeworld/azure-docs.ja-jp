---
title: Azure Machine Learning サービスから Kubernetes にモデルをデプロイする |Microsoft Docs
description: Azure Machine Learning サービスから Azure Kubernetes Service にモデルをデプロイする方法について説明します。 モデルは Web サービスとしてデプロイされます。 Azure Kubernetes Service は、高スケールの運用ワークロードに適してします。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: efaaa196220213877283040120d6c2eeed86dbf3
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346322"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Azure Machine Learning サービスから Azure Kubernetes Service にモデルをデプロイする方法

高スケールの運用シナリオでは、Azure Kubernetes Service (AKS) にモデルをデプロイすることができます。 Azure Machine Learning では、デプロイ時に既存の AKS クラスターまたは新しく作成したクラスターを使用することができます。 モデルは Web サービスとして ASK にデプロイされます。

AKS にデプロイすることで、ご利用の Web サービスに関する、自動スケーリング、ログ記録、モデル データ収集、および高速応答時間が提供されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- Azure Machine Learning service ワークスペース、スクリプトを保存するローカル ディレクトリ、Azure Machine Learning SDK for Python のインストール。 これらの前提条件を満たす方法については、[開発環境を構成する方法](how-to-configure-environment.md)に関するドキュメントを参照してください。

- トレーニングされた機械学習モデル。 ない場合は、[イメージ分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するチュートリアルを参照してください。

## <a name="initialize-the-workspace"></a>ワークスペースを初期化する

ワークスペースを初期化するには、ワークスペース情報を含む `config.json` ファイルを読み込みます。

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>モデルを登録する

既存のモデルを登録するには、モデルのパス、説明、およびタグを指定します。

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Docker イメージを作成します。

Azure Kubernetes Service では Docker イメージを使用します。 イメージを作成するには、次の手順を使用します。

1. イメージを構成するには、スコアリング スクリプトと環境ファイルを作成する必要があります。 スクリプトと環境ファイルの作成例については、次のイメージ分類例のセクションを参照してください。

    * [スコアリング スクリプト (score.py) を作成する](tutorial-deploy-models-with-aml.md#create-scoring-script)

        > [!IMPORTANT]
        > スコア スクリプトでは、クライアントから送信されたデータが受信されて、スコア付けのためにモデルに渡されます。 スクリプトとモデルで予期されるデータ構造を文書化します。 このドキュメントを作成すると、Web サービスを使用するクライアントの作成が容易になります。

    * [環境ファイル (myenv.yml) を作成する](tutorial-deploy-models-with-aml.md#create-environment-file) 

   次の例では、これらのファイルを使用してイメージを構成します。

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. イメージを作成するには、モデルとイメージ構成を使用します。 この操作は、完了するまで約 5 分かかる場合があります。

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>AKS クラスターを作成する

次のコード スニペットでは、AKS クラスターを作成する方法を示します。 このプロセスは、完了するまで約 20 分かかります。

> [!IMPORTANT]
> AKS クラスターの作成は、ワークスペースの 1 回限りのプロセスです。 一度作成すれば、複数のデプロイでこのクラスターを再利用できます。 クラスターまたはそれを含むリソース グループを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>既存の AKS クラスターを接続する (省略可能)

Azure サブスクリプションに既存の AKS クラスターがある場合は、それを使用してイメージをデプロイできます。 次のコード スニペットでは、クラスターをワークスペースに接続する方法を示します。 

> [!IMPORTANT]
> サポートされるのは、AKS バージョン 1.11.2 のみです。

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

## <a name="deploy-your-web-service"></a>Web サービスをデプロイする

次のコード スニペットでは、AKS クラスターにイメージをデプロイする方法を示します。

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> デプロイ中にエラーが発生した場合は、`aks_service.get_logs()` を使用して AKS サービス ログを表示します。 ログに記録された情報に、エラーの原因が示されている可能性があります。

## <a name="test-the-web-service"></a>Web サービスをテストする

`aks_service.run()` を使用して、Web サービスをテストします。 次のコード スニペットでは、サービスにデータを渡して、予測を表示する方法を示しています。

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>クリーンアップ

サービス、イメージ、およびモデルを削除するには、次のコード スニペットを使用します。

```python
aks_service.delete()
image.delete()
model.delete()
```

## <a name="next-steps"></a>次の手順

[Web サービスとしてデプロイされる ML モデルを使用する](how-to-consume-web-service.md)方法を学習します。