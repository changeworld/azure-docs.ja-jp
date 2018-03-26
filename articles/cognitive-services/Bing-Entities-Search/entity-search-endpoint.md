---
title: "エンティティの検索エンドポイント |Microsoft ドキュメント"
description: "エンティティの検索 API エンドポイントの概要です。"
services: cognitive-services
author: v-jaswel
manager: kaiq
ms.service: cognitive-services
ms.technology: bing-entity-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: 531d98b6e4ac7ad1c7942fc50866ea2c636f0a07
ms.sourcegitcommit: 2c2dfe04a0af718cf058445467fa407ead5f45a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="entity-search-endpoints"></a>エンティティの検索エンドポイント
**エンティティの検索 API** 1 つのエンドポイントが含まれています。

## <a name="endpoint"></a>エンドポイント
エンティティの検索結果を要求するには、次のエンドポイントに要求を送信します。 仕様の定義に、ヘッダーと URL パラメーターを使用します。

エンドポイント`GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

次の URL パラメーターが必要です。
- mkt. 結果がどこから得られるマーケットです。 
- q. エンティティの検索クエリ。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing エンティティの検索のクイック スタート](quickstarts/csharp.md)

## <a name="see-also"></a>関連項目 

[Bing エンティティの検索の概要](search-the-web.md )
[API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
