---
title: セマンティック応答を構築する
titleSuffix: Azure Cognitive Search
description: Cognitive Search のセマンティック ランク付けアルゴリズムについて説明し、結果セットから "セマンティック回答" と "セマンティック キャプション" を構成する方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678760"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Azure Cognitive Search のセマンティック ランク付けおよび応答

> [!IMPORTANT]
> セマンティック ランク付けアルゴリズムとセマンティック回答およびキャプションはパブリック プレビュー段階にあり、プレビュー REST API を介してのみ利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。

セマンティック ランク付けでは、キーワードではなく自然言語で表現されたクエリ用にトレーニングされたセマンティック ランク付けモデルを使用して上位の一致を再ランク付けすることにより、検索結果の精度を向上させます。

この記事では、セマンティック ランク付けアルゴリズムについて説明し、セマンティック応答がどのように形成されるかについて説明します。 応答には、キャプション (プレーンテキストと強調表示付きの両方) と回答 (省略可能) が含まれます。

+ セマンティック キャプションは、検索結果から抽出されたクエリに関連するテキストの一節です。 これらは、個々のコンテンツ フィールドが結果ページに対して大きすぎる場合に、結果を要約するのに役立ちます。 セマンティック強調表示を提供するキャプションにより、ユーザーはクエリ結果にさっと目を通して最も関連性の高いドキュメントを見つけることができるため、全体的なユーザー エクスペリエンスが向上します。

+ セマンティック回答では、Bing の機械学習モデルを使用して、質問のように見えるクエリに対する回答が作成されます。 クエリの結果セットの上位のドキュメントから抽出された、クエリに最も関連する節の一覧から回答が選択されます。 回答は、クエリ応答ペイロード内の独立した最上位オブジェクトとして返されます。これを検索結果と共に検索ページに表示するように選択できます。

## <a name="prerequisites"></a>前提条件

+ セマンティック クエリ型を使用して作成されたクエリ。 詳細については、「[セマンティック クエリを作成する](semantic-how-to-query-request.md)」を参照してください。

## <a name="understanding-a-semantic-response"></a>セマンティック応答を理解する

セマンティック応答には、スコア、キャプション、および回答用の新しいプロパティが含まれています。 セマンティック応答は、[フルテキスト検索エンジン](search-lucene-query-architecture.md)によって返された上位 50 件の結果を使用して標準応答から構築され、セマンティック ランカーを使用して再ランク付けされます。 50 を超える値を指定した場合は追加の結果が返されます。ただし、それらは意味的に再ランク付けされません。

すべてのクエリと同様に、応答は、取得可能としてマークされているすべてのフィールド、または select ステートメントに指定されているフィールドのみで構成されます。 また、"回答" フィールドと "キャプション" も含まれます。

+ セマンティック結果ごとに、既定で 1 つの "回答" があります。これは個別のフィールドとして返され、検索ページでレンダリングするために選択できます。 最大で 5 つまで指定できます。 回答の作成は自動的に行われます。最初の結果のすべてのドキュメントを読み取り、抽出型要約を実行し、続いてマシン読解を実行し、最後に回答フィールドでユーザーの質問に対する直接回答を昇格させます。

+ "キャプション" は、ドキュメント コンテンツの抽出ベースの概要作成であり、プレーンテキストまたは強調表示付きで返されます。 キャプションは自動的に含まれ、抑制することはできません。 強調表示は、マシン読解を使用して適用され、強調する必要がある文字列を識別します。 強調表示によって最も関連性の高い一節に注目を集めることができるため、結果のページをすばやくスキャンして適切なドキュメントを見つけることができます。

意味的に再ランク付けされた結果セットにより、結果が @search.rerankerScore 値で並べ替えられます。 OrderBy 句を追加した場合、セマンティック クエリでサポートされていないため、HTTP 400 エラーが返されます。

## <a name="example"></a>例

@search.rerankerScore は標準の @search.score と共に存在し、結果を再ランク付けするために使用されます。

次のクエリがあるとします。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

セマンティック応答を表す次の結果が表示されることが予想されます。 これらの結果は、"@search.rerankerScore" でランク付けされた上位 3 件の応答のみを示しています。 Oceanside Resort が現在 1 位にランク付けされていることに注目してください。 既定のランク付け "@search.score" では、この結果は Trails End の後の 2 位で返されます。

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>次のステップ

+ [セマンティック検索の概要](semantic-search-overview.md)
+ [類似性アルゴリズム](index-ranking-similarity.md)
+ [セマンティック クエリを作成する](semantic-how-to-query-request.md)
+ [基本的なクエリの作成](search-query-create.md)