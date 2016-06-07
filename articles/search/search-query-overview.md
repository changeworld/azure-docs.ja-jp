<properties
    pageTitle="Azure Search インデックスの照会 | Microsoft Azure | ホステッド クラウド検索サービス"
    description="Azure Search の検索クエリを作成し、検索パラメーターを使用して検索結果のフィルター処理と並べ替えを行います。"
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"/>

# Azure Search インデックスの照会
> [AZURE.SELECTOR]
- [概要](search-query-overview.md)
- [ポータル](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST ()](search-query-rest-api.md)

Azure Search に検索要求を送信する際には、アプリケーションの検索ボックスに入力される実際の語句のほかにも、さまざまなパラメーターを指定できます。これらのクエリ パラメーターを使用すると、フルテキスト検索の機能をよりきめ細かく制御できます。

以下の一覧で、Azure Search におけるクエリ パラメーターの一般的な使用方法について簡単に説明します。クエリ パラメーターとその動作を網羅した説明については、[REST API](https://msdn.microsoft.com/library/azure/dn798927.aspx) および [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx) 向けの詳細ページをご覧ください。

## クエリの種類

Azure Search では、非常に強力なクエリを作成できる多くのオプションが用意されています。主に使用するクエリの種類は、`search` と `filter` の 2 種類です。`search` クエリは、インデックスのすべての_検索可能_フィールドで 1 つ以上の語句を検索し、Google や Bing などの検索エンジンに期待するのと同様に機能します。`filter` クエリは、インデックスのすべての_フィルター処理可能_フィールドでブール式を評価します。`search` クエリとは異なり、`filter` クエリはフィールドの内容を厳密に照合します。つまり、文字列フィールドでは大文字と小文字が区別されます。

検索とフィルターは、一緒に使用することも、別々に使用することもできます。一緒に使用した場合、フィルターが最初にインデックス全体に適用され、次にフィルター処理の結果に対して検索が実行されます。フィルターはクエリのパフォーマンス向上に役立つ手法です。フィルターを使うと、検索クエリで処理が必要なドキュメントの数が減ります。

フィルター式の構文は、[OData フィルター言語](https://msdn.microsoft.com/library/azure/dn798921.aspx)のサブセットです。検索クエリでは、以下で説明する[単純な構文](https://msdn.microsoft.com/library/azure/dn798920.aspx)または [Lucene クエリ構文](https://msdn.microsoft.com/library/azure/mt589323.aspx)を使用できます。

### 単純なクエリ構文
[単純なクエリ構文](https://msdn.microsoft.com/library/azure/dn798920.aspx)は、Azure Search で使用される既定のクエリ言語です。単純なクエリ構文では、AND、OR、NOT、フレーズ、サフィックス、優先順位の演算子など、一般的な各種検索演算子がサポートされています。

### Lucene クエリ構文
[Lucene クエリ構文](https://msdn.microsoft.com/library/azure/mt589323.aspx)では、[Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) の一部として開発された、広く採用されている表現性の高いクエリ言語を使用できます。

このクエリ構文を使うと、[フィールド スコープ クエリ](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields)、[ファジー検索](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy)、[近接検索](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity)、[ターム ブースト](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost)、[正規表現検索](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex)、[ワイルドカード検索](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard)、[構文の基本](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)、[ブール演算子を使用したクエリ](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean)などの機能を簡単に実現できます。



## 結果の並べ替え
検索クエリに対する結果を受け取る際、Azure Search から指定のフィールドの値に基づいて並べ替えられた結果が返されるようにすることができます。既定では、Azure Search では、[TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) から派生する各ドキュメントの検索スコアのランクに基づいて検索結果を並べ替えます。

検索スコア以外の値に基づいて並べ替えられた結果が Azure Search から返されるようにするには、`orderby` 検索パラメーターを使います。フィールド名を含める `orderby` パラメーターの値と、地理空間値の [`geo.distance()` 関数](https://msdn.microsoft.com/library/azure/dn798921.aspx)の呼び出しを指定できます。結果を昇順で要求する場合は各式の後に `asc` を指定し、降順で要求する場合は `desc` を指定します。既定のランクは昇順です。

## ページング
Azure Search では、検索結果のページングを簡単に実装できます。`top` および `skip` パラメーターを使用すると、管理しやすい並べ替えられたサブセットとして検索結果一式を取得できるようにする検索要求をスムーズに発行できます。また、このような検索結果により、検索 UI の利便性を簡単に高めることができます。こうした結果の少数のサブセットを取得する際には、検索結果一式に含まれるドキュメントの数を取得することもできます。

検索結果のページングの詳細については、「[Azure Search でのページ検索結果の表示方法](search-pagination-page-layout.md)」をご覧ください。


## 検索結果の強調表示
Azure Search では、検索クエリに一致する検索結果の特定の部分を正確に強調表示できます。これは、`highlight`、`highlightPreTag`、`highlightPostTag` の各パラメーターを使用して簡単に行えます。一致するテキストを強調表示する_検索可能_フィールドを指定できるほか、Azure Search から返される一致テキストの先頭と末尾に追加する文字列タグを正確に指定することもできます。

<!---HONumber=AcomDC_0601_2016-->