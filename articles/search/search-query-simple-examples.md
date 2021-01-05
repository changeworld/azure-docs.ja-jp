---
title: 単純な Lucene クエリ構文を使用する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデックスに対するフルテキスト検索、フィルター検索、地理検索、ファセット検索の簡単な構文を基にしてクエリを実行することにより、例を使用して学習します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401748"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search での "単純な" 検索構文を使用する

Azure Cognitive Search では、[単純なクエリ構文](query-simple-syntax.md)で既定のクエリ パーサーが呼び出されて、フルテキスト検索が実行されます。 このパーサーは高速で、フルテキスト検索、フィルター検索、ファセット検索、プレフィックス検索などの一般的なシナリオに対応します。 この記事では、例を使用して、[ドキュメントの検索 (REST API)](/rest/api/searchservice/search-documents) 要求での単純な構文の使用方法を示します。

> [!NOTE]
> 代替のクエリ構文は[完全な Lucene](query-lucene-syntax.md) であり、あいまい検索、ワイルドカード検索などのより複雑なクエリ構造をサポートしています。 詳細と例については、[完全な Lucene 構文の使用](search-query-lucene-examples.md)に関するページをご覧ください。

## <a name="nyc-jobs-examples"></a>NYC ジョブの例

次の例では、[City of New York OpenData イニシアティブ](https://nycopendata.socrata.com/)が提供するデータセットに基づく利用可能なジョブで構成される [NYC ジョブ検索インデックス](https://azjobsdemo.azurewebsites.net/)を活用します。 このデータが最新のものであるとか、完全であるとはお考えにならないでください。 インデックスは、Microsoft が提供するサンドボックス サービス上にあります。つまり、これらのクエリを試すのに Azure サブスクリプションまたは Azure Cognitive Search は必要ありません。

必要になるのは、GET または POST で HTTP 要求を発行するための Postman または同等のツールです。 これらのツールに慣れていない場合は、[クイック スタート: Azure Cognitive Search の REST API を探索する](search-get-started-rest.md)」をご覧ください。

## <a name="set-up-the-request"></a>要求を設定する

1. 要求ヘッダーには次の値が必要です。

   | Key | 値 |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (これは、NYC ジョブ インデックスをホストするサンドボックス検索サービスの、実際のクエリ API キーです) |

1. 動詞は **`GET`** に設定します。

1. URL は **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** に設定します。 

   + インデックスのドキュメント コレクションには、検索可能なすべてのコンテンツが含まれます。 要求ヘッダーに指定されたクエリ API キーは、ドキュメント コレクションを対象とする読み取り操作に対してのみ機能します。

   + **`$count=true`** は検索条件に一致するドキュメントの数を返します。 空の検索文字列では、この数はインデックス内のすべてのドキュメントになります (NYC ジョブの場合は約 2558)。

   + **`search=*`** は null または空の検索に相当する未指定のクエリです。 これは特に便利なわけではありませんが、最も簡単な検索方法であり、インデックス内の取得可能なすべてのフィールドに値がすべて入った状態で表示されます。

1. 確認手順として、次の要求を GET に貼り付け、 **[送信]** をクリックします。 結果は冗長な JSON ドキュメントとして返されます。

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>単純なクエリの解析を呼び出す方法

対話型クエリでは何も指定する必要はありません。既定値は simple です。 コードで、以前に **`queryType=full`** を呼び出した場合、 **`queryType=simple`** を使用して既定値にリセットできます。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>例 1:特定のフィールドに対するフルテキスト検索

この最初の例はパーサー固有ではありませんが、最初の基本的なクエリの概念である包含を紹介するために利用します。 この例では、クエリの実行とその応答の両方をいくつかの特定のフィールドに制限しています。 ツールが Postman または Search エクスプローラーの場合、読みやすい JSON 応答を構成する方法を理解することが重要です。 

このクエリは **`searchFields`** の *business_title* のみを対象としており、 **`select`** パラメーターから、応答の同じフィールドを指定しています。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

このクエリの応答は、次のスクリーンショットのようになります。

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Postman の応答のサンプル" border="false":::

応答の検索スコアに気付いたかもしれません。 検索がフルテキスト検索ではない、または指定された基準がないという理由でランクがない場合は、**1** の均一のスコアが発生します。 空の検索の場合、行は任意の順序で返されます。 実際の基準を含めると、検索スコアは意味のある値に変化します。

## <a name="example-2-look-up-by-id"></a>例 2:ID による参照

クエリで検索結果を返す場合は、その後の論理的な手順として、ドキュメントから多くのフィールドを含む詳細ページを指定します。 この例では、[参照操作](/rest/api/searchservice/lookup-document)を使用してドキュメント ID を渡して 1 つのドキュメントを返す方法を示します。

すべてのドキュメントは一意の識別子を持ちます。 参照クエリの構文を試す場合、使用する ID を見つけるために、最初にドキュメント ID の一覧を返します。 NYC ジョブの場合、識別子は `id` フィールドに格納されます。

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

次に、前の応答で最初に出現した `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" に基づいてコレクションからドキュメントを取得します。 次のクエリでは、ドキュメント全体に対するすべての取得可能なフィールドが返されます。

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>例 3: フィルター クエリ

[フィルター構文](./search-query-odata-filter.md)は、単独で使用することも、 **`search`** と一緒に使用することもできる OData 式です。 search パラメーターがないスタンドアロン フィルターは、関心があるドキュメントをフィルター式で完全に修飾できる場合に役立ちます。 クエリ文字列がない場合、字句または言語の分析なし、スコア付けなし (すべて 1 にスコア付け)、および優先度付けなしになります。 検索文字列が空である点に注目してください。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

一緒に使用した場合、フィルターが最初にインデックス全体に適用され、次にフィルター処理の結果に対して検索が実行されます。 フィルターはクエリのパフォーマンス向上に役立つ手法です。フィルターを使うと、検索クエリで処理が必要なドキュメントの数が減ります。

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="フィルター クエリの応答" border="false":::

フィルターと検索を組み合わせる別の強力な方法は、フィルター式の中で **`search.ismatch*()`** を使用することです。この場合、フィルター内で検索クエリを使用できます。 次のフィルター式では、*plan* でワイルドカードを使用して、plan、planner、planning などの用語が含まれる business_title を選択します。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

この関数の詳細については、[「フィルターの例」の search.ismatch](./search-query-odata-full-text-search-functions.md#examples) を参照してください。

## <a name="example-4-range-filters"></a>例 4: 範囲フィルター

範囲フィルターは、任意のデータ型の **`$filter`** 式を通してサポートされます。 次の例では、数値フィールドと文字列フィールドを検索します。 

範囲フィルターではデータ型が重要であり、数値フィールドにある数値データと文字列フィールドにある文字列データで最もうまく機能します。 Azure Cognitive Search では数値文字列を比較できないため、文字列フィールドの数値データは範囲には適していません。

次のクエリは数値範囲です。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
このクエリの応答は、次のスクリーンショットのようになります。

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="数値範囲用の範囲フィルター" border="false":::

このクエリでは、範囲が文字列フィールド (business_title) の上にあります。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

このクエリの応答は、次のスクリーンショットのようになります。

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="テキスト範囲用の範囲フィルター" border="false":::

> [!NOTE]
> 検索アプリケーションでは値の範囲に対するファセットが一般に必要になります。 詳細と例については、「[Azure Cognitive Search でファセット フィルターを作成する方法](search-filters-facets.md)」をご覧ください。

## <a name="example-5-geo-search"></a>例 5:地理空間検索

サンプル インデックスには、緯度と経度の座標を持つ geo_location フィールドが含まれています。 この例では、[geo.distance 関数](search-query-odata-geo-spatial-functions.md#examples)を使用して、開始点を中心に、指定された距離 (キロメートル単位) に収まるドキュメントをフィルター処理します。 クエリの最後の値 (4) を調整して、クエリのサーフェス領域を拡大または縮小できます。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

結果をさらに読みやすくするために、検索結果は、役職および勤務地を含むようにトリミングされます。 開始座標は、インデックス内のランダムなドキュメントから取得されています (ここではスタテン島の勤務地)。

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="スタテン島の地図" border="false":::

## <a name="example-6-search-precision"></a>例 6:検索の精度

用語のクエリは、単一の用語を対象とし、おそらくその多くは個別に評価されます。 語句のクエリは引用符で囲まれ、逐語的な文字列として評価されます。 一致の精度は、演算子と searchMode によって制御されます。

例 1: `search=fire` は、140 件の結果で一致し、すべての一致には、ドキュメントのどこかに出現する単語 fire が含まれています。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

例 2: `search=fire department` は、2,002 件の結果を返します。 fire または department を含むドキュメントについて一致が返されます。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

例 3: `search="fire department"` は、77 件の結果を返します。 文字列を二重引用符で囲むと両方の用語から構成される語句検索が作成されます。一致は、結合された用語で構成されるインデックス内のトークン化された用語で検出されます。 これが、 `search=+fire +department` のような検索と同等でない理由です。 どちらの用語も必須ですが、これらは個別にスキャンされます。 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> 語句クエリは引用符で指定されるため、この例では、構文を保持するためにエスケープ文字 (`\`) を追加します。

## <a name="example-7-booleans-with-searchmode"></a>例 7:searchMode によるブール値

単純な構文では、文字形式のブール演算子 (`+, -, |`) がサポートされています。 searchMode パラメーターは、精度と再現率のトレードオフを示します。 **`searchMode=any`** は再現率を優先し (任意の条件で一致すると結果セットとしてドキュメントが適格になります)、 **`searchMode=all`** は精度を優先します (すべての条件が一致する必要があります)。 

既定値は **`searchMode=any`** です。これは、クエリに複数の演算子を含めて、絞り込んだ結果ではなくより広範な結果を取得した場合に、混乱を招く可能性があります。 これは特に NOT に当てはまります。NOT では、特定の用語を "含まない" すべてのドキュメントが結果に含まれます。

既定の searchMode (any) を使用した場合、2,800 件のドキュメントが返されます。これは、"fire department" という語句を含むドキュメントと、"Metrotech Center" という語句を含まないすべてのドキュメントの合計です。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

このクエリの応答は、次のスクリーンショットのようになります。

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="検索モード any" border="false":::

**`searchMode=all`** に変更すると、条件に累積的な効果が適用され、より小さな結果セット (21 個のドキュメント) が返されます。これは、"fire department" という語句全体を含むドキュメントから、Metrotech Center のアドレスのジョブを除いた結果です。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="検索モード all" border="false":::

## <a name="example-8-structuring-results"></a>例 8:結果の構造化

いくつかのパラメーターは、検索結果に含まれるフィールド、各バッチで返されるドキュメントの数、および並べ替え順を制御します。 この例では、前の例のいくつかを再利用し、 **`$select`** ステートメントと逐語検索基準を使用して結果を特定のフィールドに限定し、82 件の一致を返します。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

前の例に付加して、タイトルで並べ替えることができます。 この並べ替えは、インデックス内で civil_service_title が *sortable* であるために機能します。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

結果のページングは、 **`$top`** パラメーターを使用して実装できます。このケースでは、上位 5 つのドキュメントが返されます。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

次の 5 件を取得するには、最初のバッチをスキップします。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>次のステップ

コードでクエリを指定してみてください。 次のリンクは、Azure SDK を使用して検索クエリを設定する方法について説明しています。

+ [.NET SDK を使用したインデックスのクエリ実行](search-get-started-dotnet.md)
+ [Python SDK を使用したインデックスのクエリ実行](search-get-started-python.md)
+ [JavaScript SDK を使用したインデックスのクエリ実行](search-get-started-javascript.md)

追加の構文リファレンス、クエリ アーキテクチャ、およびサンプルについては、次のリンク先を参照してください。

+ [高度なクエリを作成するための Lucene 構文のクエリの例](search-query-lucene-examples.md)
+ [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [単純なクエリ構文](query-simple-syntax.md)
+ [Full Lucene クエリ構文](query-lucene-syntax.md)
+ [フィルターの構文](search-query-odata-filter.md)