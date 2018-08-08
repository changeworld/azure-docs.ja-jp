---
title: Azure Search の Lucene クエリ例 | Microsoft Docs
description: Azure Search サービスでのあいまい検索、近接検索、用語ブースト、正規表現検索、およびワイルドカード検索の Lucene クエリ構文。
author: LiamCa
manager: pablocas
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: liamca
ms.openlocfilehash: d90a7b2d12a147b8020abbd51ef055f0e70471fb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365430"
---
# <a name="lucene-syntax-query-examples-for-building-advanced-queries-in-azure-search"></a>Azure Search で高度なクエリを作成するための Lucene 構文のクエリの例
Azure Search のクエリを構築するときは、既定の[単純なクエリ パーサー](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)を代替の [Azure Search の Lucene Query Parser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) に置き換えることにより、特殊化された高度なクエリ定義を作成することができます。 

Lucene Query Parser は、フィールド スコープ クエリ、あいまい検索、ワイルドカード検索、近接検索、用語ブースト、正規表現検索など、複雑なクエリ構文に対応しています。 追加の機能には、それに伴う追加の処理要件があります。 この記事では、完全な構文を使用するときに、利用できるクエリ操作をデモンストレーションする例について詳しく説明します。

> [!Note]
> 語幹検索や見出し語認定を想定していると意外なことですが、完全な Lucene クエリ構文で有効になる特殊化されたクエリ構造の多くは[テキスト解析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)されません。 語彙の分析は、完全な用語でのみ実行されます (用語クエリまたは語句クエリ)。 不完全な語句 (プレフィックス クエリ、ワイルドカードのクエリ、正規表現のクエリ、あいまいクエリ) でのクエリの種類は、解析ステージをバイパスして、クエリ ツリーに直接追加されます。 不完全なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。 
>

## <a name="formulate-requests-in-postman"></a>Postman で要求を作成する

