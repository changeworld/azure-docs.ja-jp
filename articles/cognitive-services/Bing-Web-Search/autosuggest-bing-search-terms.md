---
title: Bing 検索語句の自動提案
titleSuffix: Microsoft Cognitive Services
description: Bing Web Search API を Bing Autosuggest API と組み合わせて、強化された検索エクスペリエンスをユーザーに提供します。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: df8a57b3136abfacce971f4d01ccb2296dfa784c
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889349"
---
# <a name="autosuggest-bing-search-terms"></a>Bing 検索語句の自動提案

ユーザーが検索語句を入力するための検索ボックスを用意する場合は、[Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) を使用することでエクスペリエンスが向上します。 この API は、検索語句をユーザーが入力している最中に、その一部分に基づいてクエリ文字列の候補を返します。

ユーザーによって入力された検索語句は、[q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) クエリ パラメーターが設定される前に URL エンコードされる必要があります。 たとえば「*sailing dinghies*」と入力された場合、`q` を `sailing+dinghies` または `sailing%20dinghies` に設定します。

検索語にスペル ミスが含まれていると、検索応答に [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) オブジェクトが含まれます。 このオブジェクトは元のスペルと Bing が検索に使用した修正済みのスペルを示します。

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

この情報を使用して、検索結果を表示するときにクエリ文字列を変更したことをユーザーに知らせることができます。

![クエリ コンテキスト UX の例](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>次の手順  

* サンプルの [Bing Web Search API 応答](search-responses.md)を確認します。  

## <a name="see-also"></a>関連項目  

* [Bing Web Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
