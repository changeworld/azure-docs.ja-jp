---
title: Web Search エンドポイント
titleSuffix: Azure Cognitive Services
description: 検索の結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 25ecd1a753cb9a401408f7ed6605d53e5310df2b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075672"
---
# <a name="web-search-endpoint"></a>Web Search エンドポイント

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](https://aka.ms/cogsvcs/bingmove)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](https://aka.ms/cogsvcs/bingmigration) に関する記事を参照してください。

**Web Search API** は、Web ページ、ニュース、画像、動画、 [エンティティ](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)を返します。 エンティティには、人物、場所、またはトピックに関する概要情報があります。

## <a name="endpoint"></a>エンドポイント

Bing API を使用して Web 検索の結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義します。

**エンドポイント:**`?q=""` によって定義されたユーザーの検索クエリに関連する Web 検索結果を返します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

エンドポイント: ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing Web API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) のリファレンスを参照してください。

## <a name="response-json"></a>応答 JSON

Web 検索要求に対する応答には、すべての結果が JSON オブジェクトとして含まれます。 結果を解析するには、各種類の要素を処理するプロシージャが必要です。 例については、[チュートリアル](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app)と[ソース コード](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search)をご覧ください。

## <a name="next-steps"></a>次のステップ

**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。  すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。  すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Web Search API を使用した基本的な要求の例については、[Web の検索のクイック スタート](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web)のページをご覧ください。
