---
title: ML 実験の MLflow Tracking
titleSuffix: Azure Machine Learning
description: MLflow に Azure Machine Learning を設定してメトリックと成果物をログに記録し、Databricks、ローカル環境、または VM 環境からモデルをデプロイします。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 47d4c1de12823eaf0aae5beeff776d50f8f5a6a7
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896362"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>MLflow と Azure Machine Learning を使用してメトリックを追跡し、モデルをデプロイする (プレビュー)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、MLflow の追跡 URI とログ API (まとめて [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) と呼ばれる) を Azure Machine Learning で有効にする方法について説明します。 これを実行することで、以下のことが可能になります。

+ [Azure Machine Learning ワークスペース](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)で、実験のメトリックと成果物を追跡してログに記録します。 実験に MLflow Tracking を既に使用している場合、トレーニングのメトリックとモデルを保存するための一元化された安全でスケーラブルな場所がワークスペースに用意されています。

+ MLflow の実験を Azure Machine Learning Web サービスとしてデプロイします。 Web サービスとしてデプロイすることで、Azure Machine Learning の監視機能とデータ誤差検出機能を実稼働モデルに適用できます。 

[MLflow](https://www.mlflow.org) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 MLFlow Tracking は MLflow のコンポーネントです。実験の環境がリモートのコンピューティング先、仮想マシン、ローカル コンピューター、Azure Databricks クラスターのいずれであるかにかかわらず、トレーニング実行のメトリックとモデル成果物をログに記録し、追跡します。

次の図は、MLflow Tracking を使用して、実験の実行メトリックを追跡し、Azure Machine Learning ワークスペース内にモデル成果物を保存する例を示しています。

![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> このドキュメントの情報は主に、モデルのトレーニング プロセスを監視したいデータ サイエンティストや開発者を対象としています。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある管理者の方は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow と Azure Machine Learning のクライアントの比較

 次の表に、Azure Machine Learning を使用できるさまざまなクライアントとそれぞれの機能を示します。

 MLflow Tracking は、メトリックのログ機能と成果物の保存機能を提供します。他の方法では、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用している場合にのみこれらの機能を利用できます。


| | MLflow Tracking & Deployment | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| ワークスペースの管理 |   | ✓ | ✓ | ✓ |
| データ ストアの使用  |   | ✓ | ✓ | |
| メトリックのログ記録      | ✓ | ✓ |   | |
| 成果物のアップロード | ✓ | ✓ |   | |
| メトリックを表示する     | ✓ | ✓ | ✓ | ✓ |
| コンピューティングの管理   |   | ✓ | ✓ | ✓ |
| モデルをデプロイする    | ✓ | ✓ | ✓ | ✓ |
|モデル パフォーマンスを監視する||✓|  |   |
| データの誤差を検出する |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>前提条件

* [MLflow をインストール](https://mlflow.org/docs/latest/quickstart.html)します。
* ローカル コンピューターに [Azure Machine Learning SDK をインストール](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)します。この SDK により、MLflow がワークスペースにアクセスするための接続が提供されます。
* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)します。

## <a name="track-local-runs"></a>ローカル実行の追跡

Azure Machine Learning で MLflow Tracking を使用すると、ローカル実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。

Jupyter Notebook またはコード エディターでローカルで実行される Azure Machine Learning の実験で MLflow Tracking を使用するには、`azureml-contrib-run` パッケージをインストールします。

```shell
pip install azureml-mlflow
```

>[!NOTE]
>azureml.contrib 名前空間は、サービスの改善に伴って頻繁に変更されます。 そのため、この名前空間内のものはすべて Microsoft によって完全にサポートされているものではなく、プレビューとして見なされる必要があります。

MLflow の追跡 URI にアクセスし、ワークスペースを構成するには、`mlflow` および [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) クラスをインポートします。

次のコードでは、`get_mlflow_tracking_uri()` メソッドによって、ワークスペース `ws` に一意の追跡 URI アドレスを割り当て、`set_tracking_uri()` によって、MLflow の追跡 URI がそのアドレスを参照するよう設定します。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>追跡 URI の有効期間は最大 1 時間以内です。 一定のアイドル時間の経過後にスクリプトを再起動した場合は、get_mlflow_tracking_uri API を使用して新しい URI を取得してください。

`set_experiment()` で MLflow の実験名を設定し、`start_run()` でトレーニング実行を開始します。 次に、`log_metric()` を使用して MLflow ログ API をアクティブにし、トレーニング実行のメトリックのログ記録を開始します。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>リモート実行の追跡

Azure Machine Learning で MLflow Tracking を使用すると、リモート実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。

リモート実行では、GPU 対応仮想マシンや Machine Learning コンピューティング クラスターなど、より強力なコンピューティングでモデルをトレーニングできます。 さまざまなコンピューティング オプションについては、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)」をご覧ください。

[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) クラスを使用して、コンピューティングとトレーニングの実行環境を構成します。 `mlflow` および `azure-contrib-run` pip パッケージを、環境の [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) セクションに含めます。 次に、コンピューティング ターゲットとして、リモート コンピューティングで [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) を構築します。

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

トレーニング スクリプトで、`mlflow` をインポートして MLflow ログ API を使用し、実行のメトリックのログ記録を開始します。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

このコンピューティングおよびトレーニングの実行構成では、`Experiment.submit('train.py')` メソッドを使用して実行を送信します。 この方法により、MLflow の追跡 URI が自動的に設定され、MLflow からワークスペースにログが送信されます。

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks の実行を追跡する

Azure Machine Learning で MLflow Tracking を使用すると、Databricks の実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。

MLflow の実験を Azure Databricks で実行するには、[Azure Databricks ワークスペースとクラスター](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を最初に作成する必要があります

クラスターでは、PyPi から *azureml-mlflow* ライブラリを必ずインストールして、必要な関数とクラスにクラスターがアクセスできるようにします。
ここから、実験ノートブックをインポートして、それにクラスターをアタッチし、実験を実行します。 

### <a name="install-libraries"></a>ライブラリのインストール

クラスターにライブラリをインストールするには、 **[ライブラリ]** タブに移動して、 **[新規インストール]** をクリックします。

 ![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

**[パッケージ]** フィールドに azureml-mlflow と入力し、次に [インストール] をクリックします。 必要に応じてこの手順を繰り返して、実験のための他の追加パッケージをクラスターにインストールします。

 ![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>ノートブックとワークスペースのセットアップ

クラスターがセットアップされたら、実験ノートブックをインポートして開き、それにクラスターをアタッチします。

実験ノートブックには次のコードが含まれているはずです。 このコードにより、ワークスペースをインスタンス化するための Azure サブスクリプションの詳細が取得されます。 このコードは、既存のリソースグループと Azure Machine Learning ワークスペースがあることを前提としています。そうでない場合は、[それらを作成](how-to-manage-workspace.md)できます。 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks と Azure Machine Learning ワークスペースに接続する

[Azure portal](https://ms.portal.azure.com) で、Azure Databricks (ADB) ワークスペースを新規または既存の Azure Machine Learning ワークスペースにリンクできます。 これを行うには、ADB ワークスペースに移動し、右下の **[Link Azure Machine Learning workspace]\(Azure Machine Learning ワークスペースをリンク\)** を選択します。 ワークスペースをリンクすると、Azure Machine Learning ワークスペースで実験データを追跡できるようになります。 

### <a name="link-mlflow-tracking-to-your-workspace"></a>ワークスペースへの MLflow 追跡のリンク

ワークスペースをインスタンス化したら、MLflow の追跡 URI を設定します。 これにより、MLflow の追跡を Azure Machine Learning ワークスペースにリンクします。 リンク後、すべての実験は管理対象の Azure Machine Learning 追跡サービスに入れられます。

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>ノートブックで MLflow 追跡を直接設定する

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

トレーニング スクリプトで、MLflow をインポートして MLflow のログ API を使用し、実行メトリックのログ記録を開始します。 次の例では、エポック損失メトリックをログに記録します。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>MLflow 追跡の設定の自動化

クラスター上の後続の実験ノートブック セッションごとに追跡 URI を手動で設定する代わりに、この [Azure Machine Learning 追跡クラスターの Init スクリプト](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)を使用して、これを自動的に行います。

正しく構成されている場合は、Azure Machine Learning REST API およびすべてのクライアント、MLflow ユーザー インターフェイスからの Azure Databricks または MLflow クライアントを使用することで、自分の MLflow 追跡データを確認することができます。

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>ワークスペースでのメトリックと成果物の表示

MLflow ログ記録のメトリックと成果物は、お使いのワークスペースに保持されます。 それらを随時表示するには、[Azure Machine Learning Studio](https://ml.azure.com) でワークスペースに移動し、ワークスペースで名前によって実験を見つけます。  または、次のコードを実行します。 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.


## Example notebooks

The [MLflow with Azure ML notebooks](https://aka.ms/azureml-mlflow-examples) demonstrate and expand upon concepts presented in this article.

## Next steps
* [Manage your models](concept-model-management-and-deployment.md).
* Monitor your production models for [data drift](how-to-monitor-data-drift.md).
 -->