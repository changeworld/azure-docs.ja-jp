<properties
    pageTitle="Azure Search でのデータのアップロード | Microsoft Azure | ホステッド クラウド検索サービス"
    description="Azure Search のインデックスにデータをアップロードする方法について説明します。"
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Azure Search へのデータのアップロード
> [AZURE.SELECTOR]
- [概要](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST ()](search-import-data-rest-api.md)


## Azure Search のデータ アップロード モデル
Azure Search インデックスにデータを設定する方法は 2 とおりあります。1 つ目は、Azure Search [REST API](search-import-data-rest-api.md) または [.NET SDK](search-import-data-dotnet.md) を使用して、手動でデータをインデックスにプッシュする方法です。2 つ目は、Azure Search に[サポートされているデータ ソースを指定](search-indexer-overview.md)し、Azure Search によってデータが検索サービスに自動的にプルされるようにする方法です。

このガイドでは、データ アップロードのプッシュ モデル ([REST API](search-import-data-rest-api.md) と [.NET SDK](search-import-data-dotnet.md) でのみサポート) の使用手順のみを取り上げますが、以下でプル モデルの詳細も確認できます。

### インデックスにデータをプッシュする

このアプローチでは、データを Azure Search にプログラムで送信し、検索に利用できるようにします。待機時間の要件が非常に厳しいアプリケーションの場合 (たとえば、検索操作を動的な在庫データベースと同期する必要がある場合) は、プッシュ モデルしか利用できません。

インデックスにデータをプッシュするには、[REST API](https://msdn.microsoft.com/library/azure/dn798930.aspx) または [.NET SDK](search-import-data-dotnet.md) を使用することができます。現在では、ポータルを使用してデータをプッシュするためのツール サポートはありません。

このアプローチでは、ドキュメントを個別に、またはバッチでアップロードできるため、プル モデルよりも柔軟です ("バッチごとに最大 1,000 個" と "16 MB" のうち、どちらか先に達した制限が適用されます)。プッシュ モデルでは、データのある場所にかかわらず、ドキュメントを Azure Search にアップロードすることもできます。

### インデックスへのデータのプル

プル モデルでは、サポートされているデータ ソースをクロールし、データを Azure Search に自動的にアップロードします。インデクサーは、新しいドキュメントを認識するだけでなく、既存のドキュメントの変更と削除を追跡するため、インデックス内のデータをアクティブに管理する必要がありません。

Azure Search では、この機能は*インデクサー*によって実装され、現時点では [Blob Storage (プレビュー)](search-howto-indexing-azure-blob-storage.md)、[DocumentDB](http://aka.ms/documentdb-search-indexer)、[Azure SQL Database、Azure VM 上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) で利用可能です。

インデクサーの機能は、[REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx) に加えて [Azure ポータル](search-import-data-portal.md)でも公開されています。

<!---HONumber=AcomDC_0831_2016-->