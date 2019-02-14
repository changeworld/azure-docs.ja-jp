---
title: Bing Entity Search API エンドポイント
titlesuffix: Azure Cognitive Services
description: Bing Entity Search API エンドポイントの概要を学習して、エンドポイントに要求を送信します。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: cae65c8fb7deb9f68a297de2058a86249b60136b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753982"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API エンドポイント


Bing Entity Search API には、クエリに基づいて Web からのエンティティを返す 1 つのエンドポイントが含まれています。 これらの検索結果は JSON 形式で返されます。

## <a name="get-entity-results-from-the-endpoint"></a>エンドポイントからエンティティの結果を取得する

**Bing API** を使用してエンティティの結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 検索要求をカスタマイズするには、[ヘッダー](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers)と[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters)を使用します。 検索要求は、`?q=` パラメーターを使用して送信できます。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Entity Search API とは](overview.md)

## <a name="see-also"></a>関連項目 

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどの詳細については、「[Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)」の参照記事をご覧ください。
