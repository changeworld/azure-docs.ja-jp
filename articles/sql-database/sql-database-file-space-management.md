---
title: Azure SQL Database のファイル領域管理 | Microsoft Docs
description: このページでは、Azure SQL Database を使用してファイル領域を管理する方法について説明します。また、データベースを縮小する必要があるかどうかを判断する方法、データベースの縮小操作を実行する方法を示すコード サンプルを紹介します。
services: sql-database
author: oslake
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: moslake
ms.openlocfilehash: 5dce07996191af3df3a4bdf16b211c29d59a994f
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003860"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Azure SQL Database でファイル領域を管理する
この記事では、Azure SQL Database のさまざまな種類の記憶域スペースと、データベースとエラスティック プールに割り当てられたファイル領域を明示的に管理する必要がある場合に実行できる手順について説明します。

## <a name="overview"></a>概要

Azure SQL Database では、Azure Portal に表示される大部分の記憶域スペースのメトリックと次の API で、データベースとエラスティック プールの使用データ ページ数を測定します。
- PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric) などの Azure Resource Manager ベースのメトリック API
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

データベースの基礎となるデータ ファイルの割り当てが使用データ ページ数よりも大きくなるようなワークロード パターンがあります。  この状況は、使用領域が増えた後でデータが削除された場合に発生する可能性があります。  これは、データの削除時に割り当てられていたファイル領域が自動的に再利用されないためです。  このようなシナリオでは、データベースまたはプールに割り当てられた領域がサポートされている上限を超える可能性があるため、データを増やすことができなくなり、パフォーマンス層を変更できなくなります。この状況を軽減するには、データ ファイルを縮小する必要があります。

