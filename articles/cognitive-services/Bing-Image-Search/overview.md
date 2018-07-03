---
title: Bing Image Search とは | Microsoft Docs
description: Bing Images Search API を使用して Web から画像を検索する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336521"
---
# <a name="what-is-bing-image-search"></a>Bing Image Search とは

Bing Image Search API は、[Bing 画像検索](https://www.bing.com/images)と同様、ユーザーの検索クエリを Bing に送信して関連性の高い一連の画像を取得する機能を備えています。

ユーザーの検索クエリと関連性の高い画像を探し出すことを目的として、画像のみの検索結果ページを作成する場合は、より汎用性の高い [Web Search API](../bing-web-search/search-the-web.md) ではなく、こちらの API を呼び出してください。 画像だけでなく、Web ページやニュース、動画など、他の種類のコンテンツも対象にする場合は、Web Search API を呼び出します。

## <a name="suggesting--using-search-terms"></a>検索語句の補完と使用

ユーザーが検索語句を入力するための検索ボックスを用意する場合は、[Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) を使用することでエクスペリエンスが向上します。 この API は、検索語句をユーザーが入力している最中に、その一部分に基づいてクエリ文字列の候補を返します。

ユーザーが検索語句を入力した後、その語句を URL エンコードしたうえで、[q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) クエリ パラメーターを設定します。 たとえば「*sailing dinghies*」と入力された場合、`q` を `sailing+dinghies` または `sailing%20dinghies` に設定します。

## <a name="getting-images"></a>画像の取得

ユーザーの検索語句に関連した画像を Web から取得するには、次の GET 要求を送信します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

すべての要求は、サーバーから実行する必要があります。 クライアントから呼び出すことはできません。

いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

特定のドメインから画像を取得するには、[site:](http://msdn.microsoft.com/library/ff795613.aspx) というクエリ演算子を使用します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

応答として返されるデータには、そのクエリとの関連性が高いと Bing によって判断された一連の画像を格納する [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 応答が含まれています。 このリスト内の各 [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) オブジェクトには、画像の URL、サイズ、大きさ、エンコード形式が含まれています。 image オブジェクトには、画像のサムネイルの URL やサムネイルの大きさも含まれています。

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

すべての画像サムネイルのコラージュを表示したり、サムネイルのサブセットを表示したりすることもできます。 サブセットを表示する場合、残りの画像を表示するためのオプションをユーザーに提供します。 それらの画像は、応答に含まれている順序で表示する必要があります。

ユーザーがサムネイルにカーソルを合わせたタイミングでそのサムネイルを展開することもできます。 画像を展開する場合は、必ずその帰属を示すようにしてください。 たとえば、[hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) からホストを展開して、それを画像の下に表示するようにします。 サムネイルのサイズ変更については、[サムネイルのサイズ変更とクロップ](./resize-and-crop-thumbnails.md)に関するページを参照してください。

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

[contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) を使用すると、サムネイルがクリックされた場合に、フルサイズの画像をユーザーに表示することができます。 必ずその帰属を示すようにしてください。

`shoppingSourcesCount` または `recipeSourcesCount` がゼロより大きい場合は、対応するサムネイルにバッジ (ショッピング カートなど) を追加し、画像内のアイテムについてのショッピングまたはレシピが存在することを示します。

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

画像に関する分析情報 (その画像がある Web ページ、画像内で認識された人物など) を取得するには、[imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) を使用します。 詳細については、[画像の分析情報](./image-insights.md)に関するページを参照してください。

## <a name="filtering-images"></a>画像のフィルタリング

 Images Search API の既定の動作では、クエリとの関連性が高い画像がすべて返されます。 一方、背景が透明な画像や特定のサイズの画像のみを検索対象とする場合は、次のクエリ パラメーターを使用して、Bing から返された画像をフィルタリングすることになるでしょう。  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) - 画像を縦横比でフィルタリング (例: 標準またはワイド スクリーン画像など)
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) - ドミナント カラーや白黒で画像をフィルタリング
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) - 新しさで画像をフィルタリング (例: この 1 週間に Bing によって検出された画像など)
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height)、[width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) - 幅と高さで画像をフィルタリング
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) - 画像をコンテンツでフィルタリング (例: 人の顔が映った画像など)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) - 画像を種類でフィルタリング (例: クリップ アート、アニメーション GIF、透明な背景)
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) - サイトに関連付けられているライセンスの種類で画像をフィルタリング
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) - 画像をサイズでフィルタリング (最大 200x200 ピクセルの小さな画像など)

特定のドメインから画像を取得するには、[site:](http://msdn.microsoft.com/library/ff795613.aspx) というクエリ演算子を使用します。

> [!NOTE]
> クエリによっては、`site:` 演算子を使用した場合、[safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) 設定にかかわらず、成人向けのコンテンツが応答に含まれることがあります。 `site:` の使用は、そのサイト上のコンテンツを承知していて、なおかつ成人向けのコンテンツが含まれていても問題がないシナリオに限定してください。

次の例は、Bing が過去 1 週間以内に検出した小さな画像を ContosoSailing.com から取得する方法を示しています。  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>クエリのピボット

元の検索クエリを Bing が分割できる場合、[Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) オブジェクトには `pivotSuggestions` フィールドが存在します。 たとえば、元のクエリが「*Microsoft Surface*」である場合、そのクエリが Bing によって *Microsoft* と *Surface* に分割される可能性があります。  

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

さらに、ユーザーが関心を持つであろうことを想定して、オプションの検索語を表示することもできます。

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

実際に要求を送信してみるには、[C# のクイック スタート](quickstarts/csharp.md)を参照してください。

[Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) リファレンスを活用してください。 リファレンスには、検索結果を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧が記載されています。 また、応答オブジェクトの定義も記載されています。

検索ボックスのユーザー エクスペリエンスを高めるには、[Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) に関するページを参照してください。 ユーザーによって検索語が入力されている最中に、この API を呼び出すことで、他のユーザーによって使用された関連性の高い検索語を取得できます。

検索結果の使用に関するルールを逸脱しないよう、[Bing の使用上および表示上の要件](./useanddisplayrequirements.md)に関するページを必ず読んでください。

Bing Image Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリとの関連性が高い結果の総数のサブセットです。 表示できる画像の数の見積もりは、応答の `totalEstimatedMatches` フィールドに格納されます。 ページをめくるように残りの画像を表示する方法について詳しくは、[画像のページ移動](./paging-images.md)に関するページを参照してください。