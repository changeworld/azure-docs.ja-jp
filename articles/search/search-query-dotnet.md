<properties
	pageTitle=".NET を使用して Azure Search のクエリを作成する | Microsoft Azure | ホスト型クラウド検索サービス"
	description="Azure Search の検索クエリを作成し、検索パラメーターを使用して検索結果のフィルター処理、並べ替え、ファセットを行います (.NET ライブラリまたは SDK を使用)。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

#.NET を使用して Azure Search のクエリを作成する
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

この記事は、[Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) を使用してクエリを作成する方法について説明します。以下の内容は、「[.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)」から抜粋したものです。エンド ツー エンドの手順については、元の記事を参照してください。

クエリを作成するうえでの前提条件には、(通常は `SearchServiceClient` を介して) 検索サービスへの接続が既に確立されていることが挙げられます。

次のコード スニペットは、検索文字列の入力を SearchDocuments メソッドに渡すメソッドを作成します。

	private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
	{
		// Execute search based on search text and optional filter
		var sp = new SearchParameters();
	
		if (!String.IsNullOrEmpty(filter))
		{
			sp.Filter = filter;
		}
	
		DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
		foreach (SearchResult<Hotel> result in response)
		{
			Console.WriteLine(result.Document);
		}
	}
	
まず、このメソッドは新しい SearchParameters オブジェクトを作成します。このオブジェクトは、並べ替え、フィルター処理、ページング、ファセットなどの追加オプションをクエリに対して指定するために使用されます。この例では、Filter プロパティのみを設定しています。

次の手順では、検索クエリを実際に実行します。クエリの実行には Documents.Search メソッドを使用します。この例では、使用する検索テキストを文字列として、また前に作成した検索パラメーターを渡します。また、Documents.Search の型パラメーターとして Hotel を指定します。これにより SDK が検索結果中のドキュメントを Hotel 型のオブジェクトに逆シリアル化します。

最後に、このメソッドは検索結果のすべての一致を反復処理し、各ドキュメントをコンソールに出力します。

このメソッドの呼び出し方法を詳しく見ていきます。

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

最初の呼び出しでは、クエリ語句 "fancy" または "wifi" を含むすべてのドキュメントを検索します。2 番目の呼び出しでは、検索テキストが "*" に設定されています。これは、「すべてを検索する」ことを意味します。検索クエリ式の構文の詳細については、「[Azure Search の簡単なクエリ構文](https://msdn.microsoft.com/library/azure/dn798920.aspx)」を参照してください。

2 番目の呼び出しでは、OData の $filter 式、category eq 'Luxury' を使用しています。この式は、カテゴリ フィールドが文字列 "Luxury" に完全に一致するドキュメントのみ検索によって返されるように制限します。OData の構文の詳細については、[Azure Search の OData 式の構文](https://msdn.microsoft.com/library/azure/dn798921.aspx)に関するページを参照してください。

<!---HONumber=Nov15_HO3-->