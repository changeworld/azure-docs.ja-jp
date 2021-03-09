---
title: セマンティック クエリを作成する
titleSuffix: Azure Cognitive Search
description: ディープ ラーニング モデルをクエリ処理にアタッチし、検索順位と関連性の一部として意図とコンテキストを推測するために、セマンティック クエリ型を設定します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 7551ef88c2251b64cf6f6db1de4fed22db2c69e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693647"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Cognitive Search でセマンティック クエリを作成する

> [!IMPORTANT]
> セマンティック クエリ型はパブリック プレビュー段階にあり、プレビューの REST API および Azure portal を通じて利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。 最初のプレビュー期間中は、セマンティック検索に対して料金は発生しません。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

この記事では、セマンティック ランク付けを使用する検索要求を作成し、セマンティック キャプションと回答を生成する方法について説明します。

## <a name="prerequisites"></a>前提条件

+ 米国中北部、米国西部、米国西部 2、米国東部 2、北ヨーロッパ、西ヨーロッパのいずれかのリージョンにある、Standard レベル (S1、S2、S3) の検索サービス。 これらのリージョンのいずれかに既存の S1 以上のサービスがある場合は、新しいサービスを作成しなくてもアクセスを要求できます。

+ セマンティック検索プレビューへのアクセス: [サインアップ](https://aka.ms/SemanticSearchPreviewSignup)

+ 英語のコンテンツを含む既存の検索インデックス

+ クエリを送信するための検索クライアント

  検索クライアントは、クエリ要求でプレビューの REST API をサポートする必要があります。 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md)、または自分で変更したコードを使用して、プレビュー API への REST 呼び出しを行うことができます。 Azure portal で [Search エクスプローラー](search-explorer.md)を使用してセマンティック クエリを送信することもできます。

+ この記事で説明するセマンティック オプションやその他のパラメーターを使用した [Search Documents](/rest/api/searchservice/preview-api/search-documents) 要求。

## <a name="whats-a-semantic-query"></a>セマンティック クエリとは

Cognitive Search では、クエリは、クエリ処理と応答の形を決定するパラメーター化された要求です。 "*セマンティック クエリ*" では、一致する結果のコンテキストと意味を評価し、関連性の高い一致を最上位に昇格させることができるセマンティック ランク付けアルゴリズムを呼び出すパラメーターが追加されます。