次の例では、[City of New York OpenData](https://nycopendata.socrata.com/) イニシアティブが提供するデータセットに基づいて利用可能なジョブで構成される NYC ジョブ検索インデックスを活用します。 このデータが最新のものであるとか、完全であるとはお考えにならないでください。 インデックスは、Microsoft が提供するサンドボックス サービス上にあります。つまり、これらのクエリを試すのに Azure サブスクリプションまたは Azure Search は必要ありません。

必要になるのは、GET で HTTP 要求を発行するための Postman または同等のツールです。 詳細については、[REST クライアントを使用したテスト](search-fiddler.md)に関するページを参照してください。

### <a name="set-the-request-header"></a>要求ヘッダーを設定する

1. 要求ヘッダーで、**Content-Type** を `application/json` に設定します。

2. **api-key** を追加して文字列 `252044BE3886FE4A8E3BAA4F595114BB` に設定します。 これは、NYC ジョブ インデックスをホストするサンドボックス検索サービスのクエリ キーです。

要求ヘッダーを指定した後は、**search=** 文字列のみを入れ替えることで、この記事のすべてのクエリに対して要求ヘッダーを再利用できます。 

  ![Postman の要求ヘッダー](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>要求 URL を設定する

要求は、Azure Search エンドポイントと検索文字列を含む URL と GET コマンドを組み合わせたものです。

  ![Postman の要求ヘッダー](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL は、次の要素から構成されます。

+ **`https://azs-playground.search.windows.net/`** は、Azure Search 開発チームによって管理されているサンドボックス検索サービスです。 
+ **`indexes/nycjobs/`** は、そのサービスのインデックス コレクション内の NYC ジョブ インデックスです。 要求にはサービス名とインデックスの両方が必要です。
+ **`docs`** は、検索可能なすべてのコンテンツを含むドキュメント コレクションです。 要求ヘッダーに指定されたクエリ api-key は、ドキュメント コレクションを対象とする読み取り操作に対してのみ機能します。
+ **`api-version=2017-11-11`** は、すべての要求に必須のパラメーターである api-version を設定します。
+ **`search=*`** はクエリ文字列です。最初のクエリでは null で、最初の 50 件の結果が返されます (既定値)。

## <a name="send-your-first-query"></a>初めてクエリを送信する

確認手順として、次の要求を GET に貼り付け、**[送信]** をクリックします。 結果は冗長な JSON ドキュメントとして返されます。 下の最初の例のこの URL をコピーして貼り付けることができます。

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

クエリ文字列 **`search=*`** は、null または空の検索に相当する未指定の検索です。 これは特に有用ではありませんが、実行できる最も簡単な検索です。

オプションで、URL に **`$count=true`** を追加して、検索基準に一致するドキュメントの数を返すことができます。 空の検索文字列では、これはインデックス内のすべてのドキュメントです (NYC ジョブの場合は 2802)。

## <a name="how-to-invoke-full-lucene-parsing"></a>完全な Lucene の解析を呼び出す方法

完全なクエリ構文を呼び出して既定の単純なクエリ構文をオーバーライドするには、**queryType=full** を追加します。 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&search=*
```

この記事のすべての例で、**queryType=full** 検索パラメーターを指定します。そうすることで、Lucene Query Parser によって完全な構文が処理されるように指示します。 

## <a name="example-1-field-scoped-query"></a>例 1: フィールド スコープ クエリ

最初のクエリは完全な Lucene 構文のデモンストレーションではありません (このクエリは単純な構文と完全な構文の両方で機能します) が、この例を使用して、合理的に読みやすい JSON 応答を生成するベースライン クエリの概念を紹介します。 簡潔にするため、このクエリでは *business_title* フィールドのみを対象として、肩書きのみが返されるよう指定しています。 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=*
```

**searchFields** パラメーターは、検索範囲をビジネス タイトル フィールドだけに制限します。 **select** パラメーターは、結果セットに含まれるフィールドを決定します。

このクエリの応答は、次のスクリーンショットのようになります。

  ![Postman の応答のサンプル](media/search-query-lucene-examples/postman-sample-results.png)

お気付きかもしれませんが、検索スコアが指定されていなくても、すべてのドキュメントの検索スコアも返されます。 これは、検索スコアが結果のランク順を示す値を含むメタデータであるためです。 検索がフルテキスト検索でなかった、または適用する基準がないという理由でランクがない場合は、1 の均一のスコアが発生します。 null 検索の場合、基準はなく、行は任意の順序で返されます。 検索条件に含まれる定義が増えるのに応じて、検索スコアが意味のある値に変化します。

## <a name="example-2-in-field-filtering"></a>例 2: フィールド内フィルタリング

完全な Lucene 構文では、フィールド内の式をサポートしています。 このクエリは、junior ではなく、senior という表現を含む肩書きを検索します。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:senior+NOT+junior
```

**fieldname:searchterm** 構造を指定することで、フィールド クエリ操作を定義できます。フィールドは 1 つの単語であり、検索用語も 1 つの単語または語句です。ブール演算子を利用することもあります。 たとえば、次のようになります。

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

複数の文字列を 1 つのエンティティとして評価するのであれば、複数の文字列を引用符で囲ってください。この例では、場所フィールドで 2 つの異なる都市を検索しています。 また、NOT や AND のように、演算子は大文字表記になります。

**fieldname:searchterm** に指定されたフィールドは検索可能フィールドである必要があります。 フィールド定義におけるインデックス属性の利用方法に関する詳細については、「 [Create Index (Azure Search Service REST API) (インデックスの作成 (Azure Search サービス REST API))](https://docs.microsoft.com/rest/api/searchservice/create-index) 」を参照してください。

## <a name="example-3-fuzzy-search"></a>例 3: あいまい検索の例

完全な Lucene 構文では、構造が似ている用語に一致するあいまい検索もサポートしています。 あいまい検索を実行するには、1 つの言葉の終わりにチルダ記号 `~` を付けます。任意で編集距離を指定するパラメーターとして 0 ～ 2 の値を指定します。 たとえば、`blue~` または `blue~1` は blue、blues、glue を返します。

このクエリは、"associate" という用語を含む仕事を検索します (意図的にスペルを間違えています)。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:asosiate~
```

[Lucene ドキュメント](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)によると、あいまい検索は [Damerau-Levenshtein Distance](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance) を基盤としています。

> [!Note]
> あいまいクエリは[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)されません。 不完全な語句 (プレフィックス クエリ、ワイルドカードのクエリ、正規表現のクエリ、あいまいクエリ) でのクエリの種類は、解析ステージをバイパスして、クエリ ツリーに直接追加されます。 不完全なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="example-4-proximity-search"></a>例 4: 近接検索
近接検索は、ある文書で互いに近くにある言葉を検索します。 言葉の終わりにチルダ記号 "~" を挿入し、近接境界となる語数を続けます。 たとえば、"hotel airport"~5 と指定すると、ある文書で hotel という言葉と airport という言葉が互いに 5 語以内にある箇所が検索されます。

このクエリでは、"senior analyst" という用語を含む仕事を検索します。間に 1 語だけ含まれます。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:%22senior%20analyst%22~1
```

"senior analyst" の間の言葉を削除してもう一度試します。 前のクエリの 10 個に対し、このクエリでは 8 個のドキュメントが返される点に注意してください。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>例 5: 用語ブースト
用語ブーストでは、指定用語を含む文書に含まない文書より高い順位が設定されます (ブーストされます)。 用語をブーストするには、キャレット記号 "^" とブースト係数 (数字) を検索語句の終わりに付けます。 

この "ブースト前" のクエリでは、*computer analyst* という用語を含む仕事を検索します。*computer* と *analyst* の両方を含む結果はありませんが、*computer* の仕事が結果の上位に表示されることに注目してください。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:computer%20analyst
```

この "ブースト後" のクエリでは、検索を繰り返します。今度は、両方の用語が存在しない場合、*analyst* という用語に *computer* という用語より高い優先順位を与えます。 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:computer%20analyst%5e2
```
上記のクエリをより読みやすい形式にすると、`search=business_title:computer analyst^2` になります。 実行可能なクエリの場合、`^2` は `%5E2` としてエンコードされ、見づらくなります。

用語ブーストはスコアリング プロファイルとは違います。スコアリング プロファイルは、特定の用語ではなく、特定のフィールドをブーストします。 次の例はその違いを示しています。

musicstoreindex の例の **genre** など、特定のフィールドの一致がブーストされるスコアリング プロファイルについて考えてみましょう。 用語ブーストでは、特定の用語に他の用語より高い順位を与えます。 たとえば、"rock^2 electronic" と指定した場合、 **genre** フィールドに検索語句を含む文書に、インデックスの他の検索可能フィールドより高い順位が与えられます。 さらに、用語のブースト値 (2) により、"rock" という検索用語を含む文書に、"electronic" というもう 1 つの用語よりも高い順位が与えられます。

係数レベルを設定するときにブースト係数を大きくするほど、その用語の関連性が他の検索用語に比べて大きくなります。 既定のブースト係数は 1 です。 ブースト係数は整数にする必要がありますが、1 に満たない (0.2 など) 数字にすることができます。


## <a name="example-6-regex"></a>例 6: 正規表現

正規表現検索では、スラッシュ "/" の間のコンテンツに基づいて一致が検索されます。[RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html) クラスに詳細があります。

Senior または Junior という用語を含む仕事を検索するクエリは、`search=business_title:/(Sen|Jun)ior/`` となります。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:/(Sen|Jun)ior/
```
> [!Note]
> 正規表現クエリは[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)されません。 不完全なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="example-7-wildcard-search"></a>例 7: ワイルドカード検索
一般的に認められている構文を利用できます。複数の場合は (\*) を、単数の場合は (?) をワイルドカード文字として検索できます。 Lucene Query Parser では、これらの文字を語句ではなく 1 つの用語に利用することにご注意ください。

このクエリでは、"prog" という接頭辞を含む仕事を検索します。プログラミングやプログラマーなどの用語が肩書きに含まれる仕事が検索されます。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:prog*
```
検索の最初の文字として * または ? を使用することはできません。

> [!Note]
> ワイルドカード クエリは[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)されません。 不完全なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="next-steps"></a>次の手順
自分のコードに Lucene Query Parser を指定してみてください。 次のリンクでは、.NET と REST API の両方の検索クエリを設定する方法について説明しています。 これらのリンクでは、既定の単純な構文を使用しています。**queryType** を指定するには、この記事で学習したことを応用する必要があります。

* [.NET SDK を使用した Azure Search インデックスの照会](search-query-dotnet.md)
* [REST API を使用した Azure Search インデックスの照会](search-query-rest-api.md)

追加の構文リファレンス、クエリ アーキテクチャ、およびサンプルについては、次のリンク先を参照してください。

+ [単純構文クエリの例](search-query-simple-examples.md)
+ [Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Full Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)