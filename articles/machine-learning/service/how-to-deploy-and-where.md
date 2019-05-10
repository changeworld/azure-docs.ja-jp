---
title: モデルをデプロイする方法と場所
titleSuffix: Azure Machine Learning service
description: お客様の Azure Machine Learning service モデルをデプロイする方法と場所について説明します (Azure Container Instances、Azure Kubernetes Service、Azure IoT Edge、フィールド プログラマブル ゲート アレイ)。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 1da232c2a81c9989cc78eccf1be97b5d75a48666
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024482"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning service を使用してモデルをデプロイする

Web サービスとして Azure クラウドに、または IoT Edge デバイスに機械学習モデルをデプロイする方法を説明します。 このドキュメントの情報では、次のコンピューティング ターゲットにデプロイする方法が説明されています。

| コンピューティング ターゲット | デプロイの種類 | 説明 |
| ----- | ----- | ----- |
| [ローカル Web サービス](#local) | テスト/デバッグ | 制限付きのテストとトラブルシューティングに最適。
| [Azure Kubernetes Service (AKS)](#aks) | リアルタイムの推論 | 高スケールの運用デプロイに適しています。 自動スケール、および高速な応答時間を実現します。 |
| [Azure Container Instances (ACI)](#aci) | テスト | CPU ベースの小規模なワークロードに最適。 |
| [Azure Machine Learning コンピューティング](how-to-run-batch-predictions.md) | (プレビュー) バッチ推論 | サーバーレス コンピューティングでバッチ スコアリングを実行します。 優先順位が中程度または低い VM をサポートします。 |
| [Azure IoT Edge](#iotedge) | (プレビュー) IoT モジュール | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |

## <a name="deployment-workflow"></a>デプロイのワークフロー

モデルのデプロイのプロセスは、すべてのコンピューティング先で同様です。

1. モデルを登録します。
1. モデルをデプロイします。
1. デプロイされたモデルをテストします。

デプロイ ワークフローに関連する概念の詳細については、「[Azure Machine Learning service でモデルを管理、デプロイ、および監視する](concept-model-management-and-deployment.md)」を参照してください。

## <a name="prerequisites-for-deployment"></a>デプロイの前提条件

- モデル。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](http://aka.ms/azml-deploy-cloud)で与えられたモデルと依存関係のファイルを使用できます。

- [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)または [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)。

## <a id="registermodel"></a> 機械学習モデルの登録

モデル レジストリは、トレーニング済みのモデルを Azure クラウドに格納して整理するための方法の 1 つです。 モデルは Azure Machine Learning service ワークスペースに登録されています。 モデルは Azure Machine Learning を利用してトレーニングされた、または別の場所でトレーニングされたモデルからインポートできます。 次の例では、ファイルからモデルを登録する方法が示されています。

### <a name="register-a-model-from-an-experiment-run"></a>実験の実行からモデルを登録する

**CLI による Scikit-Learn の例**
```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
```
**SDK を使用する**
```python
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

### <a name="register-an-externally-created-model"></a>外部で作成されたモデルを登録する
モデルの**ローカル パス**を指定することで、外部で作成されたモデルを登録できます。 フォルダーまたは 1 個のファイルを提供できます。

**Python SDK による ONNX の例:**
```python
onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
!tar xvzf mnist.tar.gz

model = Model.register(workspace = ws,
                       model_path ="mnist/model.onnx",
                       model_name = "onnx_mnist",
                       tags = {"onnx": "demo"},
                       description = "MNIST image classification CNN from ONNX Model Zoo",)
```

**CLI を使用する**
```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

**推定所要時間**: 約 10 秒。

詳細については、[Model クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)のリファレンス ドキュメントを参照してください。

## <a name="how-to-deploy"></a>デプロイ方法

Web サービスとしてデプロイするには、推論構成 (`InferenceConfig`) とデプロイ構成を作成する必要があります。 推論構成には、モデルにサービスを提供するために必要なスクリプトと依存関係を指定します。 デプロイ構成には、コンピューティング ターゲットのモデルにサービスを提供する方法を詳しく指定します。


### <a id="script"></a> 1.エントリ スクリプトと依存関係を定義する

エントリ スクリプトは、デプロイされた Web サービスに送信されたデータを受け取り、それをモデルに渡します。 次に、モデルから返された応答を受け取り、それをクライアントに返します。 **このスクリプトはこのモデルに固有のものです**。そのため、モデルが受け入れ、返すデータを理解する必要があります。

このスクリプトにはモデルの読み込みと実行の 2 つの関数が含まれています。

* `init()`:通常は、この関数によってモデルがグローバル オブジェクトに読み込まれます。 この関数は、Web サービスの Docker コンテナーの起動時に 1 回だけ実行されます。

* `run(input_data)`:この関数では、モデルを使用して、入力データに基づいて値が予測されます。 実行に対する入力と出力は、通常、JSON を使用してシリアル化およびシリアル化解除が実行されます。 また、未加工のバイナリ データも使用できます。 モデルに送信する前、またはクライアントに返す前のデータを変換することができます。

#### <a name="optional-automatic-swagger-schema-generation"></a>(任意) Automatic Swagger スキーマ生成

Web サービスのスキーマを自動生成するには、定義された型オブジェクトのいずれかのコンストラクターに入力や出力のサンプルを指定します。型とサンプルはスキーマを自動生成するために使用されます。 その後、Azure Machine Learning サービスによって、デプロイ中に、Web サービスの [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 仕様が作成されます。

現在サポートされている型は次のとおりです。

* `pandas`
* `numpy`
* `pyspark`
* 標準的な Python オブジェクト

スキーマ生成を使用するには、Conda 環境ファイルに `inference-schema` パッケージを追加します。 エントリ スクリプトで numpy というパラメーターの型が使用されているため、次の例では `[numpy-support]` が使用されています。 

#### <a name="example-dependencies-file"></a>依存関係ファイルの例
次は、推論のための Conda 依存関係ファイルの例です。
```python
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

自動スキーマ生成を使用する場合、エントリ スクリプトで `inference-schema` パッケージをインポートする**必要があります**。 

変数 `input_sample` と `output_sample` で入力と出力のサンプル形式を定義します。これは Web サービスの要求と応答の形式を表します。 これらのサンプルを、`run()` 関数の入力と出力の関数デコレーターで使用します。 下の scikit-learn の例では、スキーマ生成が使用されています。

> [!TIP]
> サービスのデプロイ後、`swagger_uri` プロパティを使用し、スキーマ JSON ドキュメントを取得します。

#### <a name="example-entry-script"></a>エントリ スクリプトの例

次の例では、JSON データを受け取り、返す方法が示されています。

**Swagger 生成による Scikit-learn の例:**
```python
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType

def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
output_sample = np.array([3726.995])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

他にもスクリプトの例が必要であれば、次のサンプルをご覧ください。

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* バイナリ データに対するスコアリング:[Web サービスを使用する方法](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2.InferenceConfig を定義する

推論構成では、予測するモデルを構成する方法が説明されます。 次の例では、推論構成を作成する方法が示されています。

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

この例では、構成に次の項目が含まれています。

* 推論に必要なアセットが含まれるディレクトリ
* このモデルには Python が必要であるということ
* [エントリ スクリプト](#script)。デプロイされたサービスに送信される Web 要求の処理に必要です。
* 推論に必要な Python パッケージを説明する conda ファイル

InferenceConfig 機能の詳細については、「[詳細な構成設定](#advanced-config)」セクションを参照してください。

### <a name="3-define-your-deployment-configuration"></a>手順 3.デプロイ構成を定義する

デプロイの前にデプロイ構成を定義する必要があります。 デプロイ構成は、Web サービスがホストされるコンピューティング ターゲットに固有となります。 たとえば、ローカルでデプロイするとき、サービスにより要求が受け取られるポートを指定する必要があります。

また、コンピューティング リソースを作成する必要があります。 たとえば、Azure Kubernetes Service を自分のワークスペースにまだ関連付けていない場合です。

次の表には、コンピューティング ターゲット別にデプロイ構成を作成する例があります。

| コンピューティング ターゲット | デプロイ構成の例 |
| ----- | ----- |
| ローカル | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

次のセクションでは、デプロイ構成を作成し、それを利用して Web サービスをデプロイする方法が紹介されています。

## <a name="where-to-deploy"></a>デプロイする場所

### <a id="local"></a> ローカルにデプロイする

このセクションの例では [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) を使用します。これには、デプロイを実行する前にモデルとイメージを登録する必要があります。 その他のデプロイ方法の詳細については、「[deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-)」および「[deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)」を参照してください。

**ローカルにデプロイするには、Docker をローカル コンピューターにインストールする必要があります。**

**SDK を使用する**

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**CLI を使用する**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

### <a id="aci"></a>Azure Container Instances へのデプロイ (DEVTEST)

以下の条件が 1 つ以上満たされている場合は、Azure Container Instances を使用してお客様のモデルを Web サービスとしてデプロイします。
- モデルを迅速にデプロイおよび検証する必要があります。
- 開発中のモデルをテストします。 

ACI の利用可能なクォータとリージョンを確認するには、[Azure Container Instances のクォータとリージョンの可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)に関する記事を参照してください。

**SDK を使用する**

```python
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**CLI を使用する**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
```

詳細については、[AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) クラスと [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) クラスのリファレンス ドキュメントを参照してください。

### <a id="aks"></a>Azure Kubernetes Service へのデプロイ (PRODUCTION)

既存の AKS クラスターを使用するか、Azure Machine Learning SDK、CLI、または Azure portal を使用して新しいクラスターを作成できます。


> [!IMPORTANT]
> AKS クラスターの作成は、ワークスペースに対する 1 回限りのプロセスです。 複数のデプロイでこのクラスターを再利用できます。
> AKS クラスターを作成またはアタッチしていない場合、<a href="#create-attach-aks">こちら</a>にお進みください。

#### AKS にデプロイする <a id="deploy-aks"></a>

Azure ML CLI を使用して AKS にデプロイできます。
```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Python SDK を使用することもできます。
```python
aks_target = AksCompute(ws,"myaks")
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

自動スケーリングなど、AKS デプロイ構成の詳細については、[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) のリファレンスをご覧ください。

**推定所要時間:** 約 5 分です。

#### AKS クラスターを作成またはアタッチする <a id="create-attach-aks"></a>
AKS クラスターの作成またはアタッチは、ワークスペースに対する **1 回限りのプロセス**です。 クラスターがワークスペースに関連付けられたら、それを複数のデプロイで使用できます。 

クラスターまたはそれを含むリソース グループを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。

##### <a name="create-a-new-aks-cluster"></a>新しい AKS クラスターを作成する
`autoscale_target_utilization`、`autoscale_max_replicas`、および `autoscale_min_replicas` の設定の詳細については、[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) リファレンスを参照してください。
次の例では、新しい Azure Kubernetes Service クラスターを作成する方法が紹介されています。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Azure Machine Learning SDK の外部で AKS クラスターを作成する方法の詳細については、次の記事を参照してください。
* [AKS クラスターの作成](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS クラスターの作成 (ポータル)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)


> [!IMPORTANT]
> [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) で、agent_count と vm_size にカスタム値を使用する場合は、agent_count と vm_size の積が 12 仮想 CPU 以上である必要があります。 たとえば、vm_size に "Standard_D3_v2" (4 仮想 CPU) を使用する場合、agent_count は 3 以上にする必要があります。

**推定所要時間**: 約 20 分です。

##### <a name="attach-an-existing-aks-cluster"></a>既存の AKS クラスターをアタッチする

お客様の Azure サブスクリプションに既に AKS クラスターがあり、そのバージョンが 1.12.## であり、12 以上の仮想 CPU を備えている場合は、それを使用してお客様のイメージをデプロイできます。 次のコードは、既存の AKS 1.12.## クラスターをお客様のワークスペースにアタッチする方法を示しています。

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

## <a name="consume-web-services"></a>Web サービスを使用する
デプロイされた Web サービスはすべて、REST API を提供します。そのため、さまざまなプログラミング言語でクライアント アプリケーションを作成できます。 サービスに対して認証を有効にしている場合、要求ヘッダーにトークンとしてサービス キーを指定する必要があります。

Python でサービスを呼び出す方法の例:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

詳細については、[Web サービスを使用するクライアント アプリケーションの作成](how-to-consume-web-service.md)に関するページを参照してください。

## <a id="update"></a> Web サービスを更新する

新しいモデルを作成する場合、新しいモデルを使用する各サービスを手動で更新する必要があります。 Web サービスを更新するには、`update` メソッドを使用します。 次のコードでは、Web サービスを更新して新しいモデルを使用する方法が示されています。

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="clean-up"></a>クリーンアップ
デプロイされた Web サービスを削除するには、`service.delete()` を使用します。
登録済みのモデルを削除するには、`model.delete()` を使用します。

詳細については、[WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) と [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) のリファレンス ドキュメントを参照してください。

## 詳細な構成設定 <a id="advanced-config"></a>

### <a id="customimage"></a> カスタム基本イメージの使用

内部的には、InferenceConfig によって、サービスに必要なモデルと他のアセットが含まれる Docker イメージが作成されます。 指定されていない場合、既定の基本イメージが使用されます。

推論構成と共に使用するイメージを作成するとき、イメージは次の要件を満たす必要があります。

* Ubuntu 16.04 以上
* Conda 4.5.# 以上
* Python 3.5.# または 3.6.#

カスタム イメージを使用するには、推論構成の `base_image` プロパティをイメージのアドレスに設定します。 次の例は、パブリックとプライベートの両方の Azure Container Registry からイメージを使用する方法を示しています。

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

次のイメージ URI は Microsoft から提供されるイメージ向けであり、ユーザー名やパスワードの値を指定しなくても使用できます。

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

これらのイメージを使用するには、`base_image` を上記のリストにある URI に設定します。 `base_image_registry.address` を `mcr.microsoft.com` に設定します。

> [!IMPORTANT]
> CUDA または TensorRT を使用する Microsoft イメージを使用できるのは Microsoft Azure サービスのみです。

Azure Container Registry に独自のイメージをアップロードする方法の詳細については、[プライベート Docker コンテナー レジストリへの最初のイメージのプッシュ](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)に関するページを参照してください。

モデルを Azure Machine Learning コンピューティングでトレーニングする場合、__バージョン 1.0.22 以上__の Azure Machine Learning SDK を使用して、トレーニング時にイメージが作成されます。 次の例は、このイメージを使用する方法を示しています。

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="other-inference-options"></a>その他の推論オプション

### <a id="azuremlcompute"></a> バッチ推論
Azure Machine Learning のコンピューティング ターゲットは、Azure Machine Learning service によって作成され、管理されます。 これは Azure Machine Learning パイプラインからのバッチ予測に使用できます。

Azure Machine Learning コンピューティングを使用したバッチ推論のチュートリアルについては、[バッチ予測を実行する方法](how-to-run-batch-predictions.md)に関する記事を参照してください。

## <a id="iotedge"></a> IoT Edge での推論
エッジにデプロイするためのサポートは現在、プレビューの段階にあります。 詳細については、[IoT Edge モジュールとして Azure Machine Learning をデプロイする](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-machine-learning)方法に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
