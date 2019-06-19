---
title: Bing Entity Search API エンドポイント
titlesuffix: Azure Cognitive Services
description: Bing Entity Search API エンドポイントの概要を学習して、エンドポイントに要求を送信します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 43bca65810d09c87f7f473b3bbac71ca6a7f9bc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389005"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API エンドポイント


Bing Entity Search API には、クエリに基づいて Web からのエンティティを返す 1 つのエンドポイントが含まれています。 これらの検索結果は JSON 形式で返されます。

## <a name="get-entity-results-from-the-endpoint"></a>エンドポイントからエンティティの結果を取得する

**Bing API** を使用してエンティティの結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 検索要求をカスタマイズするには、[ヘッダー](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers)と[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)を使用します。 検索要求は、`?q=` パラメーターを使用して送信できます。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Entity Search API とは](overview.md)

## <a name="see-also"></a>関連項目 

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどの詳細については、「[Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)」の参照記事をご覧ください。
