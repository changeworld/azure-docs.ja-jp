---
author: aahill
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 827afb6a966583df98b435e33066dc2951d2c0d2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029977"
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

# Example function for recognizing entities and providing a link to an online data source
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>出力

```console
Linked Entities:

    Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
    Data Source:  Wikipedia
    Matches:
            Text: Microsoft
            Confidence Score: 0.55
            Offset: 0
            Length: 9
            Text: Microsoft
            Confidence Score: 0.55
            Offset: 168
            Length: 9
    Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
    Data Source:  Wikipedia
    Matches:
            Text: Bill Gates
            Confidence Score: 0.63
            Offset: 25
            Length: 10
            Text: Gates
            Confidence Score: 0.63
            Offset: 179
            Length: 5
    Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
    Data Source:  Wikipedia
    Matches:
            Text: Paul Allen
            Confidence Score: 0.60
            Offset: 40
            Length: 10
    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
    Data Source:  Wikipedia
    Matches:
            Text: April 4
            Confidence Score: 0.32
            Offset: 54
            Length: 7
    Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
    Data Source:  Wikipedia
    Matches:
            Text: BASIC
            Confidence Score: 0.33
            Offset: 98
            Length: 5
    Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
    Data Source:  Wikipedia
    Matches:
            Text: Altair 8800
            Confidence Score: 0.88
            Offset: 125
            Length: 11
```
