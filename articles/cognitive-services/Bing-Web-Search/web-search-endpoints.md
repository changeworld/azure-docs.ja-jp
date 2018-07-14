---
title: Web Search エンドポイント | Microsoft Docs
description: Web Search API エンドポイントの概要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 72fbe1a0eb4379ad032e649f7299e37a0214adfb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372944"
---
# <a name="web-search-endpoint"></a>Web Search エンドポイント
**Web Search API** は、Web ページ、ニュース、画像、動画、[エンティティ](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)を返します。 エンティティには、人物、場所、またはトピックに関する概要情報が含まれます。
## <a name="endpoint"></a>エンドポイント
Bing API を使用して Web 検索の結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義します。

**エンドポイント:** `?q=""` によって定義されたユーザーの検索クエリに関連する Web 検索結果を返します。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
エンドポイント: ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing Web API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) のリファレンスをご覧ください。

## <a name="response-json"></a>応答 JSON
Web 検索要求に対する応答には、すべての結果が JSON オブジェクトとして含まれます。 結果を解析するには、各種類の要素を処理するプロシージャが必要です。 例については、[チュートリアル](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app)と[ソース コード](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source)をご覧ください。

## <a name="next-steps"></a>次の手順
**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。 すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。  すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Web Search API を使用した基本的な要求の例については、[Web の検索のクイック スタート](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web)のページをご覧ください。