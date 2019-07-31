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
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: cae6039b904f3dcd19ed191dc1b5fdd2f05f0323
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260345"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning service を使用してモデルをデプロイする

Web サービスとして Azure クラウドに、または IoT Edge デバイスに機械学習モデルをデプロイする方法を説明します。 

モデルを[どこにデプロイするか](#target)に関係なく、ワークフローは同様です。

1. モデルを登録します。
1. デプロイの準備をします (アセット、使用、コンピューティング ターゲットを指定する)
1. コンピューティング ターゲットにモデルをデプロイします。
1. デプロイしたモデル (Web サービスとも呼ばれる) をテストします。

デプロイ ワークフローに関連する概念の詳細については、「[Azure Machine Learning service でモデルを管理、デプロイ、および監視する](concept-model-management-and-deployment.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- モデル。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で与えられたモデルと依存関係のファイルを使用できます。

- [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)、または [Azure Machine Learning Visual Studio Code 拡張機能](how-to-vscode-tools.md)。

## <a id="registermodel"></a> モデルを登録する

登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、ワークスペースに単一モデルとしてそれらを登録できます。 登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

機械学習モデルをご使用の Azure Machine Learning ワークスペースに登録します。 これらのモデルは、Azure Machine Learning から取得することも、どこか他の場所から取得することもできます。 次の例では、ファイルからモデルを登録する方法が示されています。

### <a name="register-a-model-from-an-experiment-run"></a>実験の実行からモデルを登録する

+ **SDK を使用した Scikit-Learn の例**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているディレクトリに設定します。

+ **CLI を使用する**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > モデルの登録に複数のファイルを含めるには、`--asset-path` を、それらのファイルが含まれているディレクトリに設定します。

+ **VS コードを使用する**

  [VS コード](how-to-vscode-tools.md#deploy-and-manage-models)拡張機能により、任意のモデル ファイルまたはフォルダーを使用してモデルを登録します。

### <a name="register-an-externally-created-model"></a>外部で作成されたモデルを登録する

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

モデルの**ローカル パス**を指定することで、外部で作成されたモデルを登録できます。 フォルダーまたは 1 個のファイルを提供できます。

+ **Python SDK による ONNX の例:**
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

  > [!TIP]
  > モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているディレクトリに設定します。

+ **CLI を使用する**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > モデルの登録に複数のファイルを含めるには、`-p` を、それらのファイルが含まれているディレクトリに設定します。

**推定所要時間**: 約 10 秒。

詳細については、[Model クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)のリファレンス ドキュメントを参照してください。

Azure Machine Learning service 以外でトレーニングされたモデルの使用の詳細については、[既存のモデルをデプロイする方法](how-to-deploy-existing-model.md)に関する記事を参照してください。

<a name="target"></a>

## <a name="choose-a-compute-target"></a>コンピューティング ターゲットを選択する

次のコンピューティング ターゲット、またはコンピューティング リソースを使用して、Web サービスのデプロイをホストすることができます。 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>デプロイを準備する

Web サービスとしてデプロイするには、推論構成 (`InferenceConfig`) とデプロイ構成を作成する必要があります。 推論、つまりモデル スコアリングとは、最も一般的には運用環境のデータについて、デプロイしたモデルを使用して予測を行うフェーズです。 推論構成には、モデルにサービスを提供するために必要なスクリプトと依存関係を指定します。 デプロイ構成には、コンピューティング ターゲットのモデルにサービスを提供する方法を詳しく指定します。


### <a id="script"></a> 1.エントリ スクリプトと依存関係を定義する

エントリ スクリプトは、デプロイされた Web サービスに送信されたデータを受け取り、それをモデルに渡します。 次に、モデルから返された応答を受け取り、それをクライアントに返します。 **このスクリプトはこのモデルに固有のものです**。そのため、モデルが受け入れ、返すデータを理解する必要があります。

このスクリプトにはモデルの読み込みと実行の 2 つの関数が含まれています。

* `init()`:通常は、この関数によってモデルがグローバル オブジェクトに読み込まれます。 この関数は、Web サービスの Docker コンテナーの起動時に 1 回だけ実行されます。

* `run(input_data)`:この関数では、モデルを使用して、入力データに基づいて値が予測されます。 実行に対する入力と出力は、通常、JSON を使用してシリアル化およびシリアル化解除が実行されます。 また、未加工のバイナリ データも使用できます。 モデルに送信する前、またはクライアントに返す前のデータを変換することができます。

#### <a name="what-is-getmodelpath"></a>Get_model_path とは何か

モデルを登録するときに、レジストリ内のモデルを管理するために使用されるモデル名を指定します。 この名前は、ローカル ファイル システム上のモデル ファイルのパスを取得する [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) で使用します。 フォルダーまたはファイルのコレクションを登録した場合、この API では、これらのファイルを含むディレクトリのパスが返されます。

モデルを登録する場合、ローカルに、またはサービスのデプロイ時に、モデルの配置場所に対応する名前を指定します。

下の例では (`sklearn_mnist` という名前で登録された) `sklearn_mnist_model.pkl` という名前の単一のファイルのパスが返されます。

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>(任意) Automatic Swagger スキーマ生成

Web サービスのスキーマを自動生成するには、定義された型オブジェクトのいずれかのコンストラクターに入力や出力のサンプルを指定します。型とサンプルはスキーマを自動生成するために使用されます。 その後、Azure Machine Learning サービスによって、デプロイ中に、Web サービスの [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 仕様が作成されます。

現在サポートされている型は次のとおりです。

* `pandas`
* `numpy`
* `pyspark`
* 標準的な Python オブジェクト

スキーマ生成を使用するには、Conda 環境ファイルに `inference-schema` パッケージを追加します。 エントリ スクリプトで numpy というパラメーターの型が使用されているため、次の例では `[numpy-support]` が使用されています。 

#### <a name="example-dependencies-file"></a>依存関係ファイルの例
次の YAML は、推論のための Conda 依存関係ファイルの例です。

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

自動スキーマ生成を使用する場合、エントリ スクリプトで `inference-schema` パッケージをインポートする**必要があります**。 

変数 `input_sample` と `output_sample` で入力と出力のサンプル形式を定義します。これは Web サービスの要求と応答の形式を表します。 これらのサンプルを、`run()` 関数の入力と出力の関数デコレーターで使用します。 下の scikit-learn の例では、スキーマ生成が使用されています。

> [!TIP]
> サービスのデプロイ後、`swagger_uri` プロパティを使用し、スキーマ JSON ドキュメントを取得します。

#### <a name="example-entry-script"></a>エントリ スクリプトの例

次の例では、JSON データを受け取り、返す方法が示されています。

```python
#example: scikit-learn and Swagger
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>ディクショナリ入力を使用したスクリプト例 (Power BI からの従量課金のサポート)

次の例では、Dataframe を使用して、<キー: 値> ディクショナリとして入力データを定義する方法を示しています。 この方法は、Power BI からのデプロイされた Web サービスの使用でサポートされています ([Power BI から Web サービスを使用する方法について](https://docs.microsoft.com/power-bi/service-machine-learning-integration))。

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType

def init():
    global model
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

@input_schema('data', PandasParameterType(input_sample))
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

推論構成では、予測するモデルを構成する方法が説明されます。 次の例では、推論構成を作成する方法が示されています。 この構成では、ランタイム、エントリ スクリプト、および (必要に応じて) Conda 環境ファイルを指定します。

```python
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

詳細については、[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) クラスのリファレンスを参照してください。

推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig の CLI の例

次の JSON ドキュメントは、Machine Learning CLI で使用する推定構成の例です。

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

このファイルでは次のエンティティが有効です。

* __entryScript__:イメージに対して実行するコードを含むローカル ファイルのパス。
* __runtime__:イメージに使用するランタイム。 現在サポートされているランタイムは "spark-py" と "python" です。
* __condaFile__ (省略可能):イメージに使用する Conda 環境定義を含むローカル ファイルのパス。
* __extraDockerFileSteps__ (省略可能):イメージを設定するときに実行する追加の Docker 手順を含むローカル ファイルのパス。
* __sourceDirectory__ (省略可能):イメージを作成するためのファイルをすべて含むフォルダーのパス。
* __enableGpu__ (省略可能):イメージで GPU サポートを有効にするかどうか。 GPU イメージは、Azure Container Instances、Azure Machine Learning コンピューティング、Azure Virtual Machines、Azure Kubernetes Service などの Microsoft Azure サービス上で使用する必要があります。 既定値は False です。
* __baseImage__ (省略可能):基本イメージとして使用するカスタム イメージ。 基本イメージが指定されていない場合、指定されたランタイム パラメーターに基づいた基本イメージが使用されます。
* __baseImageRegistry__ (省略可能):基本イメージを含むイメージ レジストリ。
* __cudaVersion__ (省略可能):GPU のサポートが必要なイメージにインストールする CUDA のバージョン。 GPU イメージは、Azure Container Instances、Azure Machine Learning コンピューティング、Azure Virtual Machines、Azure Kubernetes Service などの Microsoft Azure サービス上で使用する必要があります。 サポートされているバージョンは 9.0、9.1、10.0 です。 "enable_gpu" が設定されている場合、既定値は "9.1" です。

これらのエンティティは [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) クラスのパラメーターにマップされています。

次のコマンドでは、CLI を使用してモデルをデプロイする方法を示します。

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

この例では、構成に次の項目が含まれています。

* 推論に必要なアセットが含まれるディレクトリ
* このモデルには Python が必要であるということ
* [エントリ スクリプト](#script)。デプロイされたサービスに送信される Web 要求の処理に必要です。
* 推論を行うために必要な Python パッケージを記述する conda ファイル

推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

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

### <a name="optional-profile-your-model"></a>省略可能:モデルをプロファイリングする

モデルをサービスとしてデプロイする前に、SDK または CLI を利用し、最適な CPU とメモリの要件を判断するためにプロファイリングを実行できます。  モデルのプロファイル結果は、`Run` オブジェクトとして出力されます。 [モデル プロファイル スキーマについては、API ドキュメントに](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)詳しい記述があります

SDK を使用してモデルをプロファイリングする方法については、[こちら](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)のページを参照してください。

CLI を使用してモデルをプロファイリングするには、[az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) を使用します。

## <a name="deploy-to-target"></a>ターゲットにデプロイする

### <a id="local"></a> ローカル デプロイ

ローカルにデプロイするには、お使いのローカル コンピューターに **Docker がインストールされている**必要があります。

+ **SDK を使用する**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **CLI を使用する**

    CLI を使用してデプロイするには、次のコマンドを使用します。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    `deploymentconfig.json` ドキュメントのエントリは、[LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py) のパラメーターにマッピングされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

    | JSON エンティティ | メソッド パラメーター | 説明 |
    | ----- | ----- | ----- |
    | `computeType` | NA | コンピューティング ターゲット。 ローカルの場合、値は `local` である必要があります。 |
    | `port` | `port` | サービスの HTTP エンドポイントを公開するローカル ポート。 |

    次の JSON は、CLI で使用するデプロイ構成の例です。

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a> Azure Container Instances (DEVTEST)

以下の条件が 1 つ以上満たされている場合は、Azure Container Instances を使用してお客様のモデルを Web サービスとしてデプロイします。
- モデルを迅速にデプロイおよび検証する必要があります。
- 開発中のモデルをテストします。 

ACI の利用可能なクォータとリージョンを確認するには、[Azure Container Instances のクォータとリージョンの可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)に関する記事を参照してください。

+ **SDK を使用する**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **CLI を使用する**

    CLI を使用してデプロイするには、次のコマンドを使用します。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。 このサービスに付ける名前に `myservice` を置き換えます。

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    `deploymentconfig.json` ドキュメントのエントリは、[AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py) のパラメーターにマッピングされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

    | JSON エンティティ | メソッド パラメーター | 説明 |
    | ----- | ----- | ----- |
    | `computeType` | NA | コンピューティング ターゲット。 ACI の場合、値は `ACI` である必要があります。 |
    | `containerResourceRequirements` | NA | コンテナーに割り当てられている CPU とメモリの構成要素が含まれています。 |
    | &emsp;&emsp;`cpu` | `cpu_cores` | この Web サービスに割り当てる CPU コアの数。 既定値、`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | この Web サービスに割り当てるメモリの量 (GB 単位)。 既定値、`0.5` |
    | `location` | `location` | この Web サービスのデプロイ先となる Azure リージョン。 指定されていない場合、ワークスペースの場所が使用されます。 利用できるリージョンの詳細はこちらにあります。[ACI リージョン](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | この Web サービスに対して認証を有効にするかどうか。 既定値は False です |
    | `sslEnabled` | `ssl_enabled` | この Web サービスに対して SSL を有効にするかどうか。 既定値は False です。 |
    | `appInsightsEnabled` | `enable_app_insights` | この Web サービスに対して AppInsights を有効にするかどうか。 既定値は False です |
    | `sslCertificate` | `ssl_cert_pem_file` | SSL が有効な場合、証明書ファイルが必要です |
    | `sslKey` | `ssl_key_pem_file` | SSL が有効な場合、キー ファイルが必要です |
    | `cname` | `ssl_cname` | SSL が有効な場合の cname |
    | `dnsNameLabel` | `dns_name_label` | スコアリング エンドポイントの dns 名ラベル。 指定されていない場合、一意の dns 名ラベルがスコアリング エンドポイントに対して生成されます。 |

    次の JSON は、CLI で使用するデプロイ構成の例です。

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **VS コードを使用する**

  [VS コードを使用してモデルをデプロイする](how-to-vscode-tools.md#deploy-and-manage-models)には、事前に ACI コンテナーを作成してテストする必要はありません。ACI コンテナーはその場で作成されるからです。

詳細については、[AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) クラスと [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) クラスのリファレンス ドキュメントを参照してください。

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST & PRODUCTION)

既存の AKS クラスターを使用するか、Azure Machine Learning SDK、CLI、または Azure portal を使用して新しいクラスターを作成できます。

<a id="deploy-aks"></a>

AKS クラスターが既にアタッチされている場合は、それにデプロイできます。 AKS クラスターを作成またはアタッチしていない場合は <a href="#create-attach-aks">新規 AKS クラスターを作成する</a>プロセスに従ってください。

+ **SDK を使用する**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **CLI を使用する**

    CLI を使用してデプロイするには、次のコマンドを使用します。 AKS コンピューティング先の名前に `myaks` を置き換えます。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。 このサービスに付ける名前に `myservice` を置き換えます。

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    `deploymentconfig.json` ドキュメントのエントリは、[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) のパラメーターにマッピングされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

    | JSON エンティティ | メソッド パラメーター | 説明 |
    | ----- | ----- | ----- |
    | `computeType` | NA | コンピューティング ターゲット。 AKS の場合、値は `aks` である必要があります。 |
    | `autoScaler` | NA | 自動スケーリングの構成要素が含まれます。 自動スケーラー テーブルを参照してください。 |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Web サービスの自動スケールを有効にするかどうかを指定します。 `numReplicas` = `0` の場合、`True`。それ以外の場合、`False`。 |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | この Web サービスを自動スケールするときに使用するコンテナーの最小数。 既定値、`1`。 |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | この Web サービスを自動スケールするときに使用するコンテナーの最大数。 既定値、`10`。 |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自動スケーラーがこの Web サービスのスケーリングを試行する頻度。 既定値、`1`。 |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | オートスケーラーがこの web サービスに対してメンテナンスを試行する目標使用率 (最大 100%)。 既定値、`70`。 |
    | `dataCollection` | NA | データ収集の構成要素が含まれます。 |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | Web サービスに対してモデル データ収集を有効にするかどうかを指定します。 既定値、`False`。 |
    | `authEnabled` | `auth_enabled` | Web サービスに対して認証を有効にするかどうかを指定します。 既定値、`True`。 |
    | `containerResourceRequirements` | NA | コンテナーに割り当てられている CPU とメモリの構成要素が含まれています。 |
    | &emsp;&emsp;`cpu` | `cpu_cores` | この Web サービスに割り当てる CPU コアの数。 既定値、`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | この Web サービスに割り当てるメモリの量 (GB 単位)。 既定値、`0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | Web サービスに対して Application Insights ログ記録を有効にするかどうかを指定します。 既定値、`False`。 |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | Web サービス呼び出しのスコア付けに適用するタイムアウト。 既定値、`60000`。 |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | この Web サービスのノードあたり最大同時要求数。 既定値、`1`。 |
    | `maxQueueWaitMs` | `max_request_wait_time` | 要求がキューに留まる最大時間 (ミリ秒単位)。この時間を過ぎると、503 エラーが返されます。 既定値、`500`。 |
    | `numReplicas` | `num_replicas` | この Web サービスに割り当てるコンテナーの数。 既定値はありません。 このパラメーターが設定されていない場合、自動スケーラーは既定で有効になります。 |
    | `keys` | NA | キーの構成要素が含まれます。 |
    | &emsp;&emsp;`primaryKey` | `primary_key` | この Web サービスに使用するプライマリ認証キー |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | この Web サービスに使用するセカンダリ認証キー |
    | `gpuCores` | `gpu_cores` | この Web サービスに割り当てる GPU コアの数。 既定値は 1 です。 |
    | `livenessProbeRequirements` | NA | liveness probe 要件の構成要素が含まれます。 |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | liveness probe を実行する頻度 (秒単位)。 既定値は 10 秒です。 最大値は 1 です。 |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | コンテナーの起動後、liveness probe が開始するまでの秒数。 既定値は 310 です |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | liveness probe がタイムアウトするまでの秒数既定値は 2 秒です。 最小値は 1 です |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | 失敗後、liveness probe が成功と見なされるための最小連続成功数。 既定値は 1 です。 最大値は 1 です。 |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | Pod が起動し、liveness probe が失敗したとき、Kubernetes では、failureThreshold 回数だけ試し、それからあきらめます。 既定値は 3 です。 最大値は 1 です。 |
    | `namespace` | `namespace` | Web サービスのデプロイ先となる Kubernetes 名前空間。 最大 63 個の小文字の英数字 ('a'-'z'、'0'-'9') とハイフン ('-') 文字。 先頭と末尾の文字をハイフンにすることはできません。 |

    次の JSON は、CLI で使用するデプロイ構成の例です。

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **VS コードを使用する**

  また、[VS コード拡張機能を介して AKS にデプロイする](how-to-vscode-tools.md#deploy-and-manage-models)こともできますが、事前に AKS クラスターを構成しておく必要があります。

AKS デプロイおよびオートスケーリングの詳細情報については、[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) のリファレンスをご覧ください。

#### 新しい AKS クラスターを作成する<a id="create-attach-aks"></a>
**推定所要時間**: 約 20 分です。

AKS クラスターの作成またはアタッチは、お使いのワークスペースでの 1 回限りのプロセスです。 複数のデプロイでこのクラスターを再利用できます。 クラスターまたはそれを含むリソース グループを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。 複数の AKS クラスターをワークスペースに接続できます。

開発、検証、およびテスト用に AKS クラスターを作成する場合、[`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) を使用するときに `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` を設定します。 この設定で作成されたクラスターにはノードは 1 つだけになります。

> [!IMPORTANT]
> `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` を設定すると、運用トラフィックの処理には適していない AKS クラスターが作成されます。 推論時間は、実稼働用に作成されたクラスターよりも長くなることがあります。 フォールト トレランスも開発/テスト クラスターでは保証されません。
>
> 開発/テスト用に作成されたクラスターでは、少なくとも 2 つの仮想 CPU を使用することをお勧めします。

次の例では、新しい Azure Kubernetes Service クラスターを作成する方法が紹介されています。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
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

`cluster_purpose` パラメーターの詳細については、[AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) のリファレンスを参照してください。

> [!IMPORTANT]
> [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) で、agent_count と vm_size にカスタム値を使用する場合は、agent_count と vm_size の積が 12 仮想 CPU 以上である必要があります。 たとえば、vm_size に "Standard_D3_v2" (4 仮想 CPU) を使用する場合、agent_count は 3 以上にする必要があります。
>
> Azure Machine Learning SDK では、AKS クラスターのスケーリングのサポートは提供されません。 クラスター内のノードをスケーリングするには、Azure portal 内でご自分の AKS クラスターの UI を使用します。 クラスターの VM サイズではなく、ノード数のみを変更できます。

#### <a name="attach-an-existing-aks-cluster"></a>既存の AKS クラスターをアタッチする
**推定所要時間:** 約 5 分です。

お客様の Azure サブスクリプションに既に AKS クラスターがあり、そのバージョンが 1.12.## である場合は、それを使用してお客様のイメージをデプロイできます。

> [!WARNING]
> AKS クラスターをワークスペースに接続するときに、`cluster_purpose` パラメーターを設定することにより、クラスターを使用する方法を定義できます。
>
> `cluster_purpose` パラメーターを設定しない場合、または `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` を設定した場合は、クラスターでは最低 12 の仮想 CPU が利用できる必要があります。
>
> `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` を設定した場合、クラスターには 12 の仮想 CPU は必要ありません。 ただし、開発/テスト用に構成されたクラスターは、運用レベルのトラフィックには適しておらず、推論時間が増えることがあります。

次のコードは、既存の AKS 1.12.## クラスターをお客様のワークスペースにアタッチする方法を示しています。

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

`attack_configuration()` の詳細については、[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) のリファレンスを参照してください。

`cluster_purpose` パラメーターの詳細については、[AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) のリファレンスを参照してください。

## <a name="consume-web-services"></a>Web サービスを使用する

デプロイされた Web サービスはすべて、REST API を提供します。そのため、さまざまなプログラミング言語でクライアント アプリケーションを作成できます。 サービスに対して認証を有効にしている場合、要求ヘッダーにトークンとしてサービス キーを指定する必要があります。

### <a name="request-response-consumption"></a>要求 - 応答の使用量

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


### <a id="azuremlcompute"></a> バッチ推論
Azure Machine Learning のコンピューティング ターゲットは、Azure Machine Learning service によって作成され、管理されます。 これは Azure Machine Learning パイプラインからのバッチ予測に使用できます。

Azure Machine Learning コンピューティングを使用したバッチ推論のチュートリアルについては、[バッチ予測を実行する方法](how-to-run-batch-predictions.md)に関する記事を参照してください。

### <a id="iotedge"></a> IoT Edge の推論
エッジにデプロイするためのサポートは現在、プレビューの段階にあります。 詳細については、[IoT Edge モジュールとして Azure Machine Learning をデプロイする](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)方法に関する記事を参照してください。


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

## <a name="continuous-model-deployment"></a>継続的なモデル デプロイ 

[Azure DevOps](https://azure.microsoft.com/services/devops/) 用の Machine Learning 拡張機能を使用して、モデルを継続的にデプロイできます。 Azure DevOps 用の Machine Learning 拡張機能を使用すると、新しい機械学習モデルが Azure Machine Learning service ワークスペースに登録されたときにデプロイ パイプラインをトリガーできます。 

1. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) にサインアップします。そうすると、任意のプラットフォームや任意のクラウドへのご自分のアプリケーションの継続的インテグレーションとデリバリーを実現できます。 Azure Pipelines は [ML パイプライン](concept-ml-pipelines.md#compare)とは異なります。 

1. [Azure DevOps プロジェクトを作成します。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. [Azure Pipelines 用の Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)をインストールします。 

1. ご自分のすべての成果物にアクセスするために、__サービス接続__を使用して、ご自分の Azure Machine Learning service ワークスペースへのサービス プリンシパル接続を設定します。 プロジェクト設定に移動し、[サービス接続] をクリックして、[Azure Resource Manager] を選択します。

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. __スコープ レベル__として AzureMLWorkspace を定義し、以降のパラメーターを入力します。

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. 次に、Azure Pipelines を使用してご自分の機械学習モデルを継続的にデプロイするために、パイプラインの下で __[リリース]__ を選択します。 新しい成果物を追加し、AzureML Model 成果物と前の手順で作成したサービス接続を選択します。 デプロイをトリガーするモデルとバージョンを選択します。 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. ご自分のモデル成果物に対してモデル トリガーを有効にします。 トリガーをオンにすると、そのモデルの指定したバージョン (つまり 最新バージョン) がご自分のワークスペースに登録されるたびに、Azure DevOps のリリース パイプラインがトリガーされます。 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

サンプル プロジェクトと例については、[MLOps リポジトリ](https://github.com/Microsoft/MLOps)をご確認ください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
デプロイされた Web サービスを削除するには、`service.delete()` を使用します。
登録済みのモデルを削除するには、`model.delete()` を使用します。

詳細については、[WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) と [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) のリファレンス ドキュメントを参照してください。

## <a name="next-steps"></a>次の手順
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)

