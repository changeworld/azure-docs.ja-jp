---
title: Bing Image Search API に対するクエリの送信 | Microsoft Docs
description: Bing Image Search API に対する検索クエリの送信とカスタマイズについて説明します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082588"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>Bing Image Search API に対するクエリの送信

Bing Image Search API は、Bing.com/Images と同様、ユーザーの検索クエリを Bing に送信して関連性の高い一連の画像を取得する機能を備えています。

## <a name="using-and-suggesting-search-terms"></a>検索語句の使用と提案

検索語句が入力された後、その語句を URL エンコードしたうえで、[**q**](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) クエリ パラメーターを設定します。 たとえば「*sailing dinghies*」と入力された場合、`q` を `sailing+dinghies` または `sailing%20dinghies` に設定します。

検索語句を入力するための検索ボックスがアプリに備わっている場合は、[Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) を使い、おすすめ検索語句をリアルタイムに表示することで利便性を高めることができます。 この API は、検索語句の一部分や Azure Cognitive Services に基づいてクエリ文字列の候補を返します。

## <a name="pivoting-the-query"></a>クエリのピボット

Bing が元の検索クエリを分割できる場合、返される [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) オブジェクトには、オプションの検索語句としてユーザーに表示できる `pivotSuggestions` が格納されます。 たとえば、元のクエリが「*Microsoft Surface*」である場合、そのクエリが Bing によって *Microsoft* と *Surface* に分割され、それぞれについてピボット候補を得ることができます。 それらをオプションの検索語としてユーザーに表示することができます。

次の例は、*Microsoft Surface* に関するピボット候補を示しています。  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

`pivotSuggestions` フィールドには、元のクエリを分割することによって得られたセグメント (ピボット) のリストが含まれています。 応答には、ピボットごとにおすすめクエリを含んだ [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) オブジェクトのリストが格納されます。 `text` フィールドには、おすすめクエリが格納され、`displayText` フィールには、元のクエリ内のピボットを置き換えた語句が格納されます  (例: "Release Date of Surface")。

ユーザーが本当に探しているのは、ピボット クエリ文字列であることも考えられます。そのような場合に備えて、`text` フィールドと `thumbnail` フィールドを使用して、ピボット クエリ文字列をユーザーに表示することができます。 サムネイルやテキストは、`webSearchUrl` URL または `searchLink` URL を使用して、クリック可能な状態にしましょう。 ユーザーを Bing の検索結果に誘導する場合は `webSearchUrl` を使用し、独自の結果ページを用意する場合は `searchLink` を使用します。

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>クエリの展開

Bing がクエリを展開して元の検索を絞り込むことができる場合、[Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) オブジェクトには `queryExpansions` フィールドが含まれます。 たとえば「*Microsoft Surface*」というクエリは、Microsoft Surface **Pro 3**、Microsoft Surface **RT**、Microsoft Surface **Phone**、Microsoft Surface **Hub** に展開される可能性があります。

次の例は、*Microsoft Surface* の展開されたクエリを示しています。

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

`queryExpansions` フィールドには、[Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) オブジェクトのリストが含まれています。 `text` フィールドには、展開されたクエリが格納され、`displayText` フィールドには、展開語句が格納されます。 ユーザーが本当に探しているのは、展開されたクエリ文字列であることも考えられます。そのような場合に備えて、`text` フィールドと `thumbnail` フィールドを使用して、展開されたクエリ文字列をユーザーに表示することができます。 サムネイルやテキストは、`webSearchUrl` URL または `searchLink` URL を使用して、クリック可能な状態にしましょう。 ユーザーを Bing の検索結果に誘導する場合は `webSearchUrl` を使用し、独自の結果ページを用意する場合は `searchLink` を使用します。

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

まだ Bing Image Search API を試していない方は、[クイック スタート](../quickstarts/csharp.md)をご覧ください。 さらに高度な内容をお求めの方は、[単一ページの Web アプリ](../tutorial-bing-image-search-single-page-app.md)の作成に関するチュートリアルをご覧ください。
