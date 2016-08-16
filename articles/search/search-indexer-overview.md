<properties
	pageTitle="Azure Search のインデクサー | Microsoft Azure | ホステッド クラウド検索サービス"
	description="Azure SQL Database、DocumentDB、または Azure Storage をクロールして検索可能なデータを抽出し、Azure Search インデックスを作成します。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/08/2016"
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

## インデクサーの作成と管理の方法

Azure SQL や DocumentDB のように広く提供されているインデクサーでは、次の方法でインデクサーを作成したり管理したりすることができます。

- [ポータルのデータのインポート ウィザード ](search-get-started-portal.md)
- [サービス REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Azure Blob Storage、Azure Table Storage といったプレビュー版のインデクサーでは、コードとプレビュー版の API ([インデクサー用の Azure Search Preview REST API](search-api-indexers-2015-02-28-preview.md) など) が必要となります。ポータルのツールは通常、プレビュー版の機能では利用できません。

## 基本的な構成手順

インデクサーで実行できる機能は、データ ソースごとに異なります。そのためインデクサーやデータ ソースの構成には、インデクサーの種類ごとに異なる点があります。しかし基本的な成り立ちと要件は、すべてのインデクサーに共通です。以降、すべてのインデクサーに共通の手順について取り上げます。

### 手順 1. インデックスを作成する

インデクサーは、データの取り込みに関連したいくつかのタスクを自動化しますが、そこにはインデックスの作成は含まれていません。前提条件として、定義済みのインデックスが必要です。加えてそのフィールドは、外部データ ソース内のフィールドと一致している必要があります。インデックス構築の詳細については、「[Create an Index (Azure Search REST API) (インデックスの作成 (Azure Search REST API))](https://msdn.microsoft.com/library/azure/dn798941.aspx)」を参照してください。

### 手順 2. データ ソースを作成する

インデクサーは、接続文字列などの情報を保持している**データ ソース**からデータをプルします。現在、次のデータ ソースがサポートされています。

- [Azure SQL Database または Azure 仮想マシン上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage (プレビュー)](search-howto-indexing-azure-blob-storage.md)。PDF や Office ドキュメント、HTML、XML からテキストを抽出するために使用されます。
- [Azure Table Storage (プレビュー)](search-howto-indexing-azure-tables.md)

データ ソースは、それを使用するインデクサーとは別に構成および管理されます。これは、1 つのデータ ソースを複数のインデクサーで使用して、一度に複数のインデックスを読み込めることを意味します。

### 手順 3. インデクサーを作成してスケジュールを設定する

インデクサーの定義とは、インデックスやデータ ソース、スケジュールを指定する構文です。インデクサーは、データ ソースが同じサブスクリプションに属していれば、他のサービスのデータ ソースであっても参照できます。インデクサー構築の詳細については、「[Create Indexer (Azure Search REST API) (インデクサーの作成 (Azure Search REST API))](https://msdn.microsoft.com/library/azure/dn946899.aspx)」を参照してください。

## 次のステップ

基本的な概念の説明は以上です。次のステップとしてデータ ソースの種類ごとの要件とタスクを確認してください。

- [Azure SQL Database または Azure 仮想マシン上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage (プレビュー)](search-howto-indexing-azure-blob-storage.md)。PDF や Office ドキュメント、HTML、XML からテキストを抽出するために使用されます。
- [Azure Table Storage (プレビュー)](search-howto-indexing-azure-tables.md)
- [Azure Search BLOB インデクサー (プレビュー) を使用した CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
- [Azure Search BLOB インデクサー (プレビュー) を使用した JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)

<!---HONumber=AcomDC_0810_2016-->