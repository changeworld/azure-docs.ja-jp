---
title: Bing News Search エンドポイント
titleSuffix: Azure Cognitive Services
description: この記事では、ニュース、トップ ニュース、トレンド ニュースなどの News Search API エンドポイントの概要について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 8c724925a12535c561b035296e374691f3fb2689
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098350"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API エンドポイント

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](https://aka.ms/cogsvcs/bingmove)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](https://aka.ms/cogsvcs/bingmigration) に関する記事を参照してください。

**News Search API** は、ニュース記事、Web ページ、画像、動画、 [エンティティ](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)を返します。 エンティティには、人物、場所、またはトピックに関する概要情報が含まれます。

## <a name="endpoints"></a>エンドポイント

Bing News Search API を使用してニュース検索の結果を取得するには、次のエンドポイントのいずれかに `GET` 要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義します。

### <a name="news-items-by-search-query"></a>検索クエリによるニュース項目

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

検索クエリに基づいてニュース項目を返します。 検索クエリが空の場合、API はさまざまなカテゴリのトップ ニュース記事を返します。 検索語を URL エンコードし、それを `q=""` パラメーターに追加することで、クエリを送信します。 可用性については、[サポートされている国/地域と市場](language-support.md#supported-markets-for-news-search-endpoint)に関する記事を参照してください。

### <a name="top-news-items-by-category"></a>カテゴリ別のトップ ニュース項目

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

カテゴリ別にトップ ニュース項目を返します。 `category=business`、`category=sports`、または `category=entertainment` を使用してビジネス、スポーツ、またはエンターテイメントのトップ記事を具体的に要求できます。  `category` パラメーターは、`/news` URL でのみ使用できます。 カテゴリを指定するための正式な要件がいくつかあります。[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters)に関するドキュメントの `category` に関するページをご覧ください。 検索語を URL エンコードし、それを `q=""` パラメーターに追加することで、クエリを送信します。 可用性については、[サポートされている国/地域と市場](language-support.md#supported-markets-for-news-endpoint)に関する記事を参照してください。

### <a name="trending-news-topics"></a>ニュース トピックのトレンド 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

ソーシャル ネットワークで現在注目されているニュース トピックを返します。 `/trendingtopics` オプションが含まれている場合、Bing 検索は `freshness` や `?q=""` などの他のいくつかのパラメーターを無視します。 可用性については、[サポートされている国/地域と市場](language-support.md#supported-markets-for-news-trending-endpoint)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) のリファレンスをご覧ください。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
News Search API を使用した基本的な要求の例については、[Bing News Search クイック スタート](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)のページをご覧ください。
