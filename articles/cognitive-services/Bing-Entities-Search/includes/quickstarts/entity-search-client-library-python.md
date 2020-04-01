---
title: Bing Entity Search Python クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136601"
---
このクイックスタートは、Bing Entity Search の Python 用クライアント ライブラリからエンティティを検索するのに使用できます。 Bing Entity Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py) にあります。

## <a name="prerequisites"></a>前提条件

* Python [2.x または 3.x](https://www.python.org/)

* [Bing Entity Search SDK for Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Python の仮想環境を使用することをお勧めします。 仮想環境のインストールと初期化は、venv モジュールを使用して行えます。 次にようにして、virtualenv をインストールできます。

```Console
python -m venv mytestenv
```

Bing Entity Search クライアント ライブラリは、次のようにインストールします。

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

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
> [単一ページの Web アプリの作成](../../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../../overview.md )