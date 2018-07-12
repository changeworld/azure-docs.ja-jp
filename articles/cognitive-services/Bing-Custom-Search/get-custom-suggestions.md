---
title: 'Bing Custom Search: Custom Autosuggest 検索候補の取得 | Microsoft Docs'
description: カスタムの Autosuggest 検索候補を取得する方法について説明します
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374709"
---
# <a name="get-custom-suggestions"></a>カスタム検索候補の取得
Bing Custom Search にクエリを送信する前に、Custom Autosuggest API を呼び出して検索用語候補を提案し、検索エクスペリエンスを向上させます。 Custom Autosuggest API は、ユーザーが指定した部分的なクエリ文字列に基づいて、推奨されるクエリの一覧を返します。 指定した関連カスタム検索語は、Autosuggest が生成する検索候補の前に表示されます。 詳細については、「[Define custom search suggestions](define-custom-suggestions.md)」 (カスタム検索候補を定義する) を参照してください。

## <a name="endpoint"></a>エンドポイント
Bing Custom Search API を使用して検索候補を取得するには、次のエンドポイントに `GET` 要求を送信します。

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>応答 JSON
応答には、候補となる検索語を含んだ SearchAction オブジェクトのリストが格納されます。

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

それぞれの候補には、`displayText` フィールドと `query` フィールドが含まれています。 `displayText` フィールドには、検索ボックスのドロップダウン リストの設定に使用する検索候補が含まれています。

ユーザーがドロップダウン リストから検索候補を選択する場合は、[Bing Custom Search API](overview.md) を呼び出す際に `query` フィールドの検索語を使用します。

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

- [カスタム検索の呼び出し](./search-your-custom-view.md)
- [ホストされている UI エクスペリエンスの構成](./hosted-ui.md)