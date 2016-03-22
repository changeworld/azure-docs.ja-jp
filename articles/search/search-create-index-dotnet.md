<properties
    pageTitle=".NET SDK を使用した Azure Search インデックスの作成 | Microsoft Azure | ホスト型クラウド検索サービス"
    description="Azure Search .NET SDK を使用して、コードでインデックスを作成します。"
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="brjohnst"/>

# .NET SDK を使用した Azure Search インデックスの作成
> [AZURE.SELECTOR]
- [概要](search-what-is-an-index.md)
- [ポータル](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST ()](search-create-index-rest-api.md)


この記事では、[Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) を使用して Azure Search の[インデックス](https://msdn.microsoft.com/library/azure/dn798941.aspx)を作成するプロセスについて説明します。

このガイドに従ってインデックスを作成する前に、既に [Azure Search サービスを作成済み](search-create-service-portal.md)である必要があります。

この記事に記載されたすべてのサンプル コードは、C# で記述されていることにご注意ください。[GitHub](http://aka.ms/search-dotnet-howto) に完全なソース コードがあります。

## I.Azure Search サービスの管理者 API キーの識別
Azure Search サービスのプロビジョニングが完了すると、あと少しで、.NET SDK を使用して、サービス エンドポイントに対して要求を発行できます。まず、プロビジョニングした検索サービス用に生成された管理者 API キーの 1 つを取得する必要があります。.NET SDK は、サービスに対する要求ごとに、この API キーを送信します。有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

1. サービスの API キーを探すには、[Azure ポータル](https://portal.azure.com/)にログインする必要があります。
2. Azure Search サービスのブレードに移動します。
3. "キー" アイコンをクリックします。

サービスで*管理者キー*と*クエリ キー*を使用できるようになります。

  - プライマリおよびセカンダリ*管理者キー*は、サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与するものです。キーは 2 つあるため、プライマリ キーを再生成することにした場合もセカンダリ キーを使い続けることができます (その逆も可能です)。
  - *クエリ キー*はインデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。

インデックスを作成する目的では、プライマリ管理者キーとセカンダリ管理者キーのどちらかを使用できます。

<a name="CreateSearchServiceClient"></a>
## II.SearchServiceClient クラスのインスタンスの作成
Azure Search .NET SDK を使い始めるには、`SearchServiceClient` クラスのインスタンスを作成する必要があります。このクラスにはいくつかのコンストラクターがあります。目的のコンストラクターは、パラメーターとして検索サービスの名前と `SearchCredentials` オブジェクトを使用します。`SearchCredentials` は API キーをラップします。

次のコードは、アプリケーションの構成ファイル (`app.config` または `web.config`) に格納されている検索サービスの名前と API キーの値を使用して新しい `SearchServiceClient` を作成します。

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` には `Indexes` プロパティが含まれています。このプロパティは、Azure Search インデックスの作成、一覧表示、更新、または削除に必要なすべてのメソッドを提供します。

> [AZURE.NOTE] `SearchServiceClient` クラスは検索サービスへの接続を管理します。開いている接続の数が多くなりすぎないよう、可能であれば、アプリケーションで `SearchServiceClient` の単一のインスタンスを共有する必要があります。SearchServiceClient のメソッドはスレッド セーフなので、このような共有が可能です。

<a name="DefineIndex"></a>
## III.`Index` クラスを使用した Azure Search インデックスの定義
`Indexes.Create` メソッドに対する 1 回の呼び出しでインデックスが作成されます。このメソッドでは、Azure Search インデックスを定義する `Index` オブジェクトがパラメーターとして使用されます。次のように、`Index` オブジェクトを作成して初期化する必要があります。

1. `Index` オブジェクトの `Name` プロパティをインデックスの名前に設定します。
2. `Index` オブジェクトの `Fields` プロパティを `Field` オブジェクトの配列に設定します。それぞれの `Field` オブジェクトがインデックスのフィールドの動作を定義します。コンストラクターに対して、データ型 (または文字列フィールドのアナライザー) と共に、フィールドの名前を指定することができます。`IsSearchable` や `IsFilterable` など、他のプロパティを設定することもできます。

各 `Field` には[適切なプロパティ](https://msdn.microsoft.com/library/azure/dn798941.aspx)を割り当てる必要があるため、インデックスを設計する際は、検索のユーザー エクスペリエンスとビジネス ニーズに留意することが重要です。これらのプロパティでは、どのフィールドにどの検索機能 (フィルター、ファセット、全文検索の並べ替えなど) が適用されるかを制御します。明示的に設定しないプロパティについては、明確に有効にしない限り、`Field` クラスの既定では、対応する検索機能が無効になります。

この例では、インデックスに "hotels" という名前を付け、次のようにフィールドを定義しました。

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

各 `Field` のプロパティの値は、アプリケーションでどのように使用されるかに応じて、慎重に選択されています。たとえば、ホテルについて検索するユーザーは `description` フィールドでのキーワードの一致に関心がある可能性が高いです。そのため、`IsSearchable` を `true` に設定してそのフィールドの全文検索を有効にします。

`IsKey` を `true` に設定して、インデックスの `DataType.String` 型のフィールドを 1 つだけ、key フィールドとして指定する必要があることにご注意ください (上記の例の `hotelId` を参照)。

上記のインデックス定義では、フランス語のテキストを格納することを目的としているため、`description_fr` フィールドにカスタム言語アナライザーを使用しています。言語アナライザーの詳細については、[MSDN の「言語サポート」](https://msdn.microsoft.com/library/azure/dn879793.aspx)と、関連する[ブログ記事](https://azure.microsoft.com/blog/language-support-in-azure-search/)をご覧ください。

> [AZURE.NOTE]  コンストラクターで `AnalyzerName.FrLucene` を渡すことによって、`Field` は自動的に `DataType.String` 型になり、`IsSearchable` が `true` に設定されることにご注意ください。

## IV.インデックスの作成
`Index` オブジェクトが初期化されたので、`SearchServiceClient` オブジェクトで `Indexes.Create` を呼び出すだけでインデックスを作成できます。

```csharp
serviceClient.Indexes.Create(definition);
```

要求が成功すると、メソッドは通常どおりに復帰します。無効なパラメーターなど、要求に問題がある場合、メソッドは `CloudException` をスローします。

インデックスが不要になり、それを削除する場合は、`SearchServiceClient` で `Indexes.Delete` を呼び出すだけです。たとえば、"hotels" を削除する方法を次に示します。

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] この記事のコード例では、わかりやすくするため、Azure Search .NET SDK の同期メソッドを使用します。実際のアプリケーションでは、高い拡張性と応答性を維持するため、非同期メソッドを使用することをお勧めします。たとえば、上記の例では、`Create` と `Delete` の代わりに、`CreateAsync` と `DeleteAsync` を使用できます。

## 次へ
Azure Search インデックスを作成すると、データの検索を開始できるように[インデックスにコンテンツをアップロードする](search-what-is-data-import.md)準備が完了します。

<!---HONumber=AcomDC_0316_2016-->