---
title: ML 実験の MLflow Tracking
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して MLflow を設定し、ML モデルからのメトリックと成果物をログに記録し、Web サービスとして ML モデルをデプロイします。
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 116faae1bc0a93ce2007fcf809a8c96475289036
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897193"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>MLflow と Azure Machine Learning を使用してモデル メトリックを追跡し、ML モデルをデプロイする (プレビュー)

この記事では、MLflow の追跡 URI とログ API (まとめて [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) と呼ばれる) を有効にして、MLflow の実験と Azure Machine Learning を接続する方法について説明します。 

MLflow 用の Azure Machine Learning ネイティブ サポートを使用すると、次のことができます。

+ [Azure Machine Learning ワークスペース](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)で、実験のメトリックと成果物を追跡してログに記録します。 実験に MLflow Tracking を既に使用している場合、トレーニングのメトリックとモデルを保存するための一元化された安全でスケーラブルな場所がワークスペースに用意されています。

+ Azure Machine Learning バックエンド サポートを備えた MLflow Projects を使用してトレーニング ジョブを送信します (プレビュー)。 Azure Machine Learning 追跡を使用してローカルにジョブを送信することも、[Azure Machine Learning コンピューティング](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute)を介するなどして実行をクラウドに移行することもできます。

+ MLflow および Azure Machine Learning モデル レジストリでモデルを追跡および管理します。

+ MLflow の実験を Azure Machine Learning Web サービスとしてデプロイします。 Web サービスとしてデプロイすることで、Azure Machine Learning の監視機能とデータ誤差検出機能を実稼働モデルに適用できます。 

[MLflow](https://www.mlflow.org) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 MLFlow Tracking は MLflow のコンポーネントです。これは、実験の環境がローカル コンピューター、リモートのコンピューティング先、仮想マシン、Azure Databricks クラスターのいずれであるかにかかわらず、トレーニング実行のメトリックとモデル成果物をログに記録し、追跡します。 

>[!NOTE]
> オープン ソース ライブラリである MLflow は頻繁に変更されます。 そのため、Azure Machine Learning と MLflow の統合によって利用できるようになる機能はプレビューとして見なす必要があり、Microsoft は完全にサポートしていません。

次の図は、MLflow Tracking を使用して、実験の実行メトリックを追跡し、Azure Machine Learning ワークスペース内にモデル成果物を保存する例を示しています。

![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> このドキュメントの情報は主に、モデルのトレーニング プロセスを監視したいデータ サイエンティストや開発者を対象としています。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある管理者の方は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow と Azure Machine Learning のクライアントの比較

 次の表に、Azure Machine Learning を使用できるさまざまなクライアントとそれぞれの機能を示します。

 MLflow Tracking は、メトリックのログ機能と成果物の保存機能を提供します。他の方法では、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) を使用している場合にのみこれらの機能を利用できます。

| 機能 | MLflow Tracking & Deployment | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
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
* ローカル コンピューターに [Azure Machine Learning SDK をインストール](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)します。この SDK により、MLflow がワークスペースにアクセスするための接続が提供されます。
* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)します。

## <a name="track-local-runs"></a>ローカル実行の追跡

Azure Machine Learning で MLflow Tracking を使用すると、ローカル実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。

Jupyter Notebook またはコード エディターでローカルで実行される Azure Machine Learning の実験で MLflow Tracking を使用するには、`azureml-mlflow` パッケージをインストールします。

```shell
pip install azureml-mlflow
```

MLflow の追跡 URI にアクセスし、ワークスペースを構成するには、`mlflow` および [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py&preserve-view=true) クラスをインポートします。

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

リモート実行では、GPU 対応仮想マシンや Machine Learning コンピューティング クラスターなど、より強力なコンピューティングでモデルをトレーニングできます。 さまざまなコンピューティング オプションについては、「[モデル トレーニング用のコンピューティング ターゲットを使用する](how-to-set-up-training-targets.md)」をご覧ください。

[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) クラスを使用して、コンピューティングとトレーニングの実行環境を構成します。 `mlflow` および `azureml-mlflow` pip パッケージを、環境の [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) セクションに含めます。 次に、コンピューティング ターゲットとして、リモート コンピューティングで [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) を構築します。

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

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

このコンピューティングおよびトレーニングの実行構成では、`Experiment.submit()` メソッドを使用して実行を送信します。 この方法により、MLflow の追跡 URI が自動的に設定され、MLflow からワークスペースにログが送信されます。

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>MLflow Projects を使用してトレーニングする

