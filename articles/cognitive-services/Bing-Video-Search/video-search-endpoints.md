---
title: Video Search エンドポイント | Microsoft Docs
description: Video Search API エンドポイントの概要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-gedod
ms.openlocfilehash: 9836d9928362ab37b0a81ff5043d99f9bf353f22
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372893"
---
# <a name="video-search-endpoints"></a>Video Search エンドポイント
**Video Search API** には 3 つのエンドポイントが含まれています。  エンドポイント 1 は、クエリに基づいて Web からの動画を返します。 エンドポイント 2 は、`modules` URL パラメーターに基づいて動画に関する分析情報を返します。  エンドポイント 3 は注目の画像を返します。

## <a name="endpoints"></a>エンドポイント
Bing API を使用して動画の結果を取得するには、次のエンドポイントのいずれかに `GET` 要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義するために使用します。

**エンドポイント 1:** `?q=""` によって定義されたユーザーの検索クエリに関連する動画を返します。
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**エンドポイント 2:** 関連動画など、動画に関する分析情報を返します。 要求に対する分析情報のコンマ区切りリストである `modules` [クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters)を含みます。
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**エンドポイント 3:** 他のユーザーが行った検索要求に基づく注目の動画を返します。 動画は、たとえば注目の人物やイベントに基づいて異なるカテゴリに分離されます。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) のリファレンスをご覧ください。
## <a name="response-json"></a>応答 JSON
動画検索要求に対する応答には、結果が JSON オブジェクトとして含まれます。 結果の解析例については、[チュートリアル](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app)と[ソース コード](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source)をご覧ください。

## <a name="next-steps"></a>次の手順
**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。 すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。  すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Video Search API を使用した基本的な要求の例については、[Video Search クイック スタート](https://docs.microsoft.com/azure/cognitive-services/bing-video-search)のページをご覧ください。