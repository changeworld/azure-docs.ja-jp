---
title: News Search API クイック スタート | Microsoft Docs
description: Bing News Search API の使用方法の概要について説明します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 9CF6EAF3-42D8-4321-983C-4AC3896E8E03
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2aa1b3de07bd61eebfc1d410cda76c32fc7c958e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377384"
---
# <a name="your-first-news-search-query"></a>初めてのニュース検索クエリ

最初の呼び出しを行う前に、Cognitive Services のサブスクリプション キーを入手する必要があります。 キーの入手については、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)」をご覧ください。

ニュースだけの検索結果を取得するには、次のエンドポイントに GET 要求を送信します。

```http
https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

要求では、HTTPS プロトコルを使う必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサード パーティがアクセスする可能性が高くなります。 また、サーバーから呼び出しを行うと、API の将来のバージョンでアップグレードする場所が 1 つで済みます。

要求では、ユーザーの検索用語が含まれている [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query) クエリ パラメーターを指定する必要があります。 必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#mkt) クエリ パラメーターも指定するべきです。このパラメーターは、結果取得元の市場を示します。 `freshness` や `textDecorations` などの省略可能なクエリ パラメーターの一覧については、[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters)に関するセクションをご覧ください。 すべてのクエリ パラメーターの値は、URL エンコードされる必要があります。

要求では、[Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#location)

クライアント IP と場所のヘッダーは、場所に対応した内容を返すために重要です。

すべての要求ヘッダーと応答ヘッダーの一覧は、「[Headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headers)」(ヘッダー) をご覧ください。

## <a name="the-request"></a>要求

すべての推奨されるクエリ パラメーターとヘッダーを含むニュース要求を次に示します。 いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="the-response"></a>応答

前述の要求への応答は次のようになります。 例では、Bing に固有の応答ヘッダーも示されています。

```http
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "Sailing College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },
    {
        "name" : "Reunion at Fabrikam Lakes Sailing Club, celebrates 50 years...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.38210...",
                "width" : 650,
                "height" : 366
            }
        },
        "description" : "The reunion on April 29, at Fabrikam Lakes Sailing...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T13:08:00"
    },
    {
        "name" : "Sailing Club to host Dinghy Sailing World...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.364AB41...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    },
    {
        "name" : "A 24-Carat Dinghy",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.6CC2...",
                "width" : 700,
                "height" : 466
            }
        },
        "description" : "“Hard dinghies are for purists, the kind of people who...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-03T12:14:00",
        "category" : "Politics"
    }]
}
```

## <a name="next-steps"></a>次の手順

API を試してみます。 [News Search API のテスト コンソール](https://dev.cognitive.microsoft.com/docs/services/56b43f72cf5ff8098cef380a/operations/56f02400dbe2d91900c68553)に関するページをご覧ください。

応答オブジェクトの使用に関する詳細については、「[What is Bing News Search?](./search-the-web.md)」(Bing News Search とは) をご覧ください。 次の一般的なアクションについての詳細もわかります。

- [今日のトップ ニュースを取得する](./search-the-web.md#getting-todays-top-news)
- [カテゴリ別のニュースを取得する](./search-the-web.md#getting-news-by-category)
- [トレンドのニュースを取得する](./search-the-web.md#getting-trending-news)
