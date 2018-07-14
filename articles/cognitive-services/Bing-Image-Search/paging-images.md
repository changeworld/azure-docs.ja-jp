---
title: 利用可能な画像をページングする方法 | Microsoft Docs
description: Bing が返すことのできるすべての画像をページングする方法を示します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a74ee817e84be5bb563c5fdaf25afc1dc14732e5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372744"
---
# <a name="paging-results"></a>ページングの結果

Image Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリとの関連性が高い結果の総数のサブセットです。 利用可能な結果の推定総数を取得するには、回答オブジェクトの [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) フィールドにアクセスします。  
  
次の例は、画像の回答に含まれる `totalEstimatedMatches` フィールドを示しています。  
  
```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  
  
利用可能な画像をページングするには、[count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) および [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) クエリ パラメーターを使用します。  
  
`count` パラメーターでは、応答で返される結果の数を指定します。 応答に要求できる結果の最大数は 150 です。 既定値は 35 です。 配信される実際の数は、要求した数よりも少ない可能性があります。

`offset` パラメーターでは、スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。  
  
1 ページあたり 20 個の画像を表示する場合は、`count` を 20 に、`offset` を 0 に設定して、結果の最初のページを取得します。 オフセット 40 から始まる 20 個の画像を要求する例を次に示します。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

既定の `count` 値が実装で動作する場合、指定する必要があるのは `offset` クエリ パラメーターだけです。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
一度に 35 個の画像をページングする場合は、最初の要求で `offset` クエリ パラメーターを 0 に設定し、後続の要求ごとに `offset` を 35 ずつ増やすと予想できます。 しかし、後続の応答の結果の一部が前の応答と重複することがあります。 たとえば、応答内の最初の 2 つの画像が、前の応答の最後の 2 つの画像と同じである場合があります。

重複する結果を排除するには、`Images` オブジェクトの [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) フィールドを使用します。 `nextOffset` フィールドは、次の要求に使用する `offset` を示します。 たとえば、一度に 30 個の画像をページングする場合は、最初の要求で `count` を 30 に、`offset` を 0 に設定します。 次の要求では、`count` を 30 に、`offset` を前の応答の `nextOffset` の値に設定します。 後方にページングするには、前のオフセットのスタックを維持し、最新のものをポップすることお勧めします。

> [!NOTE]
> ページングは、画像検索 (/images/search) にのみ適用され、画像の分析情報や注目の画像 (/images/trending) には適用されません。