次の要求は、基本的なセマンティック クエリ (回答なし) を表しています。

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us",  
}
```

Cognitive Search のすべてのクエリと同様に、要求は、単一インデックスのドキュメント コレクションを対象とします。 さらに、セマンティック クエリでは、非セマンティック クエリと同じ解析、分析、およびスキャンのシーケンスが実行されます。 違いは、関連性の計算方法にあります。 このプレビュー リリースで定義されているように、セマンティック クエリでは、高度なアルゴリズムを使用して "*結果*" が再処理されます。これにより、既定の類似性ランク付けアルゴリズムによって割り当てられるスコアではなく、セマンティック ランカーによって最も関連性があると見なされる一致を明らかにする方法が提供されます。 

最初の結果の上位 50 件の一致のみを意味的にランク付けすることができます。すべての結果には応答にキャプションが含まれます。 必要に応じて、要求に **`answer`** パラメーターを指定して、可能性のある回答を抽出できます。 このモデルでは、クエリに対して可能性のある回答を最大で 5 つ作成します。これは、検索ページの最上部に表示するために選択できます。

## <a name="query-using-rest-apis"></a>REST API を使用してクエリを実行する

REST API の完全な仕様については、「[Search Documents (REST プレビュー)](/rest/api/searchservice/preview-api/search-documents)」を参照してください。

セマンティック クエリは、"花粉媒介者に最適な植物は何か" や "卵を揚げる方法" などの自由形式の質問を対象としています。 応答に回答を含める場合は、省略可能な **`answer`** パラメーターを要求に追加できます。

次の例では、hotels-sample-index を使用して、セマンティック回答およびキャプションを含むセマンティック クエリ要求を作成します。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

### <a name="formulate-the-request"></a>要求を作成する

1. **`"queryType"`** を "semantic" に、 **`"queryLanguage"`** を "en-us" に設定します。 どちらのパラメーターも必須です。

   queryLanguage は、インデックス スキーマのフィールド定義に割り当てられている[言語アナライザー](index-add-language-analyzers.md)と一致している必要があります。 queryLanguage が "en-us" の場合、言語アナライザーも英語のバリアント ("en.microsoft" または "en.lucene") である必要があります。 keyword や simple など、言語に依存しないアナライザーは、queryLanguage 値と競合しません。

   クエリ要求で[スペル修正](speller-how-to-add.md)も使用している場合、設定した queryLanguage は、speller、answers、および captions に等しく適用されます。 個々のパーツに対するオーバーライドはありません。 

   検索インデックスのコンテンツは複数の言語で構成できますが、通常、クエリ入力は 1 つの言語で行われます。 検索エンジンでは、queryLanguage、言語アナライザー、およびコンテンツが構成されている言語の互換性が確認されません。そのため、誤った結果が生成されないように、適切にクエリのスコープを設定してください。

<a name="searchfields"></a>

1. **`"searchFields"`** を設定します (省略可能ですが、設定することをお勧めします)。

   セマンティック クエリでは、"searchFields" 内のフィールドの順序は、セマンティック ランク付けでのフィールドの優先度または相対的な重要性を反映したものとなります。 最上位レベルの文字列フィールド (スタンドアロンまたはコレクション内) のみが使用されます。 searchFields は単純および完全な Lucene クエリでは他の動作をするため (暗黙の優先順位はありません)、非文字列フィールドおよびサブフィールドによってエラーが発生することはありませんが、これらはセマンティック ランク付けでは使用されません。

   searchFields を指定する場合は、次のガイドラインに従ってください。

   + HotelName やタイトルなどの簡潔なフィールドは、Description などの詳細フィールドの前に置く必要があります。

   + インデックスにテキスト (`www.domain.com/?id=23463&param=eis` のようにマシンにフォーカスされている形式ではなく、`www.domain.com/name-of-the-document-and-other-details` のように人間が読める形式) の URL フィールドがある場合は、これをリストの 2 番目に配置します (簡潔なタイトル フィールドがない場合は先頭に配置します)。

   + 指定されたフィールドが 1 つしかない場合、そのフィールドはドキュメントのセマンティック ランク付けの説明フィールドと見なされます。  

   + フィールドが指定されていない場合は、すべての検索可能なフィールドがドキュメントのセマンティック ランク付けの対象と見なされます。 ただし、検索インデックスから最適な結果が得られない可能性があるため、これはお勧めしません。

1. 既存の要求に **`"orderBy"`** 句が存在する場合は削除します。 結果の順序付けにはセマンティック スコアが使用されます。明示的な並べ替えロジックを含めると、HTTP 400 エラーが返されます。

1. 必要に応じて、 **`"answers"`** を追加して "extractive" に設定し、複数の回答が必要な場合はその数を指定します。

1. 必要に応じて、キャプションに適用される強調表示のスタイルをカスタマイズします。 キャプションでは、ドキュメント内の重要な一説 (応答を要約する部分) に強調の書式設定が適用されます。 既定では、 `<em>`です。 書式設定の種類 (黄色の背景など) を指定する場合は、highlightPreTag と highlightPostTag を設定できます。

1. 要求で必要なその他のパラメーターを指定します。 [speller](speller-how-to-add.md)、[select](search-query-odata-select.md)、count などのパラメーターを使用すると、要求の品質と応答の読みやすさが向上します。

### <a name="review-the-response"></a>応答を確認する

上記のクエリに対する応答では、最上位の選択として次の一致が返されます。 キャプションについては、プレーンテキスト バージョンと強調表示されたバージョンが自動的に返されます。 セマンティック応答の詳細については、[セマンティック ランク付けと応答](semantic-how-to-query-response.md)に関するページを参照してください。

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>セマンティック クエリで使用されるパラメーター

次の表は、セマンティック クエリで使用されるクエリ パラメーターを全体的に参照できるようにまとめたものです。 すべてのパラメーターの一覧については、「[Search Documents (REST プレビュー)](/rest/api/searchservice/preview-api/search-documents)」を参照してください

| パラメーター | 種類 | 説明 |
|-----------|-------|-------------|
| queryType | String | 有効な値は、simple、full、semantic です。 セマンティック クエリには、"semantic" の値が必要です。 |
| queryLanguage | String | セマンティック クエリに必要です。 現在、"en-us" のみが実装されています。 |
| searchFields | String | 検索可能なフィールドのコンマ区切りの一覧。 省略可能ですが、指定することをお勧めします。 セマンティック ランク付けを行うフィールドを指定します。 </br></br>単純および完全なクエリの種類とは対照的に、フィールドの順番によって優先順位が決まります。|
| answers |String | セマンティック回答を結果に含めるかどうかを指定する省略可能なフィールド。 現在、"extractive" のみが実装されています。 回答は、最大 5 つを返すように構成できます。 この例の "extractive|count3" は回答の数が 3 であることを示しています。 既定値は 1 です。|

## <a name="query-with-search-explorer"></a>検索エクスプローラーを使用したクエリ実行

次のクエリは、API バージョン 2020-06-30-Preview を使用して組み込みの Hotels サンプル インデックスを対象としています。このクエリは、Search エクスプローラーで実行されます。 `$select` 句によって結果が少数のフィールドに制限されます。これにより、Search エクスプローラーの詳細な JSON でのスキャンが容易になります。

### <a name="with-querytypesemantic"></a>With queryType=semantic

```json
search=I want a nice hotel on the water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

最初の少数の結果は次のようになります。

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>With queryType (既定値)

比較のために、`&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` を削除して上記と同じクエリを実行します。 これらの結果に `"@search.rerankerScore"` が含まれていないこと、および上位 3 件に異なるホテルが表示されていることに注意してください。

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>次のステップ

セマンティック ランク付けと応答は最初の結果セットを基に構築されていることを思い出してください。 最初の結果の品質を向上させるロジックは、セマンティック検索に引き継がれます。 次の手順として、最初の結果に寄与する機能を確認します。これには、文字列のトークン化の方法に影響を与えるアナライザー、結果を調整できるスコアリング プロファイル、および既定の関連性アルゴリズムが含まれます。

+ [テキスト処理のためのアナライザー](search-analyzers.md)
+ [Cognitive Search での類似性とスコアリング](index-similarity-and-scoring.md)
+ [スコアリング プロファイルの追加](index-add-scoring-profiles.md)
+ [セマンティック検索の概要](semantic-search-overview.md)
+ [クエリ用語にスペル チェックを追加する](speller-how-to-add.md)
