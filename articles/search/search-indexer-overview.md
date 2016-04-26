<properties
	pageTitle="Azure Search のインデクサー | Microsoft Azure | ホステッド クラウド検索サービス"
	description="Azure SQL Database、DocumentDB、または Azure Storage をクロールして検索可能なデータを抽出し、Azure Search インデックスを作成します。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="04/14/2016"
	ms.author="heidist"/>

# Azure Search のインデクサー
> [AZURE.SELECTOR]
- [概要](search-indexer-overview.md)
- [ポータル](search-import-data-portal.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Blob Storage (プレビュー)](search-howto-indexing-azure-blob-storage.md)
- [Table Storage (プレビュー)](search-howto-indexing-azure-tables.md)

Azure Search の**インデクサー**は、検索可能なデータとメタデータを外部データ ソースから抽出し、インデックスとデータ ソース間のフィールドのマッピングに基づいてインデックスを作成するクローラーです。この方法は、インデックスにデータをプッシュするコードを記述することなく、サービスがデータをプルするため、「プル モデル」と呼ばれることもあります。

データ取り込みの唯一の手段としてインデクサーを使用したり、インデクサーの使用を含む手法を組み合わせて使用したりして、インデックス内のフィールドの一部だけを読み込むことができます。

インデクサーは、オンデマンドで実行することも、または 15 分ごとに実行される定期的なデータ更新スケジュールで実行することもできます。より頻繁に更新するには、Azure Search と外部データ ソースの両方のデータを同時に更新するプッシュ モデルが必要です。

インデクサーは、接続文字列などの情報を保持している**データ ソース**からデータをプルします。現在、次のデータ ソースがサポートされています。

- [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (または Azure 仮想マシン内の SQL Server)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (現時点ではプレビュー段階です。PDF、Office ドキュメント、HTML、XML からテキストを抽出します。)
- [Azure Table Storage](search-howto-indexing-azure-tables.md) (現時点ではプレビュー段階)

データ ソースは、それを使用するインデクサーとは別に構成および管理されます。これは、1 つのデータ ソースを複数のインデクサーで使用して、一度に複数のインデックスを読み込めることを意味します。

[.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) と [サービスの REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx) は、インデクサーとデータ ソースの管理をサポートします。

または、**データのインポート** ウィザードを使用する場合は、ポータルでインデクサーを構成することもできます。サンプル データや DocumentDB インデクサーを使用して、ウィザードを使ってインデックスを作成し、読み込む方法のクイック チュートリアルについては、「[ポータルから Azure Search を使用する](search-get-started-portal)」を参照してください。

<!---HONumber=AcomDC_0420_2016-->