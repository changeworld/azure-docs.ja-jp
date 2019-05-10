---
title: GPU を使用して推論を行うためにディープ ラーニング モデルをデプロイする方法
titleSuffix: Azure Machine Learning service
description: ディープ ラーニング モデルを、推論に GPU が使用される Web サービスとしてデプロイする方法について説明します。 この記事では、TensorFlow モデルが Azure Kubernetes Service クラスターにデプロイされます。 このクラスターでは、Web サービスをホストして推論要求をスコア付けするために GPU 対応 VM を使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515039"
---
# <a name="how-to-do-gpu-inferencing"></a>GPU 推論を行う方法

Web サービスとしてデプロイされた機械学習モデルで GPU 推論を使用する方法について説明します。 この記事では、Azure Machine Learning service を使用して、TensorFlow ディープ ラーニング モデルのサンプルをデプロイする方法について説明します。 モデルは、GPU 対応 VM を使用してサービスをホストする Azure Kubernetes Service (AKS) クラスターにデプロイされます。 要求がサービスに送信されると、モデルで GPU が使用され、推論が実行されます。

GPU は、高度に並列化可能な計算におけるパフォーマンスについて、CPU を上回っています。 ディープ ラーニング モデルのトレーニングと推論は (特に要求のバッチが大きい場合)、GPU の優れたユース ケースです。  

この例では、TensorFlow で保存されたモデルを Azure Machine Learning にデプロイする方法を示します。 

## <a name="goals-and-prerequisites"></a>目標と前提条件

手順に従って次の操作を行います。
* GPU 対応 AKS クラスターを作成する
* Tensorflow-GPU を使用してモデルをデプロイする

前提条件:
* Azure Machine Learning service ワークスペース
* Python
* TensorFlow SavedModel が登録済み。 モデルを登録する方法については、[モデルのデプロイ](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)に関するページを参照してください

この記事は、[TensorFlow モデルの AKS へのデプロイ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb)に関する記事に基づいています。そこでは、TensorFlow で保存されたモデルを使用し、AKS クラスターにデプロイします。 ただし、スコアリング ファイルと環境ファイルをわずかに変更すれば、GPU をサポートするすべての機械学習フレームワークに適用可能です。  

## <a name="provision-aks-cluster-with-gpus"></a>GPU を備えた AKS クラスターをプロビジョニングする
Azure には多数の異なる GPU オプションがあり、それらはすべて、推論に使用できます。 機能とコストの完全な内訳については、[N シリーズの一覧](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)を参照してください。 

AKS と Azure Machine Learning service の使用の詳細については、[デプロイの方法と場所に関する記事](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)を参照してください。

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure では、AKS クラスターがプロビジョニングがされている限り、課金が行われます。 使い終わったら、必ず自分の AKS クラスターを削除してください。


## <a name="write-entry-script"></a>エントリ スクリプトを記述する

以下を `score.py` として自分の作業ディレクトリに保存します。 このファイルは、自分のサービスに送信された画像のスコア付けに使用されます。 このファイルによって、TensorFlow で保存されたモデルが読み込まれます。そして、各 POST 要求によって入力画像が TensorFlow セッションに渡され、結果スコアが返されます。
他の推論フレームワークでは、異なるスコアリング ファイルが必要になります。

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Conda 環境を定義する
`myenv.yml` という名前の Conda 環境ファイルを作成し、自分のサービスの依存関係を指定します。 `tensorflow-gpu` の使用を指定することは、パフォーマンスの高速化を実現するうえで重要です。
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>GPU InferenceConfig を定義する

GPU の有効化を指定する [`InferenceConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) を作成します。 これにより、CUDA がイメージに確実にインストールされます。

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

詳細については、[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) と [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) に関するページを参照してください。
## <a name="deploy-the-model"></a>モデルをデプロイする

モデルを自分の AKS クラスターにデプロイし、それによってサービスが作成されるのを待ちます。

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning service では、GPU を必要とする `InferenceConfig` があるモデルは、GPU がないクラスターにデプロイされません。

詳細については、[モデル](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)に関するページを参照してください。

## <a name="issue-sample-query-to-deployed-model"></a>デプロイされたモデルに対してサンプル クエリを発行する

デプロイされた自分のモデルに対してサンプル クエリを発行します。 このモデルでは、そこに POST 要求として送信する JPEG 画像がスコア付けされます。 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> 待機時間とスループットを最適化するために、ご利用のクライアントは、エンドポイントと同じ Azure リージョンに存在する必要があります。  現時点では、この API は、Azure の米国東部リージョンに作成されています。

## <a name="cleaning-up-the-resources"></a>リソースをクリーンアップする

デモの完了後、リソースを削除します。

> [!IMPORTANT]
> Azure では、AKS クラスターのデプロイ時間に基づいて課金が行われます。 これが完了した後は必ずクリーンアップしてください。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>次の手順

* [FPGA でモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [ONNX を使用してモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [TensorFlow DNN モデルをトレーニングする](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
