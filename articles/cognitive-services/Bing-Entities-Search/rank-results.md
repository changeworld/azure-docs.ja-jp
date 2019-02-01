---
title: ランキングを使用した回答の表示 - Bing Entity Search
titlesuffix: Azure Cognitive Services
description: Bing Entity Search API が返す回答を、ランキングを使用して表示する方法を説明します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: aahi
ms.openlocfilehash: f0cae32acf2db62a5d3c060ea944b1131252beda
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195921"
---
# <a name="using-ranking-to-display-results"></a>ランキングを使用して結果を表示する  

エンティティ検索の各応答には、[RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) 回答が含まれます。これは、Bing Web Search の応答と同様のもので、検索結果の表示方法が指定されています。 ランキング応答により、結果がポール コンテンツ、メインライン コンテンツ、およびサイドバー コンテンツにグループ化されます。 ポールの結果は、最も重要または主要な結果であるため、最初に表示する必要があります。 残りの結果を従来のメインラインおよびサイドバー形式で表示しない場合は、メインラインのコンテンツをサイドバーのコンテンツより高い優先度で表示する必要があります。 
  
各グループ内で、[Items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) 配列は、コンテンツの表示順序を指定します。 各項目では、回答内の結果を 2 つの方法で識別できます。  
  
-   `answerType` および `resultIndex` — `answerType` フィールドは、回答 (Entity または Place) を識別し、`resultIndex` は、回答内の結果を識別します (エンティティなど)。 インデックスは、0 から始まります。  
  
-   `value` — `value` フィールドには、回答または回答内の結果の ID に一致する ID が含まれます。 回答または結果のいずれかに ID が含まれています。両方には含まれていません。  
  
ID を使用するには、回答の ID またはその結果の ID とランキング ID とを一致させる必要があります。 回答オブジェクトに `id` フィールドが含まれている場合は、すべての回答の結果を一緒に表示します。 例えば、`Entities` オブジェクトに `id` フィールドが含まれている場合は、すべてのエンティティのアーティクルを一緒に表示します。 `Entities` オブジェクトに `id` フィールドが含まれていない場合は、各エンティティに `id` フィールドが含まれており、ランキング応答によって、エンティティと Places の結果がミックスされます。  
  
`answerType` と `resultIndex` の使用は、2 段階のプロセスです。 最初に、`answerType` を使用して、表示する結果が含まれる回答を識別します。 次に、`resultIndex` を使用して、その回答の結果にインデックスを付け、表示する結果を取得します。 (`answerType` 値は、[SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) オブジェクト内のフィールドの名前です。)すべての回答の結果を一緒に表示する場合、ランキング応答の項目には、`resultIndex` フィールドは含まれません。

## <a name="ranking-response-example"></a>ランキング応答の例

次に示すのは、[RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) の例です。
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

このランキング応答に基づいて、Jimi Hendrix に関連する 2 つのエンティティの結果がサイドバーに表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Entity Search のチュートリアル](tutorial-bing-entities-search-single-page-app.md)
