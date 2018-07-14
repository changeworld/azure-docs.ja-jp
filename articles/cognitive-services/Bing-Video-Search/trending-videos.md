---
title: 急上昇中の動画を Web で検索する | Microsoft Docs
description: Bing Video Search API を使用して Web で急上昇中の動画を検索する方法を示します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 897A28A3-0980-484E-814F-FFE1D5C885E6
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8db7fcf77042631260b4b165bd3d44053827f3ce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372784"
---
# <a name="get-trending-videos"></a>急上昇中の動画の取得  

今日の急上昇中の動画を取得するには、次の GET 要求を送信します。  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

次の市場は、急上昇中の動画をサポートしています。  
 
-   en-AU (英語、オーストラリア)  
-   en-CA (英語、カナダ)  
-   en-GB (英語、英国)  
-   en-ID (英語、インドネシア)  
-   en-IE (英語、アイルランド)  
-   en-IN (英語、インド)  
-   en-NZ (英語、ニュージーランド)  
-   en-PH (英語、フィリピン)  
-   en-SG (英語、シンガポール)  
-   en-US (英語、米国)  
-   en-WW (英語、世界全域の集約コード)  
-   en-ZA (英語、南アフリカ)  
-   zh-CN (中国語、中国)

  
次の例は、急上昇中の動画を含む応答を示しています。  

```  
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```  
応答には、カテゴリおよびサブカテゴリごとの動画の一覧が含まれています。 たとえば、カテゴリの一覧にミュージック ビデオ カテゴリが含まれており、そのサブカテゴリの 1 つが上位の場合は、ユーザー エクスペリエンスに上位ミュージック ビデオ カテゴリを作成できます。 `thumbnailUrl`、`displayText`、`webSearchUrl` のフィールドを使用して、各カテゴリ (たとえば、上位ミュージック ビデオ) の下にクリック可能なタイルを作成できます。 ユーザーがタイルをクリックすると、Bing のビデオ ブラウザーに移動し、動画が再生されます。

応答には、最も人気の高い急上昇中の動画であるバナー ビデオも含まれます。 バナー ビデオは、1 つまたは複数のカテゴリから取得される可能性があります。  
  
