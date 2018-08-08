---
title: Azure SQL Database のファイル領域管理 | Microsoft Docs
description: このページでは、Azure SQL Database を使用してファイル領域を管理する方法について説明します。また、データベースを縮小する必要があるかどうかを判断する方法、データベースの縮小操作を実行する方法を示すコード サンプルを紹介します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39416005"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Azure SQL Database でファイル領域を管理する

この記事では、Azure SQL Database のさまざまな種類の記憶域スペースと、データベースとエラスティック プールに割り当てられたファイル領域を管理する必要がある場合に実行できる手順について説明します。

## <a name="overview"></a>概要

Azure SQL Database では、Azure portal に表示される記憶域サイズのメトリックと次の API で、データベースとエラスティック プールの使用データ ページ数を測定します。
- PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric) などの Azure Resource Manager ベースのメトリック API
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

データベースの基礎となるデータ ファイル内の領域の割り当てが、データ ファイルで使用されているデータ ページ数よりも大きくなるようなワークロード パターンがあります。 このシナリオは、使用領域が増えた後にデータが削除された場合に発生する可能性があります。 データが削除されると、データの削除時に割り当てられていたファイル領域は自動的に再利用されません。 このようなシナリオでは、データベースまたはプールの割り当て済み領域が、設定されている (またはサポートされている) データベースの上限を超える可能性があり、その結果、実際のデータベース領域の使用量が領域の上限未満であっても、データを増やすことができなくなり、パフォーマンス層を変更できなくなります。 この状況を軽減するには、必要に応じてデータベースを縮小し、データベースの割り当て済みで未使用の領域を減らします。

データベースのパフォーマンスに影響を与える可能性があるため、未使用の割り当て領域を再利用するために SQL Database サービスがデータベース ファイルを自動的に縮小することはありません。 ただし、[未使用の割り当て領域の再利用](#reclaim-unused-allocated-space)に関する記事で説明されている手順に従って、選択した時点でデータベース内のファイルを圧縮できます。 

> [!NOTE]
> データ ファイルとは異なり、ログ ファイルの操作はデータベースのパフォーマンスに影響を与えないため、ログ ファイルは SQL Database サービスによって自動的に縮小されます。

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>データベースの記憶域スペースの種類

ファイル領域を管理するには、単一のデータベースとエラスティック プールの両方のデータベース記憶域に関連する以下の用語を理解する必要があります。

|記憶域スペースの用語|定義|説明|
|---|---|---|
|**使用済みのデータ領域**|データベース データを 8 KB ページに格納するために使用された領域の量。|一般的に、この領域は挿入 (削除) で増加 (減少) します。 操作に関連するデータの量とパターン、および断片化によっては、挿入または削除時に使用される領域が変わらない場合があります。 たとえば、各データ ページから 1 行を削除しても、使用される領域が減らない場合があります。|
|**割り当て済みの領域**|データベース データの格納に使用できるフォーマット済みファイル領域の量|割り当て済みの領域は自動的に増えますが、削除後に自動的に減ることはありません。 このような動作で領域を再フォーマットする必要がないため、以降の挿入はより高速になります。|
|**割り当て済みで未使用の領域**|データベースに割り当てられ、未使用のデータ ファイル領域のサイズ。|この量は、割り当て済みの領域と使用済みの領域の差であり、データベース ファイルを縮小するで再利用できるようになる領域の上限を表します。|
|**最大サイズ**|データベースが使用できるデータ領域の最大量。|データの最大サイズを超えて割り当て済みのデータ領域を拡大することはできません。|
||||

次の図は、記憶域スペースの種類間の関係を示しています。

![記憶域スペースの種類と関係](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>記憶域スペースの情報についてデータベースのクエリを実行する

個々のデータベースに、再利用できる割り当て済みで未使用のデータ領域があるかどうかを判断するには、次のクエリを使用します。

### <a name="database-data-space-used"></a>使用済みのデータベース データ領域
MB で使用されているデータベース データ領域の量を返すように次のクエリを変更します。

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>割り当て済みのデータベース データと未使用の割り当て済み領域
割り当て済みデータベース データのサイズと未使用の割り当て済み領域を返すように次のクエリを変更します。

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>データベースの最大サイズ
データベースの最大サイズ (バイト単位) を返すように次のクエリを変更します。

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>記憶域スペースの情報についてエラスティック プールのクエリを実行する

エラスティック プールと個々のデータベースに、再利用できる割り当て済みで未使用のデータ領域があるかどうかを判断するには、次のクエリを使用します。

### <a name="elastic-pool-data-space-used"></a>使用済みのエラスティック プールのデータ領域
MB で使用されているエラスティック プール データ領域の量を返すように次のクエリを変更します。

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>割り当て済みのエラスティック プール データと未使用の割り当て済み領域

エラスティック プール内の各データベースの割り当て済み領域合計と未使用の割り当て済み領域の一覧表を返すように、次の PowerShell スクリプトを変更します。 この表は、未使用の割り当て済み領域が最大サイズのデータベースから、未使用の割り当て済み領域の最小サイズのデータベースの順に並んでいます。  

割り当て済みエラスティック プール領域を決定するために、プール内の各データベースの割り当て済み領域を決定するためのクエリ結果も追加することができます。 割り当て済みエラスティック プール領域は、エラスティック プールの最大サイズを超えないようにする必要があります。  

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
# Requires SQL Server PowerShell module – see here to install.  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

以下のスクリーンショットは、スクリプトの出力例です。

![エラスティック プールの割り当て済み領域と未使用の割り当て領域の例](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-max-size"></a>エラスティック プールの最大サイズ

次の T-SQL クエリを使用して、エラスティック データベースの最大サイズ (MB 単位) を返します。

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>未使用の割り当て済み領域を再利用する

未使用の割り当て済み領域があり、再利用すると判断したら、次のコマンドを使用して、割り当て済みデータベース領域を縮小します。 

> [!IMPORTANT]
> エラスティック プール内のデータベースの場合、ファイル領域を最も迅速に再利用できるように、未使用の領域が最も多く割り当てられたデータベースを最初に縮小する必要があります。  

指定したデータベース内のすべてのデータ ファイルを縮小するには、次のコマンドを使用します。

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

このコマンドの詳細については、[SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) を参照してください。

> [!IMPORTANT] 
> データベース データ ファイルの縮小後は、データベース インデックスを再構築することをお勧めします。縮小後はインデックスが断片化され、パフォーマンスの最適化の効果が失われる可能性があるためです。 このような場合は、インデックスを再作成することをお勧めします。 インデックスの断片化と再構築の詳細については、「[インデックスの再構成と再構築](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)」を参照してください。

## <a name="next-steps"></a>次の手順

- 最大データベースサイズについては、以下を参照してください。
  - [Azure SQL Database の単一データベースに対する仮想コアベースの購入モデルの制限](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [DTU ベースの購入モデルを使用した単一データベースに対するリソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Azure SQL Database のエラスティック プールに対する仮想コアベースの購入モデルの制限](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [DTU ベースの購入モデルを使用したエラスティック プールに対するリソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- `SHRINKDATABASE` コマンドの詳細については、[SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) を参照してください。 
- インデックスの断片化と再構築の詳細については、「[インデックスの再構成と再構築](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)」を参照してください。