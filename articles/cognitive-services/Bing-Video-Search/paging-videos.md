---
title: 利用可能な動画をページングする方法 | Microsoft Docs
description: Bing が返すことのできるすべての動画をページングする方法を示します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 910A485F-BCF3-42B9-958D-DD48BDEDA965
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 00476825eb3fc1008c3f2172b591d8b7a2f35884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372805"
---
# <a name="paging-videos"></a>動画のページング

Video Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリとの関連性が高い結果の総数のサブセットです。 利用可能な結果の推定総数を取得するには、回答オブジェクトの [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) フィールドにアクセスします。  
  
次の例は、動画回答に含まれる `totalEstimatedMatches` フィールドを示しています。  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
利用可能な動画をページングするには、[count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) および [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) クエリ パラメーターを使用します。  
  
`count` パラメーターでは、応答で返される結果の数を指定します。 応答に要求できる結果の最大数は 105 です。 既定値は 35 です。 配信される実際の数は、要求した数よりも少ない可能性があります。

`offset` パラメーターでは、スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。  
  
1 ページあたり 20 個の動画を表示する場合は、`count` を 20 に、`offset` を 0 に設定して、結果の最初のページを取得します。 後続のページごとに、`offset` を 20 ずつ増やします (たとえば、20、40)。  

オフセット 40 から始まる 20 個の動画を要求する例を次に示します。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

既定の `count` 値が実装で動作する場合、指定する必要があるのは `offset` クエリ パラメーターだけです。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

通常、一度に 35 個の動画をページングする場合は、最初の要求で `offset` クエリ パラメーターを 0 に設定し、後続の要求ごとに `offset` を 35 ずつ増やします。 しかし、後続の応答の結果の一部が前の応答と重複することがあります。 たとえば、応答内の最初の 2 つの動画が、前の応答の最後の 2 つの動画と同じである場合があります。

重複する結果を排除するには、`Videos` オブジェクトの [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) フィールドを使用します。

たとえば、一度に 30 個の動画をページングする場合は、最初の要求で `count` を 30 に、`offset` を 0 に設定します。 次の要求で、`offset` クエリ パラメーターを `nextOffset` 値に設定します。


> [!NOTE]
> ページングは、動画検索 (/videos/search) にのみ適用され、動画の分析情報 (/videos/details) や急上昇中の動画 (/videos/trending) には適用されません。