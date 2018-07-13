---
title: Azure Cognitive Services、Bing Entity Search API の Python のクイック スタート | Microsoft Docs
description: Azure 上の Cognitive Services で Bing Entity Search API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 88e954af0254b158ea59a88ed4523e3b141a135e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377328"
---
# <a name="quickstart-for-microsoft-bing-entity-search-api-with-python"></a>Python での Microsoft Bing Entity Search API のクイック スタート 
<a name="HOLTop"></a>

この記事では、Python で [Bing Entity Search](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) API を使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

このコードを実行するには、[Python 3.x](https://www.python.org/downloads/) が必要です。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Entity Search API** を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。あるいは、Azure ダッシュボードの有料サブスクリプション キーを使用できます。

## <a name="search-entities"></a>エンティティの検索

このアプリケーションを削除するには、次の手順に従います。

1. 適切な IDE で新しい Python プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse
import json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/entities'

mkt = 'en-US'
query = 'italian restaurants near me'

params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)

def get_suggestions ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_suggestions ()
print (json.dumps(json.loads(result), indent=4))
```

**応答**

次の例で示すように、正常な応答が JSON で返されます。 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

[先頭に戻る](#HOLTop)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Entity Search のチュートリアル](../tutorial-bing-entities-search-single-page-app.md)
> [Bing Entity Search の概要](../search-the-web.md )
> [API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
