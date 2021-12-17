---
title: Bing Entity Search API とは
titleSuffix: Azure Cognitive Services
description: Bing Entity Search API の詳細と、検索クエリからエンティティと場所を抽出して検索する方法について説明します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.openlocfilehash: 7cf29bd4c20d6acd2a5a69cf9f8bbfb705413a0a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631155"
---
# <a name="what-is-bing-entity-search-api"></a>Bing Entity Search API とは

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Entity Search API は、Bing に検索クエリを送信して、エンティティと場所を含む検索結果を取得します。 場所の結果には、レストラン、ホテルやその他の地元企業が含まれます。 クエリで地元企業の名前を指定、またはビジネスの種類 (近くのレストランなど) を要求すると、Bing は場所を返します。 クエリで有名な人や場所 (観光名所、州、国/地域など)、またはものを指定すると、Bing はエンティティを返します。

|特徴量  |説明  |
|---------|---------|
|[リアルタイムの検索候補](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | ユーザーが入力するときにドロップダウン リストとして表示される検索候補を提供します。       | 
| [エンティティのあいまいさ排除](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | 可能性のある複数の意味を使用し、クエリに対して複数のエンティティを取得します。 |
| [場所を探す](concepts/search-for-entities.md#find-places) | ローカルな企業とエンティティに関する情報を検索して返します  |

## <a name="workflow"></a>ワークフロー

Bing Entity Search API は RESTful Web サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 REST API または SDK のいずれかを使用してサービスを使用できます。

1. Bing Search API が利用できる [Cognitive Services API アカウント](../cognitive-services-apis-create-account.md)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)できます。
2. 有効な検索クエリを使用して API に要求を送信します。
3. 返された JSON メッセージを解析して API 応答を処理します。

## <a name="next-steps"></a>次のステップ

* Bing Entity Search API の[対話型デモ](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/)を試してください。 
* 実際に要求を送信してみるには、[クイック スタート](quickstarts/csharp.md)を試してください。
* [Bing Entity Search API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) リファレンス セクション。
* [Bing の利用と表示の要件](../bing-web-search/use-display-requirements.md)に関するトピックでは、Bing Search API を使用して得られるコンテンツと情報の許可される使用方法が指定されています。
* その他の提供されている API については、[Bing Search API ハブ ページ](../bing-web-search/overview.md)をご覧ください。