---
title: Bing Video Search とは
titlesuffix: Azure Cognitive Services
description: Bing Video Search API を使用して Web で動画を検索する方法を示します。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: overview
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: cf37db9bffa8b2a54a6327c29ec806e0eefc8c91
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225414"
---
# <a name="what-is-bing-video-search"></a>Bing Video Search とは

Bing Video Search API は、[Bing Video](https://www.bing.com/videos) に似た (ただし、厳密に同じではありません) エクスペリエンスを提供します。 Bing Video Search API を使うと、検索クエリを Bing に送信し、関連する動画の一覧を取得できます。

ユーザーの検索クエリと関連性の高い動画を探し出すことを目的として、動画のみの検索結果ページを作成する場合は、より汎用性の高い [Bing Web Search API](../bing-web-search/search-the-web.md) ではなく、こちらの API を呼び出してください。 動画だけでなく、Web ページやニュース、画像など、他の種類のコンテンツも対象にする場合は、Bing Web Search API を呼び出します。

## <a name="suggesting--using-search-terms"></a>検索語句の補完と使用

ユーザーが検索語句を入力するための検索ボックスを用意する場合は、[Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) を使用することでエクスペリエンスが向上します。 この API は、検索語句をユーザーが入力している最中に、その一部分に基づいてクエリ文字列の候補を返します。

ユーザーが検索語句を入力した後、それを URL エンコードしたうえで、[q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query) クエリ パラメーターを設定します。 たとえば「*sailing dinghies*」と入力された場合、`q` を `sailing+dinghies` または `sailing%20dinghies` に設定します。

## <a name="getting-videos"></a>動画の取得

ユーザーの検索語句に関連した動画を Web から取得するには、次の GET 要求を送信します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

すべての要求は、サーバーから実行する必要があります。

いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

特定のドメインから動画を取得するには、[site:](http://msdn.microsoft.com/library/ff795613.aspx) というクエリ演算子を使用します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

応答として返されるデータには、そのクエリとの関連性が高いと Bing によって判断された一連の動画を格納する [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 応答が含まれています。 このリスト内の各 [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) オブジェクトには、動画の URL、再生時間、大きさ、エンコード形式などの属性が含まれています。 video オブジェクトには、動画のサムネイルの URL やサムネイルの大きさも含まれています。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

すべての動画サムネイルのコラージュを表示したり、サムネイルのサブセットを表示したりすることもできます。 サブセットを表示する場合、残りの動画を表示するためのオプションをユーザーに提供します。 それらの動画は、応答に含まれている順序で表示する必要があります。 サムネイルのサイズ変更については、[サムネイルのサイズ変更とクロップ](./resize-and-crop-thumbnails.md)に関するページを参照してください。 

ユーザーがサムネイルにマウス ポインターを重ねたときに、[motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) を使用して、サムネイル バージョンの動画を再生することができます。 動画のサムネイルを表示するときは、必ずその帰属を示すようにしてください。

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

サムネイルがクリックされた場合の動画の再生方法としては、次の選択性があります。

- ホスト Web サイト (YouTube など) で動画を再生する場合は、[hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) を使用します。
- Bing の動画ブラウザーで動画を再生する場合は、[webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) を使用します。
- 独自に用意したエクスペリエンスに動画を埋め込む場合は、[embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) を使用します。 

動画を再生するときは、それが発行者や作成者によるものであることがわかるようにします。

[videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) を使用して動画の分析情報を取得する方法については、「[Video Insights (ビデオの分析情報)](./video-insights.md)」を参照してください。

## <a name="filtering-videos"></a>動画のフィルタリング

Video Search API の既定の動作では、クエリとの関連性が高い動画がすべて返されます。 無料動画や長さが 5 分未満の動画のみを必要する場合は、次のフィルター クエリ パラメーターを使用します。

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing)&mdash; 料金で動画をフィルタリング (例: 無料動画や有料動画など)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution)&mdash; 解像度で動画をフィルタリング (例: 720p 以上の解像度の動画など)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength)&mdash; 動画の長さで動画をフィルタリング (例: 長さが 5 分未満の動画など)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness)&mdash; 新しさで動画をフィルタリング (例: この 1 週間に Bing によって検出された動画など)

