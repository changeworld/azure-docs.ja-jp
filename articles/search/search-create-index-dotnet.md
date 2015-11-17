<properties
	pageTitle=".NET を使用した Azure Search インデックスの作成 | Microsoft Azure | ホスト型クラウド検索サービス"
	description="Azure Search .NET SDK またはライブラリを使用して、コードでインデックスを作成します。"
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# .NET を使用した Azure Search インデックスの作成
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

この記事では、[Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) を使用してインデックスを作成する方法について説明します。以下の内容は、「[.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)」から抜粋したものです。エンド ツー エンドの手順については、元の記事を参照してください。

インデックスを作成するうえでの前提条件には、(通常は `SearchServiceClient` を介して) 検索サービスへの接続が既に確立されていることが挙げられます。スムーズに再デプロイできるように、同じ名前のインデックスが既に存在する場合はそれを削除することをお勧めします。

"hotels" という名前のインデックスがあると仮定すると、そのためのメソッドを次のように作成できます。

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

このメソッドは、指定された `SearchServiceClient` を使用してインデックスが存在するかどうかを確認し、存在する場合は、それを削除します。

新しい "hotels" インデックスを作成するには、次のようなメソッドを作成します。

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

このメソッドは、新しいインデックスのスキーマを定義する `Field` オブジェクトのリストで新しい `Index` オブジェクトを作成します。各フィールドには、名前、データ型、および検索動作を定義するいくつかの属性があります。フィールドに加えて、スコアリング プロファイル、サジェスター、または CORS オプションを Index に追加することもできます (簡潔さを優先し、サンプルではこれらは省略されています)。Index オブジェクトとその構成要素の詳細については、[MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx) の SDK リファレンスおよび [Azure Search REST API リファレンス](https://msdn.microsoft.com/library/azure/dn798935.aspx)を参照してください。

<!---HONumber=Nov15_HO3-->