---
title: Video Search API クイック スタート | Microsoft Docs
description: Bing Video Search API の使用方法の概要について説明します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7E59692A-83A8-4F4C-B122-1F0EDC8E5C86
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 0bd0f067d64cac3ebac342ebadcfcc010a47af7b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376957"
---
# <a name="your-first-video-search-query"></a>初めてのビデオ検索クエリ

最初の呼び出しを行う前に、Bing Search Cognitive Services のサブスクリプション キーを入手する必要があります。 キーを取得するには、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api)」を参照してください。

ビデオ検索の結果を取得するには、次のエンドポイントに GET 要求を送信します。  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
要求では、HTTPS プロトコルを使う必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサードパーティがアクセスする可能性が高くなります。 また、サーバーから呼び出しを行うと、API の将来のバージョンでアップグレードする場所が 1 つで済みます。

  
要求では、ユーザーの検索語句が含まれている [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query) クエリ パラメーターを指定する必要があります。 必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#mkt) クエリ パラメーターも指定するべきです。このパラメーターは、結果取得元の市場を示します。 `pricing` などの省略可能なクエリ パラメーターの一覧については、「[Query Parameters (クエリ パラメーター)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters)」をご覧ください。 すべてのクエリ パラメーターの値は、URL でエンコードする必要があります。  
  
要求では、[Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#location)  

クライアント IP と場所のヘッダーは、場所に対応したコンテンツを返すために重要です。  

すべての要求ヘッダーと応答ヘッダーの一覧については、「[Headers (ヘッダー)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#headers)」を参照してください。


## <a name="the-request"></a>要求

すべての推奨されるクエリ パラメーターとヘッダーを含む検索要求を次に示します。 いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

前述の要求への応答は次のようになります。 例では、Bing に固有の応答ヘッダーも示されています。

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>次の手順

API を試してみます。 [Video Search API テスト コンソール](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8)に関するページを参照してください。 

応答オブジェクトの使用に関する詳細については、[Web でのビデオ検索](./search-the-web.md)に関するページを参照してください。

関連検索などのビデオに関する分析情報を取得する方法の詳細については、[ビデオの分析情報の取得](./video-insights.md)に関するページを参照してください。  
  
ソーシャル メディアで注目されているビデオの詳細については、[急上昇中の動画](./trending-videos.md)に関するページを参照してください。  
