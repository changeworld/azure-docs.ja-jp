---
title: Autosuggest エンドポイント | Microsoft Docs
description: Autosuggest API エンドポイントの概要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 5aaddd09006cb6f1812bb6ae213a2f5e6720fb1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376885"
---
# <a name="autosuggest-endpoint"></a>Autosuggest エンドポイント

**Autosuggest API** には、検索用語の一部から取得されたクエリ候補の一覧を返す 1 つのエンドポイントが含まれています。

## <a name="endpoint"></a>エンドポイント

Bing API を使用してクエリ候補を取得するには、次のエンドポイントに `GET` 要求を送信します。 ヘッダーと URL パラメーターを使用して、さらに詳細を定義します。

**エンドポイント:** 検索候補を、`?q=""` によって定義されたユーザーの入力に関連する JSON 結果として返します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) のリファレンスをご覧ください。

## <a name="response-json"></a>応答 JSON

自動提案要求に対する応答には、結果が JSON オブジェクトとして含まれます。 結果の解析例については、[チュートリアル](tutorials/autosuggest.md)と[ソース コード](tutorials/autosuggest-source.md)をご覧ください。

## <a name="next-steps"></a>次の手順

**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。 すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。
すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Autosuggest API を使用した基本的な要求の例については、[Autosuggest のクイック スタート](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)をご覧ください。