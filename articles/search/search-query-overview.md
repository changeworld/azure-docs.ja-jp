---
title: Azure Search におけるクエリの基本 | Microsoft Docs
description: Azure Search で検索結果のフィルター処理、選択、並べ替えを行うためのパラメーターを使って検索クエリを作成するための基本的な事柄について説明します。
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366448"
---
# <a name="query-fundamentals-in-azure-search"></a>Azure Search におけるクエリの基本

Azure Search におけるクエリの定義とは、要求の細かな仕様です。サービス URL エンドポイント、ターゲット インデックス、要求を認証するための API キー、API バージョン、クエリ文字列などの要素で構成されます。 

クエリ文字列は、[Search Documents API](https://docs.microsoft.com/rest/api/searchservice/search-documents) に定義されているパラメーターを構成要素としています。 最も重要なのは **search=** パラメーターです。このパラメーターは未定義 (`search=*`) の状態にすることもできますが、通常は、次の例のように検索語句と演算子で構成されています。

```
"search": "seattle townhouse +\"lake\""
```

その他のパラメーターは、クエリの処理を指示したり応答を加工したりする目的で使用されます。 検索範囲を特定のフィールドに限定したり、検索モードを設定して適合率と再現率のバイアスを調整したり、結果を追跡できるようにカウントを追加したりすることが、パラメーターの使用例として挙げられます。 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

クエリの定義はきわめて重要な要素ですが、許可される操作をフィールド単位で指定するという点で、インデックス スキーマも同じぐらい重要です。 許可される操作は、インデックスの開発時に、フィールドに対する属性によって決まります。 たとえば特定のフィールドをフルテキスト検索の対象として検索結果に含めるためには、そのフィールドを "*検索可能*" かつ "*取得可能*" としてマークする必要があります。

クエリ時に実行の対象となるのは常に、要求に含まれている API キーを使用して認証された 1 つのインデックスです。 複数のインデックスを結合したり、カスタム データ構造や一時的なデータ構造をクエリの対象にしたりすることはできません。  

クエリの結果は、REST API では JSON ドキュメントとしてストリーム配信されますが、.NET の API を使用する場合は、ビルトインのシリアル化が適用されます。 検索結果は、クエリでパラメーターを設定し、結果に使用するフィールドを明示的に選択することで加工することができます。

つまり、検索の対象とするフィールド、結果セットに含めるフィールド、並べ替えやフィルター処理の有無など、クエリ要求の実質的な内容によってその範囲と操作を指定します。 明示的に指定しなかった場合は、フルテキスト検索操作としてすべての検索可能フィールドを対象にクエリが実行され、スコア付けされていない結果セットが任意の順序で返されます。

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>クエリの種類: 検索とフィルター

Azure Search では、非常に強力なクエリを作成できる多くのオプションが用意されています。 主に使用するクエリの種類は、`search` と `filter` の 2 種類です。 

+ `search` クエリは、インデックスのすべての "*検索可能*" フィールドで 1 つ以上の語句をスキャンし、Google や Bing などの検索エンジンに期待するのと同様に機能します。 この概要の各例では、`search` パラメーターを使用しています。

+ `filter` クエリは、インデックスのすべての "*フィルター処理可能*" フィールドでブール式を評価します。 `search` とは異なり、`filter` クエリはフィールドの内容を厳密に照合します。たとえば、文字列フィールドでは大文字と小文字が区別されます。

検索とフィルターは、一緒に使用することも、別々に使用することもできます。 クエリ文字列がないスタンドアロンのフィルターは、関係があるドキュメントをフィルター式で完全に修飾できる場合に役立ちます。 クエリ文字列がない場合、字句または言語の分析、スコア付け、優先度付けはありません。 検索文字列が空である点に注目してください。

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

一緒に使用した場合、フィルターが最初にインデックス全体に適用され、次にフィルター処理の結果に対して検索が実行されます。 フィルターはクエリのパフォーマンス向上に役立つ手法です。フィルターを使うと、検索クエリで処理が必要なドキュメントの数が減ります。

フィルター式の構文は、 [OData フィルター言語](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)のサブセットです。 検索クエリでは、以下で説明する[単純な構文](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)または [Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)を使用できます。


## <a name="choose-a-syntax-simple-or-full"></a>単純構文と完全構文の選択

Azure Search は、Apache Lucene を基盤としており、一般的なクエリと特殊なクエリの処理用に 2 つのクエリ パーサーが用意されています。 一般的な検索要求の記述には、既定の[単純クエリ構文](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)が使用されます。 この構文では、AND、OR、NOT、フレーズ、サフィックス、優先順位の演算子など、一般的な各種検索演算子がサポートされています。

[Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax)は、要求に **queryType=full** を追加することで有効になります。[Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) の一部として開発された、広く採用されている表現性の高いクエリ言語が公開されています。 このクエリ構文を使用すると、次のような特殊なクエリを実行できます。

+ [フィールド スコープ クエリ](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [あいまい検索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [近接検索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [用語ブースト](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [正規表現検索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [ワイルドカード検索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

どちらの構文も、ブール演算子はほぼ同じですが、完全な Lucene 構文には特別な書式が用意されています。

+ [単純構文のブール演算子](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [完全な Lucene 構文のブール演算子](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>必須の要素と省略可能な要素

Azure Search に検索要求を送信する際には、アプリケーションの検索ボックスに入力される実際の語句のほかにも、さまざまなパラメーターを指定できます。 これらのクエリ パラメーターを使用すると、[フルテキスト検索の機能](search-lucene-query-architecture.md)をよりきめ細かく制御できます。

クエリ要求の必須要素には、次のコンポーネントが含まれます。

+ サービス エンドポイントとインデックス ドキュメント コレクション。ここでは `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs` という URL で表されます。
+ API バージョン (REST のみ)。**api-version=** として表されます。
+ クエリまたは管理者の API キー。**api-key=** として表されます。
+ クエリ文字列。**search=** として表され、空の検索を実行する場合は、未指定にすることができます。 フィルター式だけを **$filter=** として送信することもできます。
+ **queryType=**。simple または full を指定します。既定の単純構文を使用する場合は、省略してもかまいません。

その他の検索パラメーターはすべて省略可能です。

## <a name="apis-and-tools-for-testing"></a>テスト用の API とツール

次の表は、API とツールを使ってクエリを送信する手法の一覧です。

| 手法 | 説明 |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Azure Search インデックスに対してクエリを実行する目的に使用できるクライアント。  <br/>[詳細情報。](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Search Documents (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | インデックスに対する GET メソッドまたは POST メソッド。追加の入力には、クエリ パラメーターを使用します。  |
| [Fiddler や Postman などの HTTP テスト ツール](search-fiddler.md) | Azure Search にクエリを送信するために要求ヘッダーと要求本文を設定する方法について説明します。  |
| [Azure portal の Search エクスプローラー](search-explorer.md) | 検索バーのほか、インデックスと API バージョンの選択に関するオプションが用意されています。 結果は JSON ドキュメントとして返されます。 <br/>[詳細情報。](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>検索結果の管理

クエリにパラメーターを使うことで、次のような方法で結果セットの構造を指定できます。

+ 結果に含めるドキュメント数を制限またはグループ化する (既定では 50 件)
+ 結果に含めるフィールドを選択する
+ 並べ替え順を設定する
+ 検索語句強調表示を追加して、検索結果の本文中で一致した語句を目立たせる

### <a name="tips-for-unexpected-results"></a>予期しない結果が生じた場合のヒント

結果の実質 (構造ではなく実質的な中身) が、予期しない内容になっている場合も少なくありません。 クエリの結果が想定と異なる場合は、次のような変更をクエリに加えて、結果が改善されるかどうかを試してみてください。

+ `searchMode=any` (既定) を `searchMode=all` に変更し、いずれかの条件への一致ではなく、すべての条件への一致を強制します。 これは特に、ブール演算子がクエリに含まれているときに当てはまります。

+ テキスト解析または字句解析が必要であるものの、クエリの種類が言語処理の妨げになっている場合は、クエリ手法を変更します。 フルテキスト検索では、スペル ミスや単語の単数形と複数形、不規則動詞、不規則名詞までもが、テキスト解析または字句解析によって自動的に修正されます。 ファジー検索やワイルドカード検索など、一部のクエリでは、クエリ解析パイプラインの過程でテキスト解析が行われません。 シナリオによっては、従来より正規表現が回避策として使用されています。 

### <a name="paging-results"></a>ページングの結果
Azure Search では、検索結果のページングを簡単に実装できます。 `top` および `skip` パラメーターを使用すると、管理しやすい並べ替えられたサブセットとして検索結果一式を取得できるようにする検索要求をスムーズに発行できます。また、このような検索結果により、検索 UI の利便性を簡単に高めることができます。 こうした結果の少数のサブセットを取得する際には、検索結果一式に含まれるドキュメントの数を取得することもできます。

検索結果のページングの詳細については、「 [Azure Search でのページ検索結果の表示方法](search-pagination-page-layout.md)」をご覧ください。

### <a name="ordering-results"></a>結果の並べ替え
検索クエリに対する結果を受け取る際、Azure Search から指定のフィールドの値に基づいて並べ替えられた結果が返されるようにすることができます。 既定では、Azure Search では、 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)から派生する各ドキュメントの検索スコアのランクに基づいて検索結果を並べ替えます。

検索スコア以外の値に基づいて並べ替えられた結果が Azure Search から返されるようにするには、 `orderby` 検索パラメーターを使います。 フィールド名を含める `orderby` パラメーターの値と、地理空間値の [`geo.distance()` 関数](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)の呼び出しを指定できます。 結果を昇順で要求する場合は各式の後に `asc` を指定し、降順で要求する場合は `desc` を指定します。 既定のランクは昇順です。


### <a name="hit-highlighting"></a>検索結果の強調表示
Azure Search では、検索クエリに一致する検索結果の特定の部分を正確に強調表示できます。これは、`highlight`、`highlightPreTag`、`highlightPostTag` の各パラメーターを使用して簡単に行えます。 一致するテキストを強調表示する*検索可能*フィールドを指定できるほか、Azure Search から返される一致テキストの先頭と末尾に追加する文字列タグを正確に指定することもできます。

## <a name="see-also"></a>関連項目

+ [Azure Search のフルテキスト検索のしくみ (クエリ解析アーキテクチャ)](search-lucene-query-architecture.md)
+ [Search エクスプローラー](search-explorer.md)
+ [.NET におけるクエリの実行方法](search-query-dotnet.md)
+ [REST におけるクエリの実行方法](search-query-rest-api.md)
