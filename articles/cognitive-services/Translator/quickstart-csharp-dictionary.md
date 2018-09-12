---
title: Translator Text と C# で翻訳の代替候補を探す | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Translator Text API と C# を使って、文脈中における用語の例と翻訳の代替候補を探します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: 3f45e7281456f9ae09912a2ee665cb480dc5052f
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "43770640"
---
# <a name="quickstart-find-alternate-translations-and-usage-with-c35"></a>クイック スタート: C&#35; を使って翻訳の代替候補と用法を探す

このクイック スタートでは、Translator Text API を使って、特定の用語の翻訳について考えられる代替候補の情報とそれらの代替候補の用例を探します。

## <a name="prerequisites"></a>前提条件

このコードを Windows 上で実行するには、[Visual Studio 2017](https://www.visualstudio.com/downloads/) が必要です。 (無料の Community Edition でかまいません。)

Translator Text API を使用するには、サブスクリプション キーも必要となります。「[Translator Text API にサインアップする方法](translator-text-how-to-signup.md)」を参照してください。

## <a name="dictionary-lookup-request"></a>辞書検索要求

以下のコードは、[Dictionary Lookup](./reference/v3-0-dictionary-lookup.md) メソッドを使って、単語の翻訳の代替候補を取得します。

1. 適切な IDE で新しい C# プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/dictionary/lookup?api-version=3.0";
        // Translate from English to French.
        static string params_ = "&from=en&to=fr";

        static string uri = host + path + params_;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string text = "great";

        async static void Lookup()
        {
            System.Object[] body = new System.Object[] { new { Text = text } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                var result = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(responseBody), Formatting.Indented);

                Console.OutputEncoding = UnicodeEncoding.UTF8;
                Console.WriteLine(result);
            }
        }

        static void Main(string[] args)
        {
            Lookup();
            Console.ReadLine();
        }
    }
}
```

## <a name="dictionary-lookup-response"></a>辞書検索応答

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>辞書の例の要求

以下のコードは、辞書に登録されている用語について、文脈中におけるその使い方の例を [Dictionary Examples](./reference/v3-0-dictionary-examples.md) メソッドを使って取得します。

1. 適切な IDE で新しい C# プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/dictionary/examples?api-version=3.0";
        // Translate from English to French.
        static string params_ = "&from=en&to=fr";

        static string uri = host + path + params_;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string text = "great";
        static string translation = "formidable";

        async static void Examples()
        {
            System.Object[] body = new System.Object[] { new { Text = text, Translation = translation } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                var result = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(responseBody), Formatting.Indented);

                Console.OutputEncoding = UnicodeEncoding.UTF8;
                Console.WriteLine(result);
            }
        }

        static void Main(string[] args)
        {
            Examples();
            Console.ReadLine();
        }
    }
}
```

## <a name="dictionary-examples-response"></a>辞書の例の応答

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>次の手順

このクイック スタートをはじめとする各種ドキュメントで翻訳と表記変換を含んだサンプル コードを詳しく見てみましょう。GitHub にある Translator Text の各種サンプル プロジェクトもご覧ください。

> [!div class="nextstepaction"]
> [GitHub で C# のコード例を詳しく見てみる](https://aka.ms/TranslatorGitHub?type=&language=c%23)
