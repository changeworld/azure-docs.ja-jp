---
title: Image Search エンドポイント | Microsoft Docs
description: Image Search API エンドポイントの概要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: 0d5f28bfdb45b27b04df068f75e8a20d0235d12b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372760"
---
# <a name="image-search-endpoints"></a>Image Search エンドポイント
**Image Search API** には 3 つのエンドポイントが含まれています。  エンドポイント 1 は、クエリに基づいて Web からの画像を返します。 エンドポイント 2 は [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) を返します。  エンドポイント 3 は注目の画像を返します。
## <a name="endpoints"></a>エンドポイント
Bing API を使用して画像の結果を取得するには、次のエンドポイントのいずれかに要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義するために使用します。

**エンドポイント 1:** `?q=""` によって定義されたユーザーの検索クエリに関連する画像を返します。
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**エンドポイント 2:** `GET` または `POST` を使用して、画像に関する分析情報を返します。
``` 
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET 要求では、画像を含む Web ページなど、画像に関する分析情報が返されます。 [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) パラメーターを `GET` 要求に含めます。

または、`POST` 要求の本文にバイナリ イメージを含めて、[modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) パラメーターを `RecognizedEntities` に設定することもできます。 これは、後続の `GET` 要求でパラメーターとして使用する [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) を返します。これは、画像内の人物に関する情報を返します。  `modules` を `All` に設定して、`insightsToken` を使用して別の呼び出しを行わずに、`POST` の結果内の `RecognizedEntities` を除くすべての分析情報を取得します。 


**エンドポイント 3:** 他のユーザーが行った検索要求に基づく注目の画像を返します。 画像は、たとえば注目の人物やイベントに基づいて異なるカテゴリに分離されます。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

注目の画像をサポートする市場の一覧については、[注目の画像](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)に関する記事をご覧ください。

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) のリファレンスをご覧ください。
## <a name="response-json"></a>応答 JSON
画像検索要求に対する応答には、結果が JSON オブジェクトとして含まれます。 結果の解析例については、[チュートリアル](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)と[ソース コード](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source)をご覧ください。

## <a name="next-steps"></a>次の手順
**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。 すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。  すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Image Search API を使用した基本的な要求の例については、[Image Search クイック スタート](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web)のページをご覧ください。