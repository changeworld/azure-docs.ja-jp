---
title: 使用可能な web ページをページングする - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Bing Custom Search が返すことができるすべての Web ページをページングする方法を示します。
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: b4e022d6237712d8281c6f4f9332a68821dfd5b1
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470083"
---
# <a name="paging-webpages"></a>Web ページのページング 

Custom Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリに関連する可能性がある結果の総数のサブセットです。 利用可能な結果の推定総数を取得するには、回答オブジェクトの [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) フィールドにアクセスします。  
  
次の例は、Web 回答に含まれる `totalEstimatedMatches` フィールドを示しています。  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
利用可能な Web ページをページングするには、[count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) および [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset) クエリ パラメーターを使用します。  
  
`count` パラメーターでは、応答で返される結果の数を指定します。 応答に要求できる結果の最大数は 50 です。 既定値は 10 です。 配信される実際の数は、要求した数よりも少ない可能性があります。

`offset` パラメーターでは、スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。  
  
1 ページあたり 15 件の Web ページを表示する場合は、`count` を 15 に、`offset` を 0 に設定して、結果の最初のページを取得します。 後続のページごとに、`offset` を 15 ずつ増やします (たとえば、15、30)。  
  
オフセット 45 から始まる 15 件の Web ページを要求する例を次に示します。  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

既定の `count` 値が実装で動作する場合、指定する必要があるのは `offset` クエリ パラメーターだけです。  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

