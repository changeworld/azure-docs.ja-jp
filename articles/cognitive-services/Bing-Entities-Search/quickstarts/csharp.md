---
title: クイック スタート:C# を使用して REST API に検索要求を送信する - Bing Entity Search
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用すると、C# を使用して Bing Entity Search REST API に要求を送信し、JSON 応答を受信することができます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: aba090378ee459f6dd2747621321aa30bb9103b0
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110615217"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>クイック スタート:C# を使用して Bing Entity Search REST API に検索要求を送信する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

このクイック スタートを使用すると、Bing Entity Search API への最初の呼び出しを行い、JSON 応答を表示することができます。 このシンプルな C# アプリケーションは、新しい検索クエリを API に送信してその応答を表示します。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs) で入手できます。

このアプリケーションは C# で記述されていますが、この API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。


## <a name="prerequisites"></a>前提条件

- [Visual Studio 2017 またはそれ以降](https://www.visualstudio.com/downloads/)の任意のエディション。
- あるいは、Linux または MacOS を使用している場合は、[Visual Studio Code](https://code.visualstudio.com/) および [.NET Core](/dotnet/core/install/macos) を使用して、このクイックスタートに従うことができます
- [無料の Azure アカウント](https://azure.microsoft.com/free/dotnet)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. Visual Studio で、新しい C# コンソール ソリューションを作成します。 
1. [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet パッケージを追加します。
    1. **ソリューション エクスプローラー** でプロジェクトを右クリックします。
    2. **[NuGet パッケージの管理]** を選択します。
    3. *Newtonsoft.json* を探して選択し、パッケージをインストールします。
1. 次に、メイン コード ファイルに次の名前空間を追加します。
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. 新しいクラスを作成し、API エンドポイント、サブスクリプション キー、検索するクエリのための変数を追加します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.bing.microsoft.com";
            static string path = "/v7.0/search";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>要求を送信して API の応答を取得する

1. クラス内に、`Search()` という関数を作成します。 この関数内で、新しい `HttpClient` オブジェクトを作成し、`Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加します。

2. ホストとパスを組み合わせることで、要求の URI を作成します。 その後、市場を追加し、クエリを URL でエンコードします。

3. `client.GetAsync()` で HTTP 応答が取得されるまで待った後、`ReadAsStringAsync()` を待機することで JSON 応答を格納します。

4. JSON 文字列を `JsonConvert.DeserializeObject()` で書式設定し、コンソールに出力します。

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. アプリケーションの `Main()` メソッドで、`Search()` 関数を呼び出します。
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>JSON の応答例

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
        "url": "https://www.princi.com/",
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

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../overview.md )
* [Bing Entity Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)。
