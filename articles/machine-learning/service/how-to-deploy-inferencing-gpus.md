---
title: GPU を使用した推論のためのモデルをデプロイする
titleSuffix: Azure Machine Learning service
description: 推論に GPU を使用する Web サービスとして、ディープ ラーニング モデルをデプロイする方法について説明します。 この記事では、TensorFlow モデルが Azure Kubernetes Service クラスターにデプロイされます。 このクラスターでは GPU 対応 VM を使用して、Web サービスをホストし、推論要求をスコア付けします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 64d42b9082895e372bb780d2db023294c1a0a380
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2019
ms.locfileid: "65884724"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU を使用した推論のためのディープ ラーニング モデルをデプロイする

Web サービスとしてデプロイされた機械学習モデルで GPU 推論を使用する方法について説明します。 推論、つまりモデル スコアリングとは、最も一般的には運用環境のデータに基づいて、デプロイしたモデルを使用して予測を行うフェーズです。

この記事では、Azure Machine Learning service を使用して、TensorFlow ディープ ラーニング モデルのサンプルを、GPU 対応仮想マシン (VM) 上の Azure Kubernetes Service (AKS) クラスターにデプロイする方法について説明します。 要求がサービスに送信されると、モデルは GPU を使用して推論ワークロードを実行します。

GPU は、高度に並列化可能な計算におけるパフォーマンスについて、CPU を上回っています。 GPU 対応仮想マシンの優れたユース ケースの 1 つとして、ディープ ラーニング モデルのトレーニングと推論を挙げることができます (特に要求のバッチが大きい場合)。

この例では、TensorFlow で保存されたモデルを Azure Machine Learning にデプロイする方法を示します。 次の手順を実行します。

* GPU 対応 AKS クラスターを作成する
* Tensorflow GPU モデルをデプロイする

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning service ワークスペース
* Python ディストリビューション
* TensorFlow で保存された登録済みモデル。 モデルを登録する方法については、[モデルのデプロイ](../service/how-to-deploy-and-where.md#registermodel)に関するページを参照してください。

この記事は、Jupyter Notebook の [AKS への Tensorflow モデルのデプロイ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb)に関するページに基づいています。 Jupyter Notebook では、TensorFlow で保存されたモデルを使用して、このモデルを AKS クラスターにデプロイします。 このノートブックは、スコアリング ファイルと環境ファイルをわずかに変更することで、GPU をサポートするすべての機械学習フレームワークに適用することもできます。  

## <a name="provision-an-aks-cluster-with-gpus"></a>GPU を備えた AKS クラスターをプロビジョニングする

Azure には、さまざまな GPU オプションが多数あります。 そのいずれも、推論に使用できます。 機能とコストの完全な内訳については、[N シリーズ VM の一覧](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)を参照してください。

AKS と Azure Machine Learning service の使用の詳細については、[デプロイの方法と場所](../service/how-to-deploy-and-where.md#deploy-aks)に関する記事を参照してください。

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

## <a name="write-the-entry-script"></a>エントリ スクリプトを記述する

次のコードを `score.py` として自分の作業ディレクトリに保存します。 画像が自分のサービスに送信されると、このファイルによって画像にスコアが付けられます。 TensorFlow で保存されたモデルが読み込まれ、各 POST 要求で入力画像が TensorFlow セッションに渡されて、結果のスコアが返されます。 他の推論フレームワークでは、異なるスコアリング ファイルが必要になります。

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

## <a name="define-the-conda-environment"></a>Conda 環境を定義する

`myenv.yml` という名前の Conda 環境ファイルを作成し、自分のサービスの依存関係を指定します。 パフォーマンスを高速化するには、`tensorflow-gpu` の使用を指定することが重要です。

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

## <a name="define-the-gpu-inferenceconfig-class"></a>GPU InferenceConfig クラスを定義する

GPU を有効にし、Docker イメージを使用して CUDA がインストールされるようにする `InferenceConfig` オブジェクトを作成します。

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

詳細については、次を参照してください。

- [InferenceConfig クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

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
> Azure Machine Learning service では、GPU の有効化が想定される `InferenceConfig` オブジェクトがあるモデルは、GPU がないクラスターにデプロイされません。

詳細については、「[Model class (Model クラス)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)」を参照してください。

## <a name="issue-a-sample-query-to-your-deployed-model"></a>デプロイされたモデルにサンプル クエリを発行する

デプロイされたモデルにテスト クエリを送信します。 モデルに jpeg 画像を送信すると、画像がスコア付けされます。

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> 待機時間を最小化し、スループットを最適化するために、ご利用のクライアントは、必ずエンドポイントと同じ Azure リージョンに存在するようにします。 この例では、API は米国東部 Azure リージョンに作成されています。

## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

この例を完了したら、リソースを削除します。

> [!IMPORTANT]
> Azure では、AKS クラスターがデプロイされている時間に基づいて課金が行われます。 これが完了した後は必ずクリーンアップしてください。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>次の手順

* [FPGA でモデルをデプロイする](../service/how-to-deploy-fpga-web-service.md)
* [ONNX を使用してモデルをデプロイする](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [TensorFlow DNN モデルをトレーニングする](../service/how-to-train-tensorflow.md)
