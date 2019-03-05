---
title: クイック スタート:Bing Spell Check REST API と C# を使用してスペルをチェックする
titlesuffix: Azure Cognitive Services
description: Bing Spell Check REST API を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a33dfe2e20cdb6c1944d4be89692ec1da5a5482e
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889666"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>クイック スタート:Bing Spell Check REST API と C# を使用してスペルをチェックする

このクイック スタートを使用して、Bing Spell Check の REST API を呼び出してみましょう。 このシンプルな C# アプリケーションは、API に要求を送信して、一連の修正候補を返します。 このアプリケーションは C# で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) の任意のエディション。
* NuGet パッケージとして入手できる [Json.NET](https://www.newtonsoft.com/json) フレームワーク。
* Linux/macOS を使用している場合、このアプリケーションは [Mono](http://www.mono-project.com/) を使用して実行できます。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. Visual Studio で、`SpellCheckSample` という新しいコンソール ソリューションを作成します。 次に、メイン コード ファイルに次の名前空間を追加します。
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. API エンドポイント、サブスクリプション キー、スペル チェックの対象テキストのための変数を作成します。

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "enter your key here";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. 検索パラメーターの変数を作成します。 該当する市場コードを `mkt=` に追加し、スペルチェック モードを `&mode=` に追加します。
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>スペル チェック要求を作成して送信する

1. API に要求を送信するための `SpellCheck()` という非同期関数を作成します。 `HttpClient` を作成し、`Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加します。 この関数内で、次の手順を実行します。

    ```csharp
    async static void SpellCheck()
    {
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = new HttpResponseMessage();
        //...
    }

2. Create the URI for your request by appending your host, path, and parameters. 
    
    ```csharp
    string uri = host + path + params_;
    ```

3. 対象テキストを含んだ `KeyValuePair` オブジェクトのリストを作成し、それを使用して `FormUrlEncodedContent` オブジェクトを作成します。 ヘッダー情報を設定し、`PostAsync()` を使用して要求を送信します。

    ```csharp
    List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
    values.Add(new KeyValuePair<string, string>("text", text));
    
    using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
    {
        content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
        response = await client.PostAsync(uri, content);
    }
    ```

## <a name="get-and-print-the-api-response"></a>API の応答を取得して出力する

### <a name="get-the-client-id-header"></a>クラアント ID ヘッダーを取得する

応答に `X-MSEdge-ClientID` ヘッダーが含まれている場合は、その値を取得して出力します。

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>応答を取得する

API から応答を取得します。 JSON オブジェクトを逆シリアル化し、それをコンソールに出力します。

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>スペル チェック関数を呼び出す

プロジェクトの Main 関数で `SpellCheck()` を呼び出します。

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>JSON の応答例

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [シングル ページ Web アプリを作成する](../tutorials/spellcheck.md)

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
