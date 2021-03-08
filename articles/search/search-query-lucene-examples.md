---
title: 完全な Lucene クエリ構文の使用
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search サービスでのあいまい検索、近接検索、用語ブースト、正規表現検索、ワイルドカード検索用の Lucene クエリ構文。
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: df26cfc3b220f40a7e73ff1c750d2b2ae37e7625
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401459"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>"完全な" Lucene 検索構文の使用 (Azure Cognitive Search での高度なクエリ)

Azure Cognitive Search のクエリを構築するときは、既定の[シンプルなクエリ パーサー](query-simple-syntax.md)をより強力な [Azure Cognitive Search の Lucene Query Parser](query-lucene-syntax.md) に置き換えることにより、特殊化された高度なクエリ定義を作成することができます。 

Lucene パーサーは、フィールド スコープ クエリ、あいまい検索、インフィックスおよびサフィックスのワイルドカード検索、近接検索、用語ブースト、正規表現検索など、より複雑なクエリ構文に対応しています。 機能が強力になるほど処理要件が増えるため、実行時間がやや長くなることを見込んでください。 この記事では、完全な構文に基づいてクエリ操作をデモンストレーションする例について詳しく説明します。

> [!Note]
> 語幹検索や見出し語認定を想定していると意外なことですが、完全な Lucene クエリ構文で有効になる特殊化されたクエリ構造の多くは[テキスト解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)されません。 語彙の分析は、完全な用語でのみ実行されます (用語クエリまたは語句クエリ)。 不完全な語句 (プレフィックス クエリ、ワイルドカードのクエリ、正規表現のクエリ、あいまいクエリ) でのクエリの種類は、解析ステージをバイパスして、クエリ ツリーに直接追加されます。 部分的なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。 
>

## <a name="nyc-jobs-examples"></a>NYC ジョブの例

