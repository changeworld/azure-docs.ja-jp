---
title: セマンティック クエリを作成する
titleSuffix: Azure Cognitive Search
description: ディープ ラーニング モデルをクエリ処理にアタッチし、検索順位と関連性の一部として意図とコンテキストを推測するために、セマンティック クエリ型を設定します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: e89f449d62a86d8a935647d0a60af25415f65b85
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058337"
---
# <a name="create-a-query-that-invokes-semantic-ranking-and-returns-semantic-captions"></a>セマンティックのランク付けを呼び出してセマンティック キャプションを返すクエリを作成する

> [!IMPORTANT]
> セマンティック検索はパブリック プレビュー段階にあり、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下で提供されます。 Azure portal、プレビュー REST API、ベータ版 SDK から利用できます。 これらの機能は課金対象です。 詳細については、「[可用性と料金](semantic-search-overview.md#availability-and-pricing)」を参照してください。

セマンティック検索は Azure Cognitive Search のプレミアム機能であり、結果セットに対してセマンティック ランク付けアルゴリズムを呼び出し、セマンティック キャプション (および必要に応じて[セマンティック回答](semantic-answers.md)) を返し、最も関連性の高い用語と語句を強調表示します。 "セマンティック" クエリの種類を使用して作成されたクエリ要求では、キャプションと回答の両方が返されます。

キャプションと回答は、検索ドキュメント内のテキストから逐語的に抽出されます。 セマンティック サブシステムでは、コンテンツのどの部分がキャプションまたは回答の特性を持つかは特定されますが、新しい文や語句は作成されません。 このため、セマンティック検索には、説明または定義を含むコンテンツが最も適しています。

## <a name="prerequisites"></a>前提条件

+ 米国中北部、米国西部、米国西部 2、米国東部 2、北ヨーロッパ、西ヨーロッパのいずれかのリージョンにある、Standard レベル (S1、S2、S3) の Cognitive Search サービス。 これらのリージョンのいずれかに既存の S1 以上のサービスがある場合は、新しいサービスを作成しなくてもプレビューにサインアップできます。

+ [プレビューにサインアップする](https://aka.ms/SemanticSearchPreviewSignup) 予想されるターンアラウンドは約 2 営業日です。

+ [サポートされている言語](/rest/api/searchservice/preview-api/search-documents#queryLanguage)のコンテンツを含む既存の検索インデックス。 セマンティック検索は、情報または説明であるコンテンツに最適です。

+ クエリを送信するための検索クライアント。

  検索クライアントは、クエリ要求でプレビューの REST API をサポートする必要があります。 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md)、またはプレビューの API への REST 呼び出しを行うコードを使用できます。 Azure portal で [Search エクスプローラー](search-explorer.md)を使用してセマンティック クエリを送信することもできます。 [Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) を使用することもできます。

+ [クエリ要求](/rest/api/searchservice/preview-api/search-documents)には、この記事で説明する `queryType=semantic` やその他のパラメーターを含める必要があります。

## <a name="whats-a-semantic-query-type"></a>セマンティック クエリの種類とは

Cognitive Search では、クエリは、クエリ処理と応答の形を決定するパラメーター化された要求です。 "*セマンティック クエリ*" には、一致する結果のコンテキストと意味を評価し、関連性の高い一致を最上位に昇格させ、セマンティック回答とキャプションを返すことができるセマンティック再ランク付けモデルを呼び出す [パラメーターがあります](#query-using-rest)。

次の要求は、最小限のセマンティック クエリ (回答なし) を表しています。

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Cognitive Search のすべてのクエリと同様に、要求は、単一インデックスのドキュメント コレクションを対象とします。 さらに、セマンティック クエリによって実行される分析、スキャン、およびスコアリングのシーケンスは、非セマンティック クエリと同じです。 

違いは、関連性とスコアリングにあります。 このプレビュー リリースで定義されているように、セマンティック クエリとは、その "*結果*" がセマンティック言語モデルを使用して再ランク付けされるものであり、既定の類似性ランク付けアルゴリズムによって割り当てられるスコアではなく、セマンティック ランカーによって最も関連性があると見なされる一致を明らかにする方法がとられます。

最初の結果の上位 50 件の一致のみを意味的にランク付けすることができます。すべての結果には応答にキャプションが含まれます。 必要に応じて、要求に **`answer`** パラメーターを指定して、可能性のある回答を抽出できます。 詳細については、[セマンティック回答](semantic-answers.md)に関するページを参照してください。

## <a name="query-in-azure-portal"></a>Azure portal でのクエリ

[検索エクスプローラー](search-explorer.md)は更新され、セマンティック クエリ用のオプションが含められました。 これらのオプションは、次の手順を完了した後にポータルに表示されます。

1. ポータルを構文 `https://portal.azure.com/?feature.semanticSearch=true` を使用して、プレビューが有効になっている検索サービスで開きます。

1. 概要ページの最上部で **[検索エクスプローラー]** をクリックします。

1. [サポートされている言語](/rest/api/searchservice/preview-api/search-documents#queryLanguage)のコンテンツがあるインデックスを選択します。

1. 検索エクスプローラーで、セマンティック クエリ、searchFields、スペル修正を有効にするクエリ オプションを設定します。 必要なクエリ パラメーターをクエリ文字列に貼り付けることもできます。

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="検索エクスプローラー内のクエリ オプション" border="true":::

## <a name="query-using-rest"></a>REST を使用してクエリを実行する

[ドキュメントの検索 (REST プレビュー)](/rest/api/searchservice/preview-api/search-documents) を使用して、プログラムで要求を作成します。 応答には、キャプションと強調表示が自動的に含められます。 応答でスペル修正または回答が必要な場合は、 **`speller`** または **`answers`** を要求に追加します。

次の例では、[hotels-sample-index](search-get-started-portal.md) を使用して、スペル チェック、セマンティック回答、およびキャプションを含むセマンティック クエリ要求を作成します。

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

次の表は、セマンティック クエリで使用されるパラメーターをまとめたものです。 要求内のすべてのパラメーターの一覧については、[ドキュメントの検索 (REST プレビュー)](/rest/api/searchservice/preview-api/search-documents) に関する記事を参照してください

| パラメーター | Type | 説明 |
|-----------|-------|-------------|
| queryType | String | 有効な値は、simple、full、semantic です。 セマンティック クエリには、"semantic" の値が必要です。 |
| queryLanguage | String | セマンティック クエリに必要です。 指定する辞書は、セマンティック ランク付け、キャプション、回答、およびスペル チェックに等しく適用されます。 詳細については、[サポートされている言語 (REST API リファレンス)](/rest/api/searchservice/preview-api/search-documents#queryLanguage) に関するセクションを参照してください。 |
| searchFields | String | 検索可能なフィールドのコンマ区切りの一覧。 セマンティック ランク付けを行うフィールドを指定します。ここから、キャプションと回答が抽出されます。 </br></br>単純および完全なクエリの種類とは対照的に、フィールドの順番によって優先順位が決まります。 使用方法の詳細については、「[手順 2: searchFields を設定する](#searchfields)」を参照してください。 |
| スペル チェック | String | 検索エンジンに到達する前にスペルミスを修正する省略可能なパラメーターであり、セマンティック クエリに固有のものではない。 詳細については、[クエリへのスペル修正の追加](speller-how-to-add.md)に関するページを参照してください。 |
| answers |String | セマンティック回答を結果に含めるかどうかを指定する省略可能なパラメーター。 現在、"extractive" のみが実装されています。 回答は、最大 10 個を返すように構成できます。 既定値は 1 です。 `extractive\|count-3` という例は、回答の数が 3 であることを示しています。 詳細については、[セマンティック回答を返す](semantic-answers.md)に関するページを参照してください。|

### <a name="formulate-the-request"></a>要求を作成する

このセクションでは、クエリの構成について説明します。

#### <a name="step-1-set-querytype-and-querylanguage"></a>Step 1: queryType と queryLanguage を設定する

REST に次のパラメーターを追加します。 どちらのパラメーターも必須です。

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

queryLanguage は[サポートされている言語](/rest/api/searchservice/preview-api/search-documents#queryLanguage)でなければならず、インデックス スキーマのフィールド定義に割り当てられている[言語アナライザー](index-add-language-analyzers.md)と一致している必要があります。 たとえば、フランス語アナライザー ("fr.microsoft" や "fr.lucene" など) を使用してフランス語の文字列にインデックスを付けた場合、queryLanguage もフランス語のバリアントである必要があります。

クエリ要求で[スペル修正](speller-how-to-add.md)も使用している場合、設定した queryLanguage は、speller、answers、および captions に等しく適用されます。 個々のパーツに対するオーバーライドはありません。 スペル チェックでサポートされる[言語が少ない](speller-how-to-add.md#supported-languages)ので、この機能を使用している場合は、queryLanguage をその一覧のいずれかに設定する必要があります。

検索インデックスのコンテンツは複数の言語で構成できますが、通常、クエリ入力は 1 つの言語で行われます。 検索エンジンでは、queryLanguage、言語アナライザー、およびコンテンツが構成されている言語の互換性が確認されません。そのため、誤った結果が生成されないように、適切にクエリのスコープを設定してください。

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>手順 2: searchFields を設定する

searchFields を要求に追加します。 これは省略可能ですが、強くお勧めします。

```json
"searchFields": "HotelName,Category,Description",
```

searchFields パラメーターは、クエリに対する "セマンティックの類似性" について評価すべき一節を識別するために使用されます。 プレビューでは、どのフィールドが処理するのに最も重要かに関するヒントがモデルに必要なため、searchFields を空白のままにすることはお勧めしません。

他のパラメーターとは対照的に、searchFields は新しくはありません。 既に searchFields を、既存のコードの単純または完全な Lucene クエリで使用している可能性があります。 その場合はパラメーターの使用方法を見直して、セマンティック クエリの種類に切り替えるときにフィールドの順序を調べられるようにしてください。

##### <a name="allowed-data-types"></a>許可されるデータ型

searchFields を設定するときは、次の[サポートされているデータ型](/rest/api/searchservice/supported-data-types)のフィールドのみを選択してください。 無効なフィールドを含めると、エラーは発生しませんが、セマンティック ランク付けでそれらのフィールドは使用されません。

| データ型 | hotels-sample-index からの例 |
|-----------|----------------------------------|
| Edm.String | HotelName、Category、Description |
| Edm.ComplexType | Address.StreetNumber、Address.City、Address.StateProvince、Address.PostalCode |
| Collection(Edm.String) | タグ (コンマ区切りリストの文字列) |

##### <a name="order-of-fields-in-searchfields"></a>SearchFields 内のフィールドの順序

フィールドの順序が重要な理由は、セマンティック ランカーは処理できる内容の量を制限しながら合理的な応答時間を提供するためです。 リストの先頭にあるフィールドの内容は含まれる可能性が高くなりますが、末尾の内容は上限に達した場合に切り捨てられる可能性があります。 詳細については、[セマンティック ランク付け中の前処理](semantic-ranking.md#pre-processing)に関する記事を参照してください。

+ 指定しているフィールドが 1 つだけの場合は、ドキュメントのメイン コンテンツなど、セマンティック クエリに対する回答が見つかる可能性のある説明フィールドを選択します。 

+ searchFields の 2 つ以上のフィールドの場合:

  + 最初のフィールドは、常に簡潔 (タイトルや名前など) にする必要があります。25 単語未満の文字列であると理想的です。

  + インデックスに、`www.domain.com/?id=23463&param=eis` のようにマシンにフォーカスされている形式でなく、`www.domain.com/name-of-the-document-and-other-details` のように人間が読める形式の URL フィールドがある場合は、これをリストの 2 番目に配置します (または、簡潔なタイトル フィールドがない場合は先頭)。

  + 上のフィールドの後に、セマンティック クエリの回答が見つかる可能性がある他の説明フィールドを配置します (ドキュメントのメイン コンテンツなど)。

#### <a name="step-3-remove-or-bracket-query-features-that-bypass-relevance-scoring"></a>手順 3: 関連性スコアリングをバイパスするクエリ機能を削除またはブラケット化する

Cognitive Search の一部のクエリ機能は関連性スコアリングを受けず、いくつかはフルテキスト検索エンジンを完全にバイパスします。 使用するクエリ ロジックに以下の機能が含まれている場合、結果に対する関連性スコアまたはセマンティック ランク付けは得られません。

+ フィルター、あいまい検索クエリ、および正規表現は、トークン化されていないテキストを反復処理し、コンテンツ内の逐語的一致をスキャンします。 上のすべてのクエリ形式の検索スコアは同じ 1.0 であり、セマンティック順位付けのための意味のある入力は提供しません。

+ 特定のフィールドで並べ替え (orderBy 句) を実行すると、検索スコアとセマンティック スコアもオーバーライドされます。 結果の並べ替えにセマンティック スコアを使用すると (明示的な並べ替えロジックを含む)、HTTP 400 エラーが返されます。

#### <a name="step-4-add-answers"></a>手順 4: 回答を追加する

回答を提供する追加の処理を含める場合は、必要に応じて "answers" を追加します。 このパラメーターの詳細については、[セマンティック回答を指定する方法](semantic-answers.md)に関する記事を参照してください。

```json
"answers": "extractive|count-3",
```

回答 (およびキャプション) は、searchFields にリストされているフィールド内の節から抽出されます。 これが、コンテンツ豊富なフィールドを searchFields に含める理由です。これにより、応答で最良の回答を得ることができます。 回答は、すべての要求で保証されるわけではありません。 クエリは質問のように見えなければならず、コンテンツには回答のように見えるテキストがなければなりません。

#### <a name="step-5-add-other-parameters"></a>手順 5: その他のパラメーターを追加する

要求で必要なその他のパラメーターを設定します。 [speller](speller-how-to-add.md)、[select](search-query-odata-select.md)、count などのパラメーターを使用すると、要求の品質と応答の読みやすさが向上します。

```json
"speller": "lexicon",
"select": "HotelId,HotelName,Description,Category",
"count": true,
"highlightPreTag": "<mark>",
"highlightPostTag": "</mark>",
```

強調表示スタイルが、応答のキャプションに適用されます。 既定のスタイルを使用することも、あるいはキャプションに適用される強調表示スタイルをカスタマイズすることもできます。 キャプションでは、ドキュメント内の重要な一説 (応答を要約する部分) に強調の書式設定が適用されます。 既定では、 `<em>`です。 書式設定の種類 (黄色の背景など) を指定する場合は、highlightPreTag と highlightPostTag を設定できます。

## <a name="query-using-azure-sdks"></a>Azure SDK を使用してクエリを実行する

Azure SDK のベータ版には、セマンティック検索のサポートが含まれています。 SDK はベータ版であるため、ドキュメントやサンプルはありませんが、上の REST API のセクションを参照して、API の動作について把握することができます。

| Azure SDK | Package |
|-----------|---------|
| .NET | [Azure.Search.Documents package 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)  |
| Java | [com.azure:azure-search-documents 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar)  |
| JavaScript | [azure/search-documents 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2)|
| Python | [azure-search-documents 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

## <a name="evaluate-the-response"></a>応答を評価する

すべてのクエリと同様に、応答は、取得可能としてマークされているすべてのフィールド、または select パラメーターに指定されているフィールドのみで構成されます。 これには、元の関連性スコアが含まれます。また、要求の作成方法に応じて、カウントまたはバッチ結果が含まれる場合もあります。

セマンティック クエリでは、次の追加の要素が応答に含められます: 意味的にランク付けされた新しい関連性スコア、プレーン テキスト内の強調表示されたキャプション、および必要に応じて回答。

クライアント アプリでは、特定のフィールドの内容全体ではなく、キャプションを一致の説明として含めるように、検索ページを構成することができます。 これは、検索結果ページにおいて個々のフィールドの密度が高すぎる場合に役立ちます。

上記のクエリ例に対する応答では、最上位の選択として次の一致が返されます。 キャプションについては、プレーンテキスト バージョンと強調表示されたバージョンが自動的に返されます。 この例では回答が省略されています。この特定のクエリとコーパスに対してそれを特定できなかったためです。

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>次のステップ

セマンティック ランク付けと応答は最初の結果セットを基に構築されていることを思い出してください。 最初の結果の品質を向上させるロジックは、セマンティック検索に引き継がれます。 次の手順として、最初の結果に寄与する機能を確認します。これには、文字列のトークン化の方法に影響を与えるアナライザー、結果を調整できるスコアリング プロファイル、および既定の関連性アルゴリズムが含まれます。

+ [テキスト処理のためのアナライザー](search-analyzers.md)
+ [類似性ランク付けアルゴリズム](index-similarity-and-scoring.md)
+ [スコアリング プロファイル](index-add-scoring-profiles.md)
+ [セマンティック検索の概要](semantic-search-overview.md)
+ [セマンティック ランク付けアルゴリズム](semantic-ranking.md)
