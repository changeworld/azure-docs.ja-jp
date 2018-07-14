---
title: Bing News search エンドポイント | Microsoft Docs
description: News Search API エンドポイントの概要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: b8ae99698926a818bf22b0b4027af8610413aaa6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372909"
---
# <a name="news-search-endpoints"></a>News Search エンドポイント
**News Search API** は、ニュース記事、Web ページ、画像、動画、[エンティティ](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)を返します。 エンティティには、人物、場所、またはトピックに関する概要情報が含まれます。 
## <a name="endpoints"></a>エンドポイント
Bing API を使用してニュース検索の結果を取得するには、次のエンドポイントのいずれかに `GET` 要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義します。

**エンドポイント 1:** ユーザーの検索クエリに基づいてニュース項目を返します。 検索クエリが空の場合、呼び出しではトップ ニュース記事が返されます。 クエリ `?q=""` オプションは、`/news` URL でも使用できます。 可用性については、[サポートされている国と市場](supported-countries-markets.md#supported-markets-for-news-search-endpoint)に関する記事をご覧ください。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search 
``` 

**エンドポイント 2:** カテゴリ別のトップ ニュース項目を返します。 `category=business`、`category=sports`、または `category=entertainment` を使用してビジネス、スポーツ、またはエンターテイメントのトップ記事を具体的に要求できます。  `category` パラメーターは、`/news` URL でのみ使用できます。 カテゴリを指定するための正式な要件がいくつかあります。[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters)に関するドキュメントの `category` に関するページをご覧ください。 可用性については、[サポートされている国と市場](supported-countries-markets.md#supported-markets-for-news-endpoint)に関する記事をご覧ください。 
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**エンドポイント 3:** ソーシャル ネットワークで現在注目されているニュース トピックを返します。 `/trendingtopics` オプションが含まれている場合、Bing 検索は `freshness` や `?q=""` などの他のいくつかのパラメーターを無視します。 可用性については、[サポートされている国と市場](supported-countries-markets.md#supported-markets-for-news-trending-endpoint)に関する記事をご覧ください。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics 
``` 

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) のリファレンスをご覧ください。
## <a name="response-json"></a>応答 JSON
ニュース検索要求に対する応答には、結果が JSON オブジェクトとして含まれます。 結果を解析するには、各種類の要素を処理するプロシージャが必要です。 例については、[チュートリアル](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app)と[ソース コード](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source)をご覧ください。

## <a name="next-steps"></a>次の手順
**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。 すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。  すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
News Search API を使用した基本的な要求の例については、[Bing News Search クイック スタート](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)のページをご覧ください。