<properties
    pageTitle=".NET SDK を使用した Azure Search インデックスの照会 | Microsoft Azure | ホストされるクラウド検索サービス"
    description="Azure Search の検索クエリを作成し、検索パラメーターを使用して検索結果のフィルター処理と並べ替えを行います。"
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# .NET SDK を使用した Azure Search インデックスの照会
> [AZURE.SELECTOR]
- [概要](search-query-overview.md)
- [Search エクスプローラー](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST ()](search-query-rest-api.md)

この記事では、[Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) を使用してインデックスを照会する方法について説明します。このチュートリアルを開始する前に、既に [Azure Search インデックスを作成](search-create-index-dotnet.md)し、[インデックスにデータを読み込んでいます](search-import-data-dotnet.md)。

この記事に記載されたすべてのサンプル コードは、C# で記述されていることにご注意ください。[GitHub](http://aka.ms/search-dotnet-howto) に完全なソース コードがあります。

## I.Azure Search サービスのクエリ API キーの識別
Azure Search インデックスの作成は済んでいるので、.NET SDK を使用してクエリを発行する準備はほとんどできています。まず、プロビジョニングした検索サービス用に生成されたクエリ API キーの 1 つを取得する必要があります。.NET SDK は、サービスに対する要求ごとに、この API キーを送信します。有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

1. サービスの API キーを探すには、[Azure ポータル](https://portal.azure.com/)にログインする必要があります。
2. Azure Search サービスのブレードに移動します。
3. "キー" アイコンをクリックします。

サービスで*管理者キー*と*クエリ キー*を使用できるようになります。

  - プライマリおよびセカンダリ*管理者キー*は、サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与するものです。キーは 2 つあるため、プライマリ キーを再生成することにした場合もセカンダリ キーを使い続けることができます (その逆も可能です)。
  - *クエリ キー*はインデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。

インデックスを照会する目的では、いずれかのクエリ キーを使用できます。クエリには管理者キーを使うこともできますが、アプリケーション コードではクエリ キーを使うようにしてください。この方が、[最少権限の原則](https://en.wikipedia.org/wiki/Principle_of_least_privilege)に適っています。

## II.SearchIndexClient クラスのインスタンスの作成
Azure Search .NET SDK を使用してクエリを発行するには、`SearchIndexClient` クラスのインスタンスを作成する必要があります。このクラスにはいくつかのコンストラクターがあります。目的のコンストラクターは、検索サービスの名前、インデックスの名前、および `SearchCredentials` オブジェクトをパラメーターとして使用します。`SearchCredentials` は API キーをラップします。

次のコードは、アプリケーションの構成ファイル (`app.config` または `web.config`) に保存されている検索サービスの名前と API キーを表す値を使用して、"hotels" というインデックス (「[.NET SDK を使用した Azure Search インデックスの作成](search-create-index-dotnet.md)」で作成したもの) の `SearchIndexClient` を作成します。

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient` には `Documents` というプロパティがあります。このプロパティは、Azure Search インデックスの照会に必要なすべてのメソッドを提供します。

## III.インデックスの照会
.NET SDK を使用した検索は簡単です。`Documents.Search` メソッドを `SearchIndexClient` で呼び出します。このメソッドは、検索テキストのほか、クエリをさらに絞り込むために使用できる `SearchParameters` オブジェクトなどのいくつかのパラメーターを受け取ります。

#### クエリの種類

Azure Search では、非常に強力なクエリを作成できる多くのオプションが用意されています。主に使用するクエリの種類は、`search` と `filter` の 2 種類です。`search` クエリは、インデックスのすべての_検索可能_フィールドで 1 つ以上の語句を検索し、Google や Bing などの検索エンジンに期待されるように機能します。`filter` クエリは、インデックスのすべての_フィルター処理可能_フィールドでブール式を評価します。`search` クエリとは異なり、`filter` クエリはフィールドの内容を厳密に照合します。つまり、文字列フィールドでは大文字と小文字が区別されます。

検索とフィルターは、一緒に使用することも、別々に使用することもできます。一緒に使用した場合、フィルターが最初にインデックス全体に適用され、次にフィルター処理の結果に対して検索が実行されます。フィルターはクエリのパフォーマンス向上に役立つ手法です。フィルターを使うと、検索クエリで処理が必要なドキュメントの数が減ります。

検索とフィルターは両方とも `Documents.Search` メソッドを使用して実行できます。検索クエリは `searchText` パラメーターで渡すことができます。一方、フィルター式は `SearchParameters` クラスの `Filter` プロパティで渡すことができます。検索しないでフィルター処理を実行するには、`"*"` を `searchText` パラメーターに渡します。フィルター処理を行わないで検索するには、`Filter` プロパティを未設定のままにするか、`SearchParameters` インスタンスを 1 つも渡さないようにします。

フィルター式の構文は、[OData フィルター言語](https://msdn.microsoft.com/library/azure/dn798921.aspx)のサブセットです。各検索クエリで、[簡略構文](https://msdn.microsoft.com/library/azure/dn798920.aspx)または [Lucene クエリ構文](https://msdn.microsoft.com/library/azure/mt589323.aspx)を使用できます。

クエリのパラメーターの詳細については、[MSDN の .NET SDK リファレンス](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters.aspx)を参照してください。以下にも、クエリの例をいくつか紹介します。

#### クエリの例

次のサンプル コードは、「[.NET SDK を使用した Azure Search の作成](search-create-index-dotnet.md#DefineIndex)」で定義した "hotels" というインデックスを照会するための、異なるいくつかの方法を示しています。検索結果で返されるドキュメントは `Hotel` クラスのインスタンスであることにご注意ください。このクラスは、「[Data Import in Azure Search using the .NET SDK (.NET SDK を使用した Azure Search へのデータのインポート)](search-import-data-dotnet.md#HotelClass)」で定義したクラスです。サンプル コードでは、`WriteDocuments` メソッドを使用して検索結果をコンソールに出力しています。このメソッドについては次のセクションで説明します。

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters = 
    new SearchParameters() 
    { 
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## IV.検索結果の処理方法
`Documents.Search` メソッドは、クエリの結果を含む `DocumentSearchResult` オブジェクトを返します。前のセクションの例では、`WriteDocuments` というメソッドを使用して検索結果をコンソールに出力しています。

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

前のセクションに示したクエリの結果は、次のようになります。インデックス "hotels" には「[Data Import in Azure Search using the .NET SDK (.NET SDK を使用した Azure Search へのデータのインポート)](search-import-data-dotnet.md)」に示したサンプル データが読み込まれていると想定しています。

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

上記のサンプル コードでは、コンソールを使って検索結果を出力します。同様に、独自のアプリケーションに検索結果を表示する場合もあります。ASP.NET MVC ベースの Web アプリケーションで検索結果を表示する方法を示す例については、[GitHub でこちらのサンプル](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample)を参照してください。

<!---HONumber=AcomDC_0309_2016-->