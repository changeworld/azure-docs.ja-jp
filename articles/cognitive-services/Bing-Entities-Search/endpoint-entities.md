---
title: Entity Search エンドポイント | Microsoft Docs
description: Entity Search API エンドポイントの概要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: a2557c6000445544b3b47a05d7d356ccaa9928b4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372765"
---
# <a name="entity-search-endpoint"></a>Entity Search エンドポイント
**Entity Search API** には、クエリに基づいて Web からのエンティティを返す 1 つのエンドポイントが含まれています。

## <a name="endpoint"></a>エンドポイント
**Bing API** を使用してエンティティの結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義するために使用します。

**エンドポイント:** `?q=""` によって定義されたユーザーの検索クエリに関連するエンティティを返します。
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、「[Bing Entity Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)」(Bing Entity Search API v7 リファレンス) をご覧ください。

## <a name="response-json"></a>応答 JSON
エンティティ検索要求に対する応答には、結果が JSON オブジェクトとして含まれます。 結果の例については、[概要](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順
**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。 すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。  すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Entity Search API の使用例については、[概要](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start)に関する記事と「[サムネイル画像のサイズ変更とトリミング](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails)」をご覧ください。