---
title: ファセット ナビゲーション カテゴリ階層を追加する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search と統合するアプリケーションで自律型のフィルター処理のためのファセット ナビゲーションを追加します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 55473848a8c8e8e7f9c6072566eb73d823feaba2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561311"
---
# <a name="add-faceted-navigation-to-a-search-app"></a>検索アプリにファセット ナビゲーションを追加する

ファセット ナビゲーションは、検索アプリでのクエリの結果に対する自律型のドリルダウン フィルター処理に使用されます。そこでは、検索範囲をドキュメントのグループ (カテゴリやブランドなど) に指定するためのフォーム コントロールをアプリケーションが提供し、Azure Cognitive Search は、このエクスペリエンスを支えるデータ構造とフィルターを提供します。 

この記事では、Azure Cognitive Search でファセット ナビゲーション構造を作成するための基本的な手順について説明します。

> [!div class="checklist"]
> * インデックス内のフィールド属性を設定する
> * 要求と応答を構造化する
> * プレゼンテーション層にナビゲーション コントロールとフィルターを追加する

プレゼンテーション層内のコードは、ファセット ナビゲーション エクスペリエンスで最も複雑な処理を実行します。 この記事の最後に一覧表示されているデモとサンプルでは、すべてを 1 つにまとめる方法を示す機能するコードが提供されます。

## <a name="faceted-navigation-in-a-search-page"></a>検索ページでのファセット ナビゲーション

ファセットは動的であり、クエリで返されます。 検索の応答には、結果のドキュメント内を移動するために使用されるすべてのファセット カテゴリが表示されます。 まずクエリが実行された後、現在の結果からファセットがプルされ、ファセット ナビゲーション構造にアセンブルされます。

Cognitive Search では、ファセットはレイヤーの深さが 1 つであり、階層化できません。 構造化されたファセット ナビゲーションになじみがない場合は、次の例の左側にそれが示されています。 カウントは、ファセットごとの一致の数を示しています。 同じドキュメントを複数のファセットで表現できます。

:::image source="media/tutorial-csharp-create-first-app/azure-search-facet-nav.png" alt-text="ファセット検索の結果":::

ファセットを使用すると、探しているものが見つけやすくなり、検索結果がゼロ件になることはありません。 開発者は、ファセットを使用することで検索インデックスのナビゲーションに最も役立つ検索条件を公開できます。

## <a name="enable-facets-in-the-index"></a>インデックスでファセットを有効にする

ファセットは、"facetable" 属性を true に設定すると、インデックス定義内のフィールドごとに有効になります。

厳密に必要なわけではありませんが、検索アプリケーションでファセット ナビゲーション エクスペリエンスを支える必要なフィルターを構築できるように、"filterable" 属性も設定する必要があります。