特定のドメインから動画を得るには、クエリ文字列に [site:](http://msdn.microsoft.com/library/ff795613.aspx) クエリ演算子を含めます。

> [!NOTE]
> クエリによっては、`site:` クエリ演算子を使用した場合、[safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch) 設定にかかわらず、成人向けのコンテンツが応答に含まれることがあります。 `site:` の使用は、そのサイト上のコンテンツを承知していて、なおかつ成人向けのコンテンツが含まれていても問題がないシナリオに限定してください。

次の例は、ContosoSailing.com から、Bing が過去 1 か月に検出した 720p 以上の解像度を持つ無料動画を取得する方法を示しています。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>クエリの展開

Bing がクエリを展開して元の検索を絞り込むことができる場合、[Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) オブジェクトには `queryExpansions` フィールドが含まれます。 たとえば、クエリが *Cleaning Gutters* だった場合、展開されるクエリには、Gutter Cleaning **Tools**、Cleaning Gutters **From the Ground**、Gutter Cleaning **Machine**、および **Easy** Gutter Cleaning となる場合があります。

次の例は、*Cleaning Gutters* の展開されたクエリを示しています。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

`queryExpansions` フィールドには、[Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) オブジェクトのリストが含まれています。 `text` フィールドには、展開されたクエリが格納され、`displayText` フィールドには、展開語句が格納されます。 ユーザーが本当に探しているのは、展開されたクエリ文字列であることも考えられます。そのような場合に備えて、text フィールドと thumbnail フィールドを使用して、展開されたクエリ文字列をユーザーに表示することができます。 サムネイルやテキストは、`webSearchUrl` URL または `searchLink` URL を使用して、クリック可能な状態にしましょう。 ユーザーを Bing の検索結果に誘導する場合は `webSearchUrl` を使用し、独自の結果ページを用意する場合は `searchLink` を使用します。

## <a name="pivoting-the-query"></a>クエリのピボット

元の検索クエリを Bing が分割できる場合、[Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) オブジェクトには `pivotSuggestions` フィールドが存在します。 たとえば、元のクエリが「*Cleaning Gutters*」である場合、そのクエリが Bing によって *Cleaning* と *Gutters* に分割される可能性があります。

次の例は、*Cleaning Gutters* に関するピボット候補を示しています。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

応答には、ピボットごとにおすすめクエリを含んだ [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) オブジェクトのリストが格納されます。 `text` フィールドには、おすすめクエリが格納され、`displayText` フィールには、元のクエリ内のピボットを置き換えた語句が格納されます  たとえば、Window Cleaning などです。

ユーザーが本当に探しているのは、展開されたクエリ文字列であることも考えられます。そのような場合に備えて、`text` フィールドと `thumbnail` フィールドを使用して、展開されたクエリ文字列をユーザーに表示することができます。 サムネイルやテキストは、`webSearchUrl` URL または `searchLink` URL を使用して、クリック可能な状態にしましょう。 ユーザーを Bing の検索結果に誘導する場合は `webSearchUrl` を使用し、独自の結果ページを用意する場合は `searchLink` を使用します。

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

実際に要求を送信してみるには、[最初の要求を行う](./quick-start.md)に関するページを参照してください。

サブスクリプション キーを取得するには、「[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api)」をご覧ください。

[Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) リファレンスを活用してください。 リファレンスには、検索結果を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧が記載されています。 また、応答オブジェクトの定義も記載されています。 

検索ボックスのユーザー エクスペリエンスを高めるには、[Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) に関するページを参照してください。 ユーザーによって検索語が入力されている最中に、この API を呼び出すことで、他のユーザーによって使用された関連性の高い検索語を取得できます。

検索結果の使用に関するルールを逸脱しないよう、[Bing の使用上および表示上の要件](./useanddisplayrequirements.md)に関するページを必ず読んでください。

Video Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリとの関連性が高い結果の総数のサブセットです。 表示できる動画の数の見積もりは、応答の `totalEstimatedMatches` フィールドに格納されます。 ページをめくるように残りの動画を表示する方法について詳しくは、[動画のページ移動](./paging-videos.md)に関するページを参照してください。

ビデオに関する分析情報を取得する方法の詳細については、[ビデオの分析情報の取得](./video-insights.md)に関するページを参照してください。

急上昇中の動画を取得する方法の詳細については、[急上昇中の動画](./trending-videos.md)に関するページを参照してください。