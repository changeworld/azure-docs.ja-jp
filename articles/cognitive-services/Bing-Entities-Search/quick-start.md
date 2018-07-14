---
title: Entities Search API クイック スタート | Microsoft Docs
description: Bing Entities Search API の使用方法の概要について説明します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: B206A254-B7E9-49FF-AFD5-87B1E4D6D30B
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 12031d2447920c7e2d6180f35cf4fb29aa1b6150
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372928"
---
# <a name="making-your-first-entities-request"></a>最初のエンティティ要求を行う

Entity Search API は、Bing に検索クエリを送信して、エンティティと場所を含む検索結果を取得します。 場所の結果には、レストラン、ホテルやその他の地元企業が含まれます。 場所の場合、クエリではローカル ビジネスの名前を指定したり、一覧 (たとえば、近くのレストラン) を尋ねたりできます。 エンティティの結果には、人、場所、または物が含まれます。 このコンテキストでの場所とは、観光名所、州、国などです。 

## <a name="first-steps"></a>最初の手順

最初の呼び出しを行う前に、Cognitive Services のサブスクリプション キーを入手する必要があります。 キーの入手については、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api)」をご覧ください。 (Entities Search API が上部に表示されない場合は、**[検索]** タブをクリックし、表示されるまで下にスクロールします。)

## <a name="the-endpoint"></a>エンドポイント

エンティティおよび場所検索の結果を取得するには、次のエンドポイントに GET 要求を送信します。  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

要求では、HTTPS プロトコルを使う必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサード パーティがアクセスする可能性が高くなります。 また、サーバーから呼び出しを行うと、API の将来のバージョンでアップグレードする場所が 1 つで済みます。

## <a name="specifying-query-parameters-and-headers"></a>クエリ パラメーターとヘッダーの指定

要求では、ユーザーの検索用語が含まれている [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) クエリ パラメーターを指定する必要があります。 要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt) クエリ パラメーターも指定する必要があります。このパラメーターは、結果取得元の市場を示します。 省略可能なクエリ パラメーターの一覧については、「[Query Parameters](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters)」(クエリ パラメーター) をご覧ください。 すべてのクエリ パラメーターを URL エンコードします。  
  
要求では、[Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

クライアント IP と場所のヘッダーは、場所に対応したコンテンツを返すために重要です。  

すべての要求ヘッダーと応答ヘッダーの一覧は、「[Headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers)」(ヘッダー) をご覧ください。

## <a name="the-request"></a>要求

すべての推奨されるクエリ パラメーターとヘッダーを含むエンティティ要求を次に示します。 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

## <a name="the-response"></a>応答

前述の要求への応答は次のようになります。 例では、Bing に固有の応答ヘッダーも示されています。 応答オブジェクトについては、「[SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse)」をご覧ください。

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```



## <a name="next-steps"></a>次の手順

API を試してみます。 [Entities Search API テスト コンソール](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/)に関するページをご覧ください。 

応答オブジェクトの使用に関する詳細については、[Web でのエンティティと場所の検索](./search-the-web.md)に関するページをご覧ください。

検索結果の使用に関するルールを逸脱しないよう、[Bing の使用上および表示上の要件](./use-display-requirements.md)に関するページを必ず読んでください。
