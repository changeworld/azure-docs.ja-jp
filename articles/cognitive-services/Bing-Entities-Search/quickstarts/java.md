---
title: 'クイック スタート: Bing Entity Search API (Java)'
titlesuffix: Azure Cognitive Services
description: Bing Entity Search API をすぐに使い始めるのに役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: aahi
ms.openlocfilehash: 20a9e9b9c52d45c0f1c933f82e81d2d9a6f73edd
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313879"
---
# <a name="quickstart-for-bing-entity-search-api-with-java"></a>Bing Entity Search API のクイック スタート (Java) 

この記事では、Java で [Bing Entity Search](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)  API を使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

このコードをコンパイルして実行するには、[JDK 7 または 8](https://aka.ms/azure-jdks) が必要です。 好みの Java IDE がある場合はそれを使用してもかまいませんが、テキスト エディターで十分です。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Entity Search API** を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。または、Azure ダッシュボードの有料サブスクリプション キーを使用できます。  「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」も参照してください。

## <a name="search-entities"></a>エンティティの検索

このアプリケーションを削除するには、次の手順に従います。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (EntitySearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac EntitySearch.java -cp .;gson-2.8.1.jar
 * java -cp .;gson-2.8.1.jar EntitySearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class EntitySearch {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/entities";

    static String mkt = "en-US";
    static String query = "italian restaurant near me";

    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);

        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify (String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            String response = search ();
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**応答**

成功した応答は、次の例に示すように JSON で返されます。 

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
