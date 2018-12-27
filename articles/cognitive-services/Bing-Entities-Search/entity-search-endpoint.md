---
title: Bing Entity Search エンドポイント
titlesuffix: Azure Cognitive Services
description: Entity Search API エンドポイントの概要。
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: b1b89cbacf43df544261847d4e3fae396a52f423
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816278"
---
# <a name="entity-search-endpoints"></a>Entity Search エンドポイント
**Entity Search API** には 1 つのエンドポイントが含まれています。

## <a name="endpoint"></a>エンドポイント
エンティティの検索結果を要求するには、次のエンドポイントに要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義するために使用します。

エンドポイント `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

次の URL パラメーターが必要です。
- mkt。 結果の取得元の市場です。 
- q。 エンティティ検索クエリです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Entity Search のクイック スタート](quickstarts/csharp.md)

## <a name="see-also"></a>関連項目 

[Bing Entity Search の概要](search-the-web.md )
[API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
