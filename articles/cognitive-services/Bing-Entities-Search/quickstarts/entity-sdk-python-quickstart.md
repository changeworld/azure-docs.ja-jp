---
title: クイック スタート:Bing Entity Search SDK for Python を使用してエンティティを検索する
titleSuffix: Azure Cognitive Services
description: Bing Entity Search SDK for Python を使用してエンティティを検索する場合は、このクイックスタートを使用します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 0a4a4e3ce6f318a693b8ee760a0d892eaecb2c82
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479020"
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
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. サブスクリプション キー用の変数を作成し、それで新しい `CognitiveServicesCredentials` オブジェクトを作成することによってクライアントをインスタンス化します。
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
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

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../overview.md )