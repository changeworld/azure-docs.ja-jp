---
author: aahill
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b575c37bec045f63b4697a6ff87be4bcab7df6a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092079"
---
[リファレンス ドキュメント](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?preserve-view=true&view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [パッケージ (PiPy)](https://pypi.org/project/azure-ai-textanalytics/5.1.0/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Azure サブスクリプションを入手したら、Azure portal で<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="言語リソースを作成する"  target="_blank">言語リソースを作成</a>して、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 分析機能を使うには、Standard (S) 価格レベルの言語リソースが必要です。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install azure-ai-textanalytics==5.1.0
```

## <a name="code-example"></a>コードの例

新しい Python ファイルを作成し、下のコードをコピーします。 `key` 変数はリソースのキーに置き換え、`endpoint` 変数はリソースのエンドポイントに置き換えてください。 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```python
key = "paste-your-key-here"
endpoint = "paste-your-endpoint-here"

from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

# Authenticate the client using your key and endpoint 
def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()

# Example function for recognizing entities from text
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

## <a name="output"></a>出力

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```
