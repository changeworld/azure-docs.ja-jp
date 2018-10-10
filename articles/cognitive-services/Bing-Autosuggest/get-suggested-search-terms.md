---
title: Bing Autosuggest とは | Microsoft Docs
description: Bing Autosuggest API の使用方法を説明します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 6F4AFEDA-71A7-48C1-B3E2-D0D430428CDC
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: 76e509289f495e09c367af926fd26e0581b6e7c6
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091108"
---
# <a name="what-is-bing-autosuggest"></a>Bing Autosuggest とは

Bing Search API のいずれかにクエリを送信する場合、検索ボックスのエクスペリエンスを向上させるために Bing Autosuggest API を使用することができます。 Bing Autosuggest API は、ユーザーが検索ボックスに入力した部分的なクエリ文字列に基づいて、候補となるクエリの一覧を返します。 検索ボックスのドロップダウン リストに検索候補を表示します。 候補となる語句は、他のユーザーが検索したクエリ候補やユーザーの意図に基づいています。

通常は、ユーザーが検索ボックスに新しい文字を入力するたびにこの API を呼び出します。 クエリ文字列の完全性は、API から返されるクエリ語句の候補の関連性に影響します。 クエリ文字列がより完全になるほど、より高い関連性を持つクエリ語句の候補が返されます。 たとえば、API から *s* に対して返される可能性のある候補は、*sailing dinghies* に対して返されるクエリよりもおそらく関連性が低くなります。

## <a name="getting-suggested-search-terms"></a>検索語句候補の取得

次の例に、*sail* に対するクエリ文字列の候補を返す要求を示します。 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query) クエリ パラメーターを設定する際には、忘れずにユーザーの部分的なクエリ用語を URL エンコードします。 たとえば「*sailing les*」と入力された場合、`q` を `sailing+les` または `sailing%20les` に設定します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

次の応答には、候補となる検索語を含んだ [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) オブジェクトのリストが格納されます。

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

それぞれの候補には、`displayText`、`query`、および `url` フィールドが含まれています。 `displayText` フィールドには、検索ボックスのドロップダウン リストの設定に使用する検索候補が含まれています。 応答に含まれるすべての候補を、指定された順序で表示する必要があります。

次に、提案されたクエリ用語をドロップダウンに表示する検索ボックスの例を示します。

![ドロップダウン検索ボックスによる自動提案リスト](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

ユーザーがドロップダウン リストからクエリ候補を選択した場合は、`query` フィールドのクエリ用語を使用して [Bing Web Search API](../bing-web-search/search-the-web.md) を呼び出し、独自にその結果を表示することができます。 または、`url` フィールドの URL を使用して、ユーザーを Bing 検索結果ページに誘導することもできます。

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

実際に要求を送信してみるには、[最初のクエリを実行する](quickstarts/csharp.md)に関するページを参照してください。

[Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) リファレンスを活用してください。 このリファレンスには、クエリ用語候補を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧と、応答オブジェクトの定義が記載されています。

[Bing Web Search API](../bing-web-search/search-the-web.md) を使用して Web を検索する方法について説明します。

検索結果の使用に関するルールを逸脱しないよう、[Bing の使用上および表示上の要件](./useanddisplayrequirements.md)に関するページを必ず読んでください。