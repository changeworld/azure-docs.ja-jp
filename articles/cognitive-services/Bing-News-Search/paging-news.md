---
title: 利用可能なニュース記事をページングする方法 | Microsoft Docs
description: Bing が返すことのできるすべてのニュース記事をページングする方法を示します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EA388F72-FA43-493B-967C-9560B3243C62
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2c90d468536f0864d7deac073667e29e9a54692f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372848"
---
# <a name="paging-news"></a>ニュースのページング

News Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリに関連する可能性がある結果の総数のサブセットです。 利用可能な結果の推定総数を取得するには、回答オブジェクトの [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) フィールドにアクセスします。  
  
次の例は、ニュース回答に含まれる `totalEstimatedMatches` フィールドを示しています。  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
利用可能な記事をページングするには、[count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) および [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) クエリ パラメーターを使用します。  
  
`count` パラメーターでは、応答で返される結果の数を指定します。 応答に要求できる結果の最大数は 100 です。 既定値は 10 です。 配信される実際の数は、要求した数よりも少ない可能性があります。

`offset` パラメーターでは、スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。  

1 ページあたり 20 件の記事を表示する場合は、`count` を 20 に、`offset` を 0 に設定して、結果の最初のページを取得します。 後続のページごとに、`offset` を 20 ずつ増やします (たとえば、20、40)。  
  
オフセット 40 から始まる 20 件のニュース記事を要求する例を次に示します。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
既定値 `count` が実装に対して動作する場合は、次の例で示すように、`offset` クエリ パラメーターのみを指定します。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> ページングはニュース検索 (/news/search) のみに適用され、トレンドのトピック (/news/trendingtopics) またはニュース (/news) のカテゴリには適用されません。