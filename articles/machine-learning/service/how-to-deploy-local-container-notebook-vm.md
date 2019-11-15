---
title: ノートブック VM にモデルをデプロイする方法
titleSuffix: Azure Machine Learning
description: ノートブック VM を使用して、Web サービスとして Azure Machine Learning のモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: d4e37b02b3d7a21546a04c8948fbbfb7262bfa6a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584764"
---
# <a name="deploy-a-model-to-azure-machine-learning-notebook-vms"></a>Azure Machine Learning ノートブック VM にモデルをデプロイする

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning を使用して Azure Machine Learning ノートブック VM にモデルを Web サービスとしてデプロイする方法を説明します。 次のいずれかの条件に当てはまる場合は、ノートブック VM を使用します。

- モデルを迅速にデプロイおよび検証する必要があります。
- 開発中のモデルをテストします。

> [!TIP]
> ノートブック VM の Jupyter Notebook から、同じ VM 上の Web サービスへのモデルのデプロイは、"_ローカル デプロイ_" です。 この場合、"ローカル" コンピューターはノートブック VM です。 デプロイの詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Notebook VM が実行されている Azure Machine Learning のワークスペース。 詳細については、[環境とワークスペースの設定](tutorial-1st-experiment-sdk-setup.md)に関する記事を参照してください。

## <a name="deploy-to-the-notebook-vms"></a>ノートブック VM にデプロイする

ローカル デプロイを示すノートブックの例がノートブック VM に含まれています。 次の手順を使用してノートブックを読み込み、モデルを Web サービスとして VM にデプロイします。

1. [Azure Machine Learning Studio](https://ml.azure.com) から、Azure Machine Learning ノートブック VM を選択します。

1. `samples-*` サブディレクトリを開き、次に `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb` を開きます。 開いたら、ノートブックを実行します。

    ![ノートブックで実行されているローカル サービスのスクリーンショット](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. ノートブックには、サービスが実行されている URL とポートが表示されます。 たとえば、「 `https://localhost:6789` 」のように入力します。 また、`print('Local service port: {}'.format(local_service.port))` を含むセルを実行して、ポートを表示することもできます。

    ![実行されているローカル サービス ポートのスクリーンショット](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. ノートブック VM からサービスをテストするには、`https://localhost:<local_service.port>` URL を使用します。 リモート クライアントからテストするには、ノートブック VM で実行されているサービスのパブリック URL を取得します。 パブリック URL は、`https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score` という形式を使用して決定できます。 たとえば、「 `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score` 」のように入力します。

## <a name="test-the-service"></a>サービスをテストする

実行中のサービスにサンプル データを送信するには、次のコードを使用します。 値 `service_url` を、前の手順で取得した URL に置き換えます。

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>次の手順

* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)