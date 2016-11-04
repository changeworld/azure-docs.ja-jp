---
title: C# でのエラスティック データベース プールの監視と管理 | Microsoft Docs
description: C# データベース開発技術を使用して Azure SQL Database エラスティック データベース プールを管理します。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein

---
# <a name="monitor-and-manage-an-elastic-database-pool-with-c&#x23;"></a>C&#x23; でのエラスティック データベース プールの監視と管理
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

C&#x23; を使用して[エラスティック データベース プール](sql-database-elastic-pool.md)を管理する方法について説明します。 

> [!NOTE]
> SQL Database の新機能の多くは、[Azure Resource Manager デプロイメント モデル](../resource-group-overview.md)を使用している場合にのみサポートされます。そのため、常に最新の **Azure SQL Database Management Library for .NET ([ドキュメント](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))** を使用する必要があります。 以前の[クラシック デプロイメント モデル ベースのライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)は互換性のためだけにサポートされているため、より新しい Resource Manager ベースのライブラリを使用することをお勧めします。
> 
> 

この記事の手順を完了するには、次のものが必要です。

* エラスティック プール (管理するプール)。 プールを作成するには、「[C# を使用したエラスティック データベース プールの作成](sql-database-elastic-pool-create-csharp.md)」を参照してください。
* 見ることができます。 Visual Studio の無償版については、 [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs) に関するページを参照してください。

## <a name="move-a-database-into-an-elastic-pool"></a>エラスティック プールへのデータベースの移動
プールに、またはプールから、スタンドアロンのデータベースを移動できます。  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>エラスティック プール内のデータベースの一覧表示
プール内のすべてのデータベースを取得するには、 [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) メソッドを呼び出します。

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>プールのパフォーマンス設定を変更する
既存のプールのプロパティを取得します。 値を変更して、CreateOrUpdate メソッドを実行します。

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>エラスティック プール操作の待機時間
* 通常、データベースあたりの最小 eDTU またはデータベースあたりの最大 eDTU の変更は、5 分以内で完了します。
* プール サイズ (eDTU) の変更時間は、プール内のすべてのデータベースを結合したサイズに依存します。 変更の平均時間は、100 GB あたり 90 分以下です。 たとえば、プール内のすべてのデータベースの合計領域が 200 GB の場合、プールあたりの eDTU の変更にかかる想定待機時間は、3 時間以下になります。

## <a name="additional-resources"></a>その他のリソース
* [SQL Database クライアント アプリケーションの SQL エラー コード: データベース接続エラーとその他の問題](sql-database-develop-error-messages.md)。
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
* [Azure リソース管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* [C# による新しいエラスティック データベース プールの作成](sql-database-elastic-pool-create-csharp.md)
* [エラスティック データベース プールの使用に適した状況](sql-database-elastic-pool-guidance.md)
* エラスティック データベース ツールを使用してスケールアウト、データの移動、クエリ、トランザクションの作成を行う方法については、「 [Azure SQL Database によるスケール アウト](sql-database-elastic-scale-introduction.md)」を参照してください。

<!--HONumber=Oct16_HO2-->


