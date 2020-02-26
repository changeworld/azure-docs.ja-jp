---
title: クイック スタート:Python 用の SDK を使用してエンティティを検索する - Bing Entity Search
titleSuffix: Azure Cognitive Services
description: Bing Entity Search SDK for Python を使用してエンティティを検索する場合は、このクイックスタートを使用します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: aahi
ms.openlocfilehash: a6b62f7ab95f7b2720434c0cf59cce33b0adb1b4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201239"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>クイック スタート:Bing Entity Search SDK (Python)

このクイック スタートを使用すると、Bing Entity Search SDK for Python でエンティティの検索を始めることができます。 Bing Entity Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py) にあります。

## <a name="prerequisites"></a>前提条件

* Python [2.x または 3.x](https://www.python.org/)

* [Bing Entity Search SDK for Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Python の仮想環境を使用することをお勧めします。 仮想環境のインストールと初期化は、venv モジュールを使用して行えます。 次にようにして、virtualenv をインストールできます。

```Console
python -m venv mytestenv
```

次のようにして、Bing Entity Search SDK をインストールします。

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. お気に入りの IDE またはエディターで新しい Python ファイルを作成し、次の import ステートメントを追加します。 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. 対象のサブスクリプション キーとエンドポイントの変数を作成します。 対象のキーを使用して新しい `CognitiveServicesCredentials` オブジェクトを作成して、クライアントをインスタンス化します。
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>検索要求を送信して応答を受信する

1. `client.entities.search()` と検索クエリを使用して、Bing Entity Search に検索要求を送信します。 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. エンティティが返された場合は、`entity_data.entities.value` をリストに変換して、最初の結果を表示します。
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../overview.md )