"hotels" サンプル インデックスの次の例は、単一値または短いフレーズが含まれているカーディナリティの低いフィールド "Category"、"Tags"、"Rating" での "facetable" と "filterable" を示しています。

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "Description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "HotelName", "type": "Edm.String", "facetable": false },
    { "name": "Category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint" }
  ]
}
```

### <a name="choosing-fields"></a>フィールドの選択

ファセットは、単一値フィールドのほか、コレクションに対して計算できます。 ファセット ナビゲーションで最適に機能するフィールドには、次の特性があります。

* カーディナリティが低い (検索コーパス内の各ドキュメントにまたがって繰り返される個別の値が少ない)

* ナビゲーション ツリーに適切に表示される短く、わかりやすい値 (1 つまたは 2 つの単語)

ファセット ナビゲーション構造内のファセットは、フィールド自体ではなく、そのフィールドの内容によって生成されます。 ファセットが文字列フィールド *Color* である場合、ファセットは青色、緑色、またはそのフィールドの他の任意の値になります。

ベスト プラクティスとして、null 値、スペルミスまたは大文字と小文字の不一致、同じ単語の単数と複数のバージョンがないかどうかフィールドを確認してください。 フィルターやファセットに対して字句解析や[スペル チェック](speller-how-to-add.md)は実行されません。つまり、単語が 1 文字だけ異なる場合でも、"facetable" フィールドのすべての値が潜在的なファセットになります。

### <a name="defaults-in-rest-and-azure-sdks"></a>REST および Azure SDK での既定値

いずれかの Azure SDK を使用している場合、コードではすべてのフィールド属性を指定する必要があります。 これに対して、REST API には、[データ型](/rest/api/searchservice/supported-data-types)に基づいたフィールド属性の既定値があります。 次のデータ型は、既定では "filterable" かつ "facetable" です。

* `Edm.String`
* `Edm.DateTimeOffset`
* `Edm.Boolean`
* `Edm.Int32`, `Edm.Int64`, `Edm.Double`
* 上記の任意の型のコレクション (`Collection(Edm.String)` や `Collection(Edm.Double)` など)

ファセット ナビゲーションでは、`Edm.GeographyPoint` フィールドや `Collection(Edm.GeographyPoint)` フィールドを使用することはできません。 ファセットはカーディナリティが低いフィールドで最適に機能します。 地理座標の解像度のために、特定のデータセット内で任意の 2 組の座標が等しくなることはまれです。 そのため、ファセットは地理座標ではサポートされていません。 場所でファセットするには、都市フィールドまたは地域フィールドが必要です。

> [!Tip]
> パフォーマンスとストレージの最適化のためのベスト プラクティスとして、ファセットとして使用されることのないフィールドではファセットを無効にします。 具体的には、ID や製品名などの一意の値の文字列フィールドは、ファセット ナビゲーションで誤って (無駄に) 使用されないように、`"facetable": false` に設定する必要があります。 これは、既定でフィルターとファセットを有効にする REST API の場合に特に当てはまります。

## <a name="facet-request-and-response"></a>ファセットの要求と応答

ファセットはクエリで指定され、ファセット ナビゲーション構造は応答の先頭で返されます。 要求と応答の構造はきわめて単純です。 実際に、ファセット ナビゲーションの背後にある実際の作業は、後のセクションで説明されるプレゼンテーション層で行われます。 

次の REST の例は、インデックス全体を範囲とする非修飾クエリ (`"search": "*"`) です ([組み込みのホテル サンプル](search-get-started-portal.md)を参照)。 ファセットは通常、フィールドの一覧ですが、このクエリは、下のより読みやすい応答に対して 1 つだけを示しています。

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "queryType": "simple",
    "select": "",
    "searchFields": "",
    "filter": "",
    "facets": [ "Category"], 
    "orderby": "",
    "count": true
}
```

ファセット ナビゲーション構造を完全に埋めるには、開いているクエリで検索ページを初期化することが有効です。 要求でクエリ用語を渡すとすぐに、ファセット ナビゲーション構造は、インデックス全体ではなく、結果内の一致だけが範囲となります。

上記の例に対する応答には、先頭にファセット ナビゲーション構造が含まれています。 この構造は、"Category" 値と、それぞれに対するホテルの数で構成されています。 その後に検索結果の残りが続きますが、ここでは、簡潔にするために切り捨てられています。 この例は、いくつかの理由で適切に機能します。 このフィールドのファセットの数は制限 (既定値は 10) に収まるため、そのすべてが表示され、50 軒のホテルのインデックス内のすべてのホテルが、これらのカテゴリのうちの正確に 1 つで表されます。

```json
{
    "@odata.context": "https://demo-search-svc.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "@odata.count": 50,
    "@search.facets": {
        "Category": [
            {
                "count": 13,
                "value": "Budget"
            },
            {
                "count": 12,
                "value": "Resort and Spa"
            },
            {
                "count": 9,
                "value": "Luxury"
            },
            {
                "count": 7,
                "value": "Boutique"
            },
            {
                "count": 5,
                "value": "Suite"
            },
            {
                "count": 4,
                "value": "Extended-Stay"
            }
        ]
    },
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Category": "Boutique",
            "Tags": [
                "pool",
                "air conditioning",
                "concierge"
            ],
            "ParkingIncluded": false,
  . . .
```

## <a name="facets-syntax"></a>ファセットの構文