次の例では、[City of New York OpenData イニシアティブ](https://nycopendata.socrata.com/)が提供するデータセットに基づく利用可能なジョブで構成される [NYC ジョブ検索インデックス](https://azjobsdemo.azurewebsites.net/)を活用します。 このデータが最新のものであるとか、完全であるとはお考えにならないでください。 インデックスは、Microsoft が提供するサンドボックス サービス上にあります。つまり、これらのクエリを試すのに Azure サブスクリプションまたは Azure Cognitive Search は必要ありません。

必要になるのは、GET または POST で HTTP 要求を発行するための Postman または同等のツールです。 これらのツールに慣れていない場合は、[クイックスタート: Azure Cognitive Search の REST API を探索する](search-get-started-rest.md)」をご覧ください。

## <a name="set-up-the-request"></a>要求を設定する

1. 要求ヘッダーには次の値が必要です。

   | Key | 値 |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (これは、NYC ジョブ インデックスをホストするサンドボックス検索サービスの、実際のクエリ API キーです) |

1. 動詞は **`GET`** に設定します。

1. URL は **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`** に設定します

   + インデックスのドキュメント コレクションには、検索可能なすべてのコンテンツが含まれます。 要求ヘッダーに指定されたクエリ API キーは、ドキュメント コレクションを対象とする読み取り操作に対してのみ機能します。

   + **`$count=true`** は検索条件に一致するドキュメントの数を返します。 空の検索文字列では、この数はインデックス内のすべてのドキュメントになります (NYC ジョブの場合は約 2558)。

   + **`search=*`** は null または空の検索に相当する未指定のクエリです。 これは特に便利なわけではありませんが、最も簡単な検索方法であり、インデックス内の取得可能なすべてのフィールドに値がすべて入った状態で表示されます。

   + **`queryType=full`** は完全な Lucene アナライザーを呼び出します。

1. 確認手順として、次の要求を GET に貼り付け、 **[送信]** をクリックします。 結果は冗長な JSON ドキュメントとして返されます。

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>完全な Lucene の解析を呼び出す方法

完全なクエリ構文を呼び出して既定の単純なクエリ構文をオーバーライドするには、 **`queryType=full`** を追加します。 この記事のすべての例で、 **`queryType=full`** 検索パラメーターが指定されます。そうすることで、Lucene Query Parser によって完全な構文が処理されるように指示されます。 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "full"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>例 1:フィールドの一覧を対象とするクエリ

この最初の例はパーサー固有ではありませんが、最初の基本的なクエリの概念である包含を紹介するために利用します。 この例では、クエリの実行とその応答の両方をいくつかの特定のフィールドに制限しています。 ツールが Postman または Search エクスプローラーの場合、読みやすい JSON 応答を構成する方法を理解することが重要です。 

このクエリは **`searchFields`** の *business_title* のみを対象としており、 **`select`** パラメーターから、応答の同じフィールドを指定しています。

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

このクエリの応答は、次のスクリーンショットのようになります。

  ![スコアを含む Postman のサンプル応答](media/search-query-lucene-examples/postman-sample-results.png)

応答の検索スコアに気付いたかもしれません。 検索がフルテキスト検索ではない、または指定された基準がないという理由でランクがない場合は、**1** の均一のスコアが発生します。 空の検索の場合、行は任意の順序で返されます。 実際の基準を含めると、検索スコアは意味のある値に変化します。

## <a name="example-2-fielded-search"></a>例 2:フィールド検索

完全な Lucene 構文では、個々の検索式の範囲を特定のフィールドに制限することができます。 この例は、junior ではなく、senior という表現を含む肩書きを検索します。 AND を使用して複数のフィールドを指定できます。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

このクエリの応答は、次のスクリーンショットのようになります (posting_type は表示されません)。

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Postman のサンプル応答の検索式" border="false":::

検索式は、単一の単語、単一の語句、またはかっこで囲まれた複雑な式が可能であり、必要に応じてブール演算子も使用できます。 例として、次のようなものがあります。

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

複数の文字列を 1 つのエンティティとして評価するのであれば、複数の文字列を引用符で囲ってください。この例では、`state` フィールドで 2 つの異なる場所を検索しています。 ツールによっては、引用符のエスケープ (`\`) が必要になる場合があります。 

**fieldName:searchExpression** に指定されたフィールドは検索可能フィールドである必要があります。 フィールド定義におけるインデックス属性の使用方法に関する詳細については、「[インデックスの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index)」を参照してください。

> [!NOTE]
> 上記の例では、クエリの各部分に明示的に指定されたフィールド名があるため、 **`searchFields`** パラメーターは省略されています。 ただし、クエリに (AND ステートメントを使用した) 複数の部分がある場合は、 **`searchFields`** を引き続き使用できます。 たとえば、クエリ `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` は、`business_title` フィールドの `senior NOT junior` のみと一致し、`posting_type` フィールドの "external" と一致します。 `fieldName:searchExpression` に指定されたフィールド名は常に **`searchFields`** に優先するため、この例では **`searchFields`** から `business_title` を省略できます。

## <a name="example-3-fuzzy-search"></a>例 3: あいまい検索

完全な Lucene 構文では、構造が似ている用語に一致するあいまい検索もサポートしています。 あいまい検索を実行するには、1 つの言葉の終わりにチルダ記号 `~` を付けます。任意で編集距離を指定するパラメーターとして 0 ～ 2 の値を指定します。 たとえば、`blue~` または `blue~1` は blue、blues、glue を返します。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

語句は直接サポートされていませんが、`search=business_title:asosiate~ AND comm~` のように、複数部分から成る語句の各用語に対してあいまい一致を指定できます。  次のスクリーンショットでは、応答に *community associate* との一致が含まれています。

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="あいまい検索の応答" border="false":::

> [!Note]
> あいまいクエリは[分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)されません。 不完全な語句 (プレフィックス クエリ、ワイルドカードのクエリ、正規表現のクエリ、あいまいクエリ) でのクエリの種類は、解析ステージをバイパスして、クエリ ツリーに直接追加されます。 部分的なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="example-4-proximity-search"></a>例 4: 近接検索

近接検索は、ある文書で互いに近くにある言葉を検索します。 言葉の終わりにチルダ記号 "~" を挿入し、近接境界となる語数を続けます。 たとえば、"hotel airport"~5 と指定すると、ある文書で hotel という言葉と airport という言葉が互いに 5 語以内にある箇所が検索されます。

このクエリでは、"senior" と "analyst" という用語が検索されます。各用語は 1 つの単語ごとに区切られ、語句を保持するために引用符はエスケープされます (`\"`)。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

このクエリの応答は、次のスクリーンショットのようになります 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="近接クエリ" border="false":::

"senior analyst" という用語間の距離を削除して (`~0`)、再度試してみてください。 前のクエリの 10 個に対し、このクエリでは 8 個のドキュメントが返される点に注意してください。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>例 5:用語ブースト

用語ブーストでは、指定用語を含む文書に含まない文書より高い順位が設定されます (ブーストされます)。 用語をブーストするには、キャレット記号 `^` とブースト係数 (数字) を、検索する用語の終わりに使用します。

この "ブースト前" のクエリでは、*computer analyst* という用語を含む仕事を検索します。*computer* と *analyst* の両方を含む結果はありませんが、*computer* の仕事が結果の上位に表示されることに注目してください。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

この "ブースト後" のクエリでは、検索を繰り返します。今度は、両方の用語が存在しない場合、*analyst* という用語に *computer* という用語より高い優先順位を与えます。 人が判読できるバージョンのクエリは `search=business_title:computer analyst^2` です。 Postman で実行可能なクエリの場合、`^2` は `%5E2` としてエンコードされます。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

このクエリの応答は、次のスクリーンショットのようになります。

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="用語ブースト後" border="false":::

用語ブーストはスコアリング プロファイルとは違います。スコアリング プロファイルは、特定の用語ではなく、特定のフィールドをブーストします。 次の例はその違いを示しています。

musicstoreindex の例の **genre** など、特定のフィールドの一致がブーストされるスコアリング プロファイルについて考えてみましょう。 用語ブーストでは、特定の用語に他の用語より高い順位を与えます。 たとえば、"rock^2 electronic" と指定した場合、 **genre** フィールドに検索語句を含む文書に、インデックスの他の検索可能フィールドより高い順位が与えられます。 さらに、用語のブースト値 (2) により、"rock" という検索用語を含む文書に、"electronic" というもう 1 つの用語よりも高い順位が与えられます。

係数レベルを設定するときにブースト係数を大きくするほど、その用語の関連性が他の検索用語に比べて大きくなります。 既定のブースト係数は 1 です。 ブースト係数は整数にする必要がありますが、1 に満たない (0.2 など) 数字にすることができます。

## <a name="example-6-regex"></a>例 6:Regex

正規表現検索では、スラッシュ "/" の間のコンテンツに基づいて一致が検索されます。[RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html) クラスに詳細があります。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

このクエリの応答は、次のスクリーンショットのようになります。

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="正規表現クエリ" border="false":::

> [!Note]
> 正規表現クエリは[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)されません。 部分的なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="example-7-wildcard-search"></a>例 7:ワイルドカード検索

一般的に認められている構文を利用できます。複数の場合は (\*) を、単数の場合は (?) をワイルドカード文字として検索できます。 Lucene Query Parser では、これらの文字を語句ではなく 1 つの用語に利用することにご注意ください。

このクエリでは、"prog" という接頭辞を含む仕事を検索します。プログラミングやプログラマーなどの用語が肩書きに含まれる仕事が検索されます。 検索の最初の文字に `*` や `?` の記号は使用できません。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

このクエリの応答は、次のスクリーンショットのようになります。

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="ワイルドカード クエリ" border="false":::

> [!Note]
> ワイルドカード クエリは[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)されません。 部分的なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

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