[MLflow Projects](https://mlflow.org/docs/latest/projects.html) を使用すると、他のデータ サイエンティスト (または自動化ツール) が実行できるようにするために、自分のコードの整理および記述を行うことができます。 Azure Machine Learning で MLflow Projects を使用すると、ワークスペースでのトレーニングの実行を追跡および管理することができます。 

この例では、Azure Machine Learning 追跡を使用して MLflow プロジェクトをローカルに送信する方法を示します。

実験において Azure Machine Learning で MLflow Tracking をローカルに使用するには、`azureml-mlflow` パッケージをインストールします。 実験は、Jupyter Notebook またはコード エディターを介して実行できます。

```shell
pip install azureml-mlflow
```

MLflow の追跡 URI にアクセスし、ワークスペースを構成するには、`mlflow` および [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) クラスをインポートします。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

`set_experiment()` で MLflow の実験名を設定し、`start_run()` でトレーニング実行を開始します。 次に、`log_metric()` を使用して MLflow ログ API をアクティブにし、トレーニング実行のメトリックのログ記録を開始します。

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

バックエンド構成オブジェクトを作成して、コンピューティング先や使用するマネージド環境の種類など、統合に必要な情報を格納します。

```python
backend_config = {"USE_CONDA": False}
```
ワークスペースのメトリックと重要な成果物を追跡するために、`azureml-mlflow` パッケージを pip 依存関係として環境構成ファイルに追加します。 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
ローカル実行を送信し、パラメーター `backend = "azureml" ` を確実に設定します。 この設定を使用すると、実行をローカルに送信して、自動出力追跡、ログ ファイル、スナップショット、およびエラーの出力といったサポートを自分のワークスペースに追加できます。 

[Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) で実行とメトリックを表示します。 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="track-azure-databricks-runs"></a>Azure Databricks の実行を追跡する

Azure Machine Learning で MLflow Tracking を使用すると、Azure Databricks の実行から、ログに記録されたメトリックと成果物を次の 3 つの領域すべてに一度に格納することができます。 

* Azure Machine Learning ワークスペース
* Azure Databricks ワークスペース
* MLflow

MLflow の実験を Azure Databricks で実行するには、[Azure Databricks ワークスペースとクラスター](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を最初に作成する必要があります。 クラスターで、PyPi から *azureml-mlflow* ライブラリをインストールして、必要な関数とクラスにクラスターが確実にアクセスできるようにします。

> [!NOTE]
> `azureml.core` パッケージには `azureml-mlflow`が含まれています。 `azureml.core` を既にインストールしている場合は、`azureml-mlflow` のインストール手順を省略できます。 

ここから、実験ノートブックをインポートして、Azure Databricks クラスターにアタッチし、実験を実行します。 

### <a name="install-libraries"></a>ライブラリのインストール

クラスターにライブラリをインストールするには、 **[ライブラリ]** タブに移動して、 **[新規インストール]** をクリックします。

 ![Azure Databricks での MLflow](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

**[パッケージ]** フィールドに azureml-mlflow と入力し、次に [インストール] をクリックします。 必要に応じてこの手順を繰り返して、実験のための他の追加パッケージをクラスターにインストールします。

 ![Azure DB install mlflow ライブラリ](./media/how-to-use-mlflow/install-libraries.png)

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

### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks と Azure Machine Learning ワークスペースに接続する

[Azure portal](https://ms.portal.azure.com) で、Azure Databricks (ADB) ワークスペースを新規または既存の Azure Machine Learning ワークスペースにリンクできます。 これを行うには、ADB ワークスペースに移動し、右下の **[Link Azure Machine Learning workspace]\(Azure Machine Learning ワークスペースをリンク\)** を選択します。 ワークスペースをリンクすると、Azure Machine Learning ワークスペースで実験データを追跡できるようになります。 

### <a name="mlflow-tracking-in-your-workspaces"></a>ワークスペースでの MLflow 追跡

ワークスペースのインスタンスを作成すると、MLflow 追跡が次のすべての場所で追跡されるように自動的に設定されます。

* リンクされた Azure Machine Learning ワークスペース。
* 元の ADB ワークスペース。 
* MLflow。 

実験はすべて、マネージド Azure Machine Learning 追跡サービスに配置されます。

#### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>ご利用の Azure Machine Learning ワークスペースでのみ追跡するように MLflow Tracking を設定する

追跡対象の実験を 1 か所で管理する場合は、Azure Machine Learning ワークスペースで**のみ**追跡するように MLflow 追跡を設定できます。 


```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

トレーニング スクリプトで、`mlflow` をインポートして MLflow ログ API を使用し、実行のメトリックのログ記録を開始します。 次の例では、エポック損失メトリックをログに記録します。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>ワークスペースでのメトリックと成果物の表示

MLflow ログ記録のメトリックと成果物は、お使いのワークスペースに保持されます。 それらを随時表示するには、[Azure Machine Learning Studio](https://ml.azure.com) でワークスペースに移動し、ワークスペースで名前によって実験を見つけます。  または、次のコードを実行します。 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>モデルを管理する 

MLflow モデル レジストリをサポートする [Azure Machine Learning モデル レジストリ](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere)にモデルを登録して追跡します。 Azure Machine Learning モデルは、MLflow モデル スキーマに対応しているため、これらのモデルをさまざまなワークフロー間で容易にエクスポートおよびインポートすることができます。 実行 ID などの MLflow 関連のメタデータには、追跡可能性のために、登録されたモデルのタグも付けられます。 ユーザーは、トレーニングの実行を送信し、MLflow の実行から生成されたモデルを登録およびデプロイすることができます。 

運用準備が整ったモデルを 1 つの手順でデプロイおよび登録したい場合は、「[MLflow モデルのデプロイと登録](#deploy-and-register-mlflow-models)」を参照してください。

実行からのモデルを登録して表示するには、次の手順に従います。

1. 実行が完了したら、`register_model()` メソッドを呼び出します。

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) を使用してワークスペースに登録されたモデルを表示します。

    次の例では、登録されたモデル `my-model` の MLflow 追跡メタデータがタグ付けされています。 

    ![registered-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. **[成果物]** タブを選択すると、MLflow モデル スキーマ (conda.yaml、MLmodel、model.pkl) に対応するすべてのモデル ファイルが表示されます。

    ![model-schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. [MLmodel] を選択すると、実行によって生成された MLmodel ファイルが表示されます。

    ![MLmodel-schema](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>MLflow モデルのデプロイと登録 

MLflow の実験を Azure Machine Learning Web サービスとしてデプロイすると、Azure Machine Learning のモデル管理およびデータ誤差検出の機能を活用して、運用モデルに適用することができます。

そのためには、次のことが必要です。

1. モデルを登録する。
1. シナリオで使用する展開構成を決定する。

    1. [Azure Container Instance (ACI)](#deploy-to-aci) は、迅速な開発テストのデプロイに適した選択肢です。
    1. [Azure Kubernetes Service (AKS)](#deploy-to-aks) は、スケーラブルな運用デプロイに適しています。

次の図は、MLflow デプロイ API を使用して、フレームワーク (PyTorch、Tensorflow、scikit-learn、ONNX など) にかかわらず既存の MLflow モデルを Azure Machine Learning Web サービスとしてデプロイでき、ワークスペース内で実稼働モデルを管理できることを示しています。

![ Azure Machine Learning を使用して MLflow モデルをデプロイする](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>ACI にデプロイする

[deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) メソッドを使用してデプロイ構成を設定します。 また、Web サービスの追跡に役立つタグや説明を追加することもできます。

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

次に、Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) メソッドを使用して、1 つの手順でモデルを登録し、デプロイします。 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>AKS にデプロイする

AKS にデプロイするには、まず AKS クラスターを作成します。 [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) メソッドを使用して、AKS クラスターを作成します。 新しいクラスターの作成には 20 分から 25 分かかる場合があります。

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
[deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) メソッドを使用してデプロイ構成を設定します。 また、Web サービスの追跡に役立つタグや説明を追加することもできます。

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

次に、Azure Machine Learning SDK [deploy()] を使用してモデルを 1 つの手順で登録し、デプロイします (つまり、Azure Machine Learning SDK の [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) メソッドを使用してモデルを登録し、デプロイします)。 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

サービスのデプロイには数分かかることがあります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ログに記録されたメトリックと成果物をワークスペースで使用する予定がない場合、現時点では、それらを個別に削除する機能は提供されていません。 代わりに、ストレージ アカウントとワークスペースを含むリソース グループを削除すれば、課金は発生しません。

1. Azure Portal で、左端にある **[リソース グループ]** を選択します。

   ![Azure portal で削除する](./media/how-to-use-mlflow/delete-resources.png)

1. 作成したリソース グループを一覧から選択します。

1. **[リソース グループの削除]** を選択します。

1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

## <a name="example-notebooks"></a>サンプルの Notebook

[Azure ML ノートブックでの MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) は、この記事で提示した概念を示し、さらに詳しく説明します。

## <a name="next-steps"></a>次のステップ

* [モデルを管理します](concept-model-management-and-deployment.md)。
* [データの誤差](how-to-monitor-data-drift.md)について実稼働モデルを監視します。
