---
title: Bing Entity Search API エンドポイント
titleSuffix: Azure Cognitive Services
description: Bing Entity Search API には、クエリに基づいて Web からのエンティティを返す 1 つのエンドポイントが含まれています。 これらの検索結果は JSON 形式で返されます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 5d6b0a6be0d99815c4d14bb04e56241d011812b5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084954"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API エンドポイント

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](https://aka.ms/cogsvcs/bingmove)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](https://aka.ms/cogsvcs/bingmigration) に関する記事を参照してください。


Bing Entity Search API には、クエリに基づいて Web からのエンティティを返す 1 つのエンドポイントが含まれています。 これらの検索結果は JSON 形式で返されます。

## <a name="get-entity-results-from-the-endpoint"></a>エンドポイントからエンティティの結果を取得する

**Bing API** を使用してエンティティの結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 検索要求をカスタマイズするには、[ヘッダー](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers)と[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)を使用します。 検索要求は、`?q=` パラメーターを使用して送信できます。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bing Entity Search API とは](overview.md)

## <a name="see-also"></a>関連項目 

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどの詳細については、「[Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)」の参照記事をご覧ください。