データベースのパフォーマンスに影響を与える可能性があるため、未使用の割り当て領域を再利用するために SQL DB サービスがデータ ファイルを自動的に縮小することはありません。  ただし、「[未使用の割り当て済み領域を再利用する](#reclaim-unused-allocated-space)」で説明されている手順に従って、データ ファイルを選択した時点でセルフサービスによって縮小できます。 

> [!NOTE]
> データ ファイルとは異なり、ログ ファイルの操作はデータベースのパフォーマンスに影響を与えないため、ログ ファイルは SQL Database サービスによって自動的に縮小されます。 

## <a name="understanding-types-of-storage-space-for-a-database"></a>データベースの記憶域スペースの種類

データベースのファイル領域を管理するには、以下に示す記憶域スペースの量について理解することが重要です。

|データベースの量|定義|説明|
|---|---|---|
|**使用済みのデータ領域**|データベース データを 8 KB ページに格納するために使用された領域の量。|一般的に、使用済みの領域は挿入 (削除) で増加 (減少) します。 操作に関連するデータの量とパターン、および断片化によっては、挿入または削除時に使用される領域が変わらない場合があります。 たとえば、各データ ページから 1 行を削除しても、使用される領域が減らない場合があります。|
|**割り当て済みのデータ領域**|データベース データの格納に使用できるフォーマット済みファイル領域の量。|割り当て済みの領域の量は自動的に増えますが、削除後に自動的に減ることはありません。 このような動作で領域を再フォーマットする必要がないため、以降の挿入はより高速になります。|
|**割り当て済みで未使用のデータ領域**|割り当て済みのデータ領域と使用済みのデータ領域の差。|この量は、データベースのデータ ファイルを縮小して再利用できる空き領域の上限を表します。|
|**データの最大サイズ**|データベース データの格納に使用できる領域の最大量。|データの最大サイズを超えて割り当て済みのデータ領域を拡大することはできません。|
||||

次の図は、データベースの異なる種類の記憶域スペース間の関係を示しています。

![記憶域スペースの種類と関係](./media/sql-database-file-space-management/storage-types.png) 

## <a name="query-a-database-for-storage-space-information"></a>記憶域スペースの情報についてデータベースのクエリを実行する

次のクエリを使用して、データベースの記憶域スペースの量を確認できます。  

### <a name="database-data-space-used"></a>使用済みのデータベース データ領域
使用されているデータベース データ領域の量を返すように次のクエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>割り当て済みのデータベース データ領域と未使用の割り当て済み領域
次のクエリを使用して、割り当て済みデータベース データ領域と未使用の割り当て済み領域を返します。  クエリ結果の単位は MB です。

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>データベース データの最大サイズ
データベース データの最大サイズを返すように次のクエリを変更します。  クエリ結果の単位はバイトです。

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>エラスティック プールの記憶域スペースの種類

エラスティック プールのファイル領域を管理するには、以下に示す記憶域スペースの量について理解することが重要です。

|エラスティック プールの量|定義|説明|
|---|---|---|
|**使用済みのデータ領域**|エラスティック プール内のすべてのデータベースで使用されるデータ領域の合計。||
|**割り当て済みのデータ領域**|エラスティック プール内のすべてのデータベースで割り当て済みのデータ領域の合計。||
|**割り当て済みで未使用のデータ領域**|エラスティック プール内のすべてのデータベースで割り当て済みのデータ領域と使用済みのデータ領域の差。|この量は、データベースのデータ ファイルを縮小して再利用できる、エラスティック プールに割り当てられた領域の上限を表します。|
|**データの最大サイズ**|エラスティック プールのすべてのデータベースに使用できるデータ領域の最大量。|エラスティック プールに割り当てられた領域は、エラスティック プールの最大サイズを超えないようにする必要があります。  最大サイズを超えた場合は、割り当て済みで未使用の領域を、データベースのデータ ファイルを縮小して再利用できます。|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>記憶域スペースの情報についてエラスティック プールのクエリを実行する

次のクエリを使用して、エラスティック プールの記憶域スペースの量を確認できます。  

### <a name="elastic-pool-data-space-used"></a>使用済みのエラスティック プールのデータ領域
使用済みのエラスティック プールのデータ領域の量を返すように次のクエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>割り当て済みのエラスティック プールのデータ領域と未使用の割り当て済み領域

エラスティック プール内の各データベースの割り当て済み領域と未使用の割り当て済み領域の一覧表を返すように、次の PowerShell スクリプトを変更します。 この表では、未使用の割り当て済み領域が最大サイズのデータベースから順に並んでいます。  クエリ結果の単位は MB です。  

エラスティック プールに割り当てられた領域の合計を確認するために、プール内の各データベースに割り当てられた領域を確認するためのクエリ結果も追加することができます。 割り当て済みエラスティック プール領域は、エラスティック プールの最大サイズを超えないようにする必要があります。  

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

### <a name="elastic-pool-data-max-size"></a>エラスティック プール データの最大サイズ

エラスティック プール データの最大サイズを返すように、次の T-SQL クエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>未使用の割り当て済み領域を再利用する

未使用の割り当て済み領域を再利用するためのデータベースを特定したら、各データベースのデータ ファイルを縮小するように次のコマンドを変更します。

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

このコマンドの詳細については、[SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) を参照してください。 

> [!IMPORTANT] 
> データベース データ ファイルの縮小後は、データベース インデックスを再構築することをお勧めします。縮小後はインデックスが断片化され、パフォーマンスの最適化の効果が失われる可能性があるためです。 このような場合は、インデックスを再作成することをお勧めします。 インデックスの断片化と再構築の詳細については、「[インデックスの再構成と再構築](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)」を参照してください。

## <a name="next-steps"></a>次の手順

- データベースの最大サイズについては、以下を参照してください。
  - [Azure SQL Database の単一データベースに対する仮想コアベースの購入モデルの制限](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [DTU ベースの購入モデルを使用した単一データベースに対するリソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Azure SQL Database のエラスティック プールに対する仮想コアベースの購入モデルの制限](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [DTU ベースの購入モデルを使用したエラスティック プールに対するリソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- `SHRINKDATABASE` コマンドの詳細については、[SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) を参照してください。 
- インデックスの断片化と再構築の詳細については、「[インデックスの再構成と再構築](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)」を参照してください。
