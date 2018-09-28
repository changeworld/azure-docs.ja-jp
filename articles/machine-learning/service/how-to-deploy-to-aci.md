---
title: Azure Container Instances (ACI) への Web サービスのデプロイ - Azure Machine Learning
description: Azure Machine Learning サービスを利用して Azure Container Instances (ACI) にトレーニング済みモデルを Web サービスとしてデプロイする方法を説明します。 この記事では、ACI にモデルをデプロイする 3 つの方法を紹介します｡ それらの方法は､コードの行数と、デプロイ対象各部の命名時の自由度が異なります。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 5a62d4b0b324d8b2536e408132210f07f08e8bb8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958698"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Azure Container Instances への Web サービスのデプロイ 

[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) (ACI)､ [Azure Kubernetes Services](https://azure.microsoft.com/services/kubernetes-service/) (AKS)､IoT エッジ デバイス、あるいは [フィールド プログラミング可能なゲート アレイ (FPGA)](concept-accelerate-with-fpgas.md) にトレーニング済みモデルを Web サービスとしてデプロイできます｡ 

一般に ACI は AKS と比べて安価であり､4 ~ 6 行のコードで組むことができます｡ ACI は、テスト環境に最適な選択肢です。 後で、ハイスケールの本番環境用途にモデルと Web サービスを使用する準備ができると､[AKS にデプロイする](how-to-deploy-to-aks.md)ことができます｡

この記事では、ACI にモデルをデプロイする 3 つの方法を紹介します｡ それらの方法は､コードの行数と、デプロイ対象各部の命名時の自由度が異なります。 コード量と自由度が最小になる方法からコード量と自由度が最大になる方法まで､ACI の選択肢は次の通りです｡

* `Webservice.deploy()` を利用してモデル ファイルからデプロイする 
* `Webservice.deploy_from_model()` を利用して登録済みモデルからデプロイする
* `Webservice.deploy_from_image()` を利用してイメージから登録済みモデルをデプロイする

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。


## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペースと、Azure Machine Learning SDK for Python がインストール済みである｡ これら前提条件を取得する方法については､[Azure Machine Learning のクイック スタートの概要](quickstart-get-started.md)で説明しています｡

- Azure Machine Learning ワークスペース オブジェクト

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- デプロイするモデル このドキュメントの例では､"[モデルをトレーニング](tutorial-train-models-with-aml.md)" チュートリアルに従って作成したモデルを利用します｡ このモデルを利用しない場合は、モデルの名前を参照する手順を変更してください  また、モデルを実行するためのスコア スクリプトを作成する必要もあります。


## <a name="configure-an-image"></a>イメージを構成する

すべてのモデル ファイルの格納に使用する Docker イメージを構成します。
1. [説明に従って](tutorial-deploy-models-with-aml.md#create-scoring-script)スコア スクリプト (score.py) を作成します｡

1. [説明に従って](tutorial-deploy-models-with-aml.md#create-environment-file)環境ファイル (myenv.yml) を作成します｡ 

1. これら 2 つのファイルを使用し､次のように SDK を利用して Python で Docker イメージを構成します｡

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>ACI コンテナーを構成する

ACI コンテナーに必要な CPU 数と RAM ギガバイト数を指定することで ACI コンテナーを構成します｡ 多くのモデルの場合､既定値のコア 1 つと RAM 1 ギガバイトで十分です｡ 後でもっと必要になった場合は、イメージを再作成し、サービスをデプロイし直してください｡  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>モデルを登録する

> [モデル ファイル ](#deploy-from-model-file) (`Webservice.deploy()`) からデプロイする場合､この前提条件は省略してかまいません｡

[ `Webservice.deploy_from_model` ](#deploy-from-registered-model) または [ `Webservice.deploy_from_image`](#deploy-from-image) を使用するためのモデルを登録します｡ 登録済みのモデルが既にある場合は､ここでそのモデルを取得します。

### <a name="retrieve-a-registered-model"></a>登録済みのモデルを取得します。
モデルのトレーニングに Azure Machine Learning を利用している場合、モデルは既にワークスペースに登録されている可能性があります。  たとえば､[モデルをトレーニング](tutorial-train-models-with-aml.md)チュートリアルの最後の手順では､ モデルが登録されます。  デプロイ登録済みモデルを取得します。

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>モデル ファイルを登録する

モデルが他の場所でビルドされていても、ワークスペースに登録することができます。  モデルを登録するには､モデル ファイル (この例では `sklearn_mnist_model.pkl`) を現在の作業ディレクトリの置いておく必要があります。 そして､`Model.register()` を使い､`sklearn_mnist` という名前のモデルとしてそのファイルをワークスペースに登録します。
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```


## <a name="option-1-deploy-from-model-file"></a>オプション 1: モデル ファイルからデプロイする

モデル ファイルからデプロイする選択肢では､記述するコードは最小になります､コンポーネントの命名時の自由度も最小になります｡ この選択肢では､モデル ファイルからスタートして､そのファイルがワークスペースに登録されます。  ただし、モデルに名前を付けたり、タグや説明を関連付けたりすることはできません。  

この選択肢では、SDK メソッドの Webservice.deploy() を利用します。  

**推定所要時間**: デプロイに約 6 ~ 7 分を要します｡

1. モデル ファイルは、ローカルの作業ディレクトリに存在することを確認します。

1. 前提条件のモデル ファイル、score.py を開いて、`init()` セクションを変更します。

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. モデル ファイルをデプロイします｡

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. これで[web サービスをテスト](#test-web-service)できます｡

## <a name="option-2-deploy-from-registered-model"></a>オプション 2: 登録済みモデルからデプロイする

登録済みモデル ファイルをデプロイする選択肢では､もう少し多くの行のコードを受け取ります｡出力に対する命名には多少の自由度があります｡ この選択肢は、既にある登録済みモデルをデプロイする便利な方法です。  ただし、Docker イメージに名前を付けることはできません。  

この選択肢は SDK メソッドの Webservice.deploy_from_model() を利用します｡

**推定所要時間**: この選択肢でのデプロイに要する時間は約 8 分です｡

1. Docker コンテナーと ACI コンテナーを構成するコードを実行して､登録済みモデルを指定します｡

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. これで[web サービスをテスト](#test-web-service)できます｡

## <a name="option-3-deploy-from-image"></a>オプション 3: イメージからデプロイする

`Webservice.deploy_from_image()` を使用して登録済みモデル (`model`) をデプロイします。 この方法では､Docker イメージを個別に作成して、そのイメージからデプロイできます。

1. `ContainerImage.create()` を使って Docker イメージをビルドし､ワークスペースに登録する

    この方法では､独立した手順で作成されるためイメージに対する自由度が増します｡  登録済みモデル (`model`) はイメージに含まれています。
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**推定所要時間**: 約 3 分です。

1. `Webservice.deploy_from_image()` を使って Docker イメージをサービスとしてデプロイする

    ACI にイメージをデプロイします｡  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**推定所要時間**: 約 3 分です。

この方法は、デプロイ対象の作成とそのコンポーネントの命名に最大の自由度があります｡

これで Web サービスをテストできます。

## <a name="test-the-web-service"></a>Web サービスをテストする

Web サービスは、使用された方法に関係なく同じです。  予測情報を取得するには、サービスの `run` メソッドを利用します。  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>リソースのクリーンアップ

この web サービスを利用しないのであれば､料金が発生しないよう削除してください｡

```python
service.delete()
```

## <a name="next-steps"></a>次の手順

大規模なデプロイで [Azure Kubernetes Service にデプロイ](how-to-deploy-to-aks.md)する方法を学びます｡ 
