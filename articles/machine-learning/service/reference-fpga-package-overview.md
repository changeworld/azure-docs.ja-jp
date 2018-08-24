---
title: Azure Machine Learning 向けのハードウェア アクセラレーション用の FPGA パッケージ
description: Azure Machine Learning ユーザーが利用できる Python パッケージについて説明します。
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: a81f5f811058f3c7940da79419b9801225716e6b
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "42143024"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Azure Machine Learning Hardware Acceleration パッケージ

Azure Machine Learning Hardware Acceleration パッケージは Azure Machine Learning 向けの Python pip でインストール可能な拡張機能です。データ サイエンティストと AI 開発者は、この拡張機能を使用して以下を短時間で実行できます。

+ ResNet 50 の量子化されたバージョンで画像の特徴を抽出する

+ これらの機能に基づいて分類子をトレーニングする

+ モデルを Azure 上の [Field Programmable Gate Array (FPGA)](concept-accelerate-with-fpgas.md) にデプロイして、待機時間が極端に短い推論を行う

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

1. Azure Machine Learning Model Management アカウントを作成する必要があります。 アカウントを作成する方法の詳細については、[Azure Machine Learning のクイック スタートとワークベンチのインストール](../service/quickstart-installation.md)に関する記事を参照してください。 

1. このパッケージはインストールする必要があります。 

 
## <a name="how-to-install-the-package"></a>パッケージをインストールする方法

1. 最新バージョンの [Git](https://git-scm.com/downloads) をダウンロードしてインストールします。

2. [Anaconda (Python 3.6)](https://conda.io/miniconda.html) をインストールします。

   構成済みの Anaconda 環境をダウンロードするには、Git プロンプトで次のコマンドを使用します。

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
1. 環境を作成するには、**Anaconda Prompt** を開き、次のコマンドを使用します。

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

1. 環境をアクティブ化にするには、次のコマンドを使用します。

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>サンプル コード

このサンプル コードは、SDK を使用してモデルを FPGA に デプロイする手順を示しています。

1. パッケージをインポートします。
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. イメージを事前処理します。
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. 画像の特徴を抽出します。
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. 分類子を作成します。
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. サービス定義を作成します。
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. FPGA で実行するモデルを準備します。
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. FPGA で実行するモデルをデプロイします。
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. クライアントを作成します。
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. API を呼び出します。
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>問題の報告

[フォーラム](https://aka.ms/aml-forum)を使用して、パッケージで発生した問題を報告してください。

## <a name="next-steps"></a>次の手順

[モデルを Web サービスとして FPGA にデプロイする](how-to-deploy-fpga-web-service.md)