ファセット クエリ パラメーターは "facetable" フィールドのコンマ区切りリストに設定され、データ型に応じて、さらにパラメーター化してカウント、並べ替え順序、範囲を設定できます (`count:<integer>`、`sort:<>`、`interval:<integer>`、`values:<list>`)。 ファセット パラメーターの詳細については、[REST API の「クエリ パラメーター」](/rest/api/searchservice/search-documents#query-parameters)を参照してください。

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "facets": [ "Category", "Tags,count:5", "Rating,values:1|2|3|4|5"],
    "count": true
}
```

ファセット ナビゲーション ツリーごとに、10 ファセットの既定の制限があります。 この既定値は、値のリストが管理しやすいサイズに保たれるため、ナビゲーション構造にとって意味のあるものです。 "count" に値を割り当てることによって、既定値をオーバーライドできます。 たとえば、`"Tags,count:5"` を指定すると、Tags セクションの下のタグの数が上位の 5 つに減ります。

Numeric 値と DateTime 値の場合のみ、ファセット フィールドの値を明示的に設定して (たとえば `facet=Rating,values:1|2|3|4|5`)、結果を連続した範囲 (数値または期間のどちらかに基づいた範囲) に分離できます。 あるいは、`facet=Rating,interval:1` のように "interval:" を追加できます。 

各範囲は始点として 0 を、終点としてリストの値を使用して作成され、前の範囲を除くことによって個別の間隔が作成されます。

### <a name="discrepancies-in-facet-counts"></a>ファセット数の不一致

特定の状況では、ファセットの数が結果セットと一致しないことがあります ([Azure Cognitive Search でのファセット ナビゲーション (Microsoft Q&A 質問ページ)](/answers/topics/azure-cognitive-search.html) に関する記事を参照)。

シャーディング アーキテクチャのために、ファセットの数が正しくなくなる可能性があります。 すべての検索インデックスに複数のシャードがあり、それぞれのシャードがドキュメント数によって上位 N ファセットを報告すると、単一の結果に結合されます。 一部のシャードの一致値が多く、他のシャードは少ない場合、一部のファセットの値が結果に含まれないか、または数が少なくなる可能性があります。

この動作はいつでも変わる可能性がありますが、現在発生している場合は、count:\<number> を意図的に大きい値に増やして各シャードから強制的に完全にレポートすることによって回避できます。 count: の値がフィールドの固有の値の数と等しいかそれより大きい場合、正確な結果が保証されます。 ただし、ドキュメントの数が大きい場合はパフォーマンスが低下するので、このオプションは注意して使用する必要があります。

## <a name="presentation-layer"></a>プレゼンテーション層

アプリケーション コードでのパターンは、ファセット クエリ パラメーターを使用してファセット結果と共にファセット ナビゲーション構造を返し、さらに $filter 式を使用するというものです。  フィルター式はクリック イベントを処理し、ファセットの選択に基づいて検索結果をさらに絞り込みます。

### <a name="facet-and-filter-combination"></a>ファセットとフィルターの組み合わせ

NYCJobs デモの `JobsSearch.cs` ファイルにある次のコード スニペットでは、ユーザーが Business Title ファセットから値を選択すると、選択された Business Title をフィルターに追加します。

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

ホテル サンプルからの別の例を次に示します。 次のコード スニペットでは、ユーザーがカテゴリ ファセットから値を選択すると、categorFacet をフィルターに追加します。

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

### <a name="html-for-faceted-navigation"></a>ファセット ナビゲーション用の HTML

NYCJobs サンプル アプリケーションの `index.cshtml` ファイルから取得された次の例は、検索結果ページにファセット ナビゲーションを表示するための静的な HTML 構造を示しています。 検索語句を送信するか、ファセットをオンまたはオフにすると、ファセットのリストが作成されたり、動的に再構築されたりします。

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

### <a name="build-html-dynamically"></a>HTML を動的に作成する

`index.cshtml` からの次のコード スニペット (NYCJobs デモでも同じです) は、最初のファセットである Business Title を表示するための HTML を動的に作成します。 同様の関数は、その他のファセットの HTML を動的に作成します。 各ファセットにはラベルと数があり、そのファセット結果に対して見つかった項目の数を表示します。

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

## <a name="tips-for-working-with-facets"></a>ファセットの操作に関するヒント

このセクションは、役立つ可能性があるヒントと回避策のコレクションです。

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>フィルター処理された結果のファセット ナビゲーション構造を非同期に保持する

Azure Cognitive Search でのファセット ナビゲーションの課題の 1 つとして、ファセットが現在の結果に対してのみ存在するということがあります。 実際には、手順を遡って検索コンテンツで代替パスを探索することで、ユーザーが逆方向にナビゲートできるようにファセットの静的なセットを保持するのが一般的です。 

これは一般的なユース ケースですが、ファセット ナビゲーション構造が現時点で標準で提供している機能には含まれていません。 静的なファセットを必要とする開発者は、通常、結果に適用されるクエリと、ナビゲーションのためにファセットの静的リストの作成に使用されるクエリの 2 つのフィルター処理されたクエリを発行してこの制限に対処しています。

### <a name="clear-facets"></a>ファセットをクリアする

検索結果ページを設計するときは、ファセットをクリアするためのメカニズムを追加することを忘れないでください。 チェック ボックスを追加する場合は、フィルターをクリアする方法を簡単に確認できます。 その他のレイアウトでは、階層リンク パターンや別の創造的な方法が必要になる場合があります。 ホテルの C# サンプルでは、空の検索を送信してページをリセットできます。 これに対して、NYCJobs サンプル アプリケーションでは、選択されたファセットの後に、そのファセットをクリアするためのクリック可能な `[X]` が表示されます。これは、ユーザーにとってより強力なビジュアル キューです。

### <a name="trim-facet-results-with-more-filters"></a>追加のフィルターを使用したファセット結果のトリミング

ファセットの結果は、ファセット語句に一致する検索結果で見つかったドキュメントです。 *cloud computing* の検索結果を示す次の例では、254 個の項目は Content type も *Internal specification* で一致しています。 項目は必ずしも相互に排他的ではありません。 1 つの項目が両方のフィルターの条件を満たしている場合、その項目はそれぞれにカウントされます。 この重複は、ドキュメントのタグ付けを実装するために使用されることの多い `Collection(Edm.String)` フィールドのファセット処理で発生する可能性があります。

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

一般に、ファセットの結果がなかなか小さくならない場合は、フィルターを増やして、検索をさらに絞り込むためのオプションをユーザーに提供することをお勧めします。

### <a name="a-facet-only-search-experience"></a>ファセットのみの検索エクスペリエンス

アプリケーションでファセット ナビゲーションのみを使用する (つまり、検索ボックスがない) 場合は、そのフィールドを `searchable=false`、`filterable=true`、`facetable=true` としてマークして、よりコンパクトなインデックスを生成できます。 そのインデックスに逆インデックスは含まれず、テキスト分析やトークン化も存在しなくなります。 フィルターは、文字レベルでの完全一致で作成されます。

### <a name="validate-inputs-at-query-time"></a>クエリ時に入力を検証する

信頼されていないユーザー入力に基づいて動的にファセットのリストを作成する場合は、ファセット フィールドの名前が有効であることを検証します。 または、.NET の `Uri.EscapeDataString()` または他のプラットフォームの同等機能を使用して URL を作成する場合は、名前をエスケープします。

## <a name="demos-and-samples"></a>デモとサンプル

一部のサンプルにはファセット ナビゲーションが含まれています。 このセクションにはサンプルへのリンクが含まれ、それぞれでどのクライアント ライブラリや言語が使用されているかも記載されています。

### <a name="create-your-first-app-in-c-razor"></a>C# での最初のアプリを作成する (Razor)

このチュートリアルと C# でのサンプル シリーズには、[ファセット ナビゲーションに焦点を絞ったレッスン](tutorial-csharp-facets.md)が含まれています。 このソリューションは ASP.NET MVC アプリであり、プレゼンテーション層では Razor クライアント ライブラリを使用します。

### <a name="add-search-to-web-apps-react"></a>Web アプリに検索を追加する (React)

チュートリアルと [C#](tutorial-csharp-overview.md)、[Python](tutorial-python-overview.md)、[JavaScript](tutorial-javascript-overview.md) でのサンプルには、ファセット ナビゲーションのほか、フィルター、候補、オートコンプリートが含まれています。 これらのサンプルでは、プレゼンテーション層に React を使用します。

### <a name="nycjobs-sample-code-and-demo-ajax"></a>NYCJobs サンプル コードとデモ (Ajax)

NYCJobs サンプルは、プレゼンテーション層で Ajax を使用する ASP.NET MVC アプリケーションです。 これは、[ライブ デモ アプリ](https://aka.ms/azjobsdemo)として、また [GitHub 上の Azure-Samples リポジトリ](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)のソース コードとして入手できます。
