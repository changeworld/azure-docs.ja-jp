---
title: Azure SQL Database のファイル領域管理
description: このページでは、Azure SQL Database の単一データベースおよびプールされたデータベースを使用してファイル領域を管理する方法について説明します。また、単一データベースまたはプールされたデータベースを縮小する必要があるかどうかを判断する方法、データベースの縮小操作を実行する方法を示すコード サンプルを紹介します。
services: sql-database
ms.service: sql-database
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, wiassaf
ms.date: 08/09/2021
ms.openlocfilehash: 27adb19b07dc67a91d1bdafb6aac54ad59eaa778
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178465"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Azure SQL Database でデータベースのファイル領域を管理する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、Azure SQL Database のデータベースのさまざまな種類の記憶域スペースと、割り当てられたファイル領域を明示的に管理する必要がある場合に実行できる手順について説明します。

> [!NOTE]
> この記事は、Azure SQL Managed Instance には適用されません。

## <a name="overview"></a>概要

Azure SQL Database では、データベースの基礎となるデータ ファイルの割り当てが使用データ ページ数よりも大きくなるようなワークロード パターンがあります。 この状況は、使用領域が増えた後にデータが削除された場合に発生する可能性があります。 これは、データの削除時に割り当てられていたファイル領域が自動的に再利用されないためです。

次のシナリオでは、ファイル領域の使用率の監視とデータ ファイルの圧縮が必要になる場合があります。

- データベースに割り当てられたファイル領域がプールのサイズ上限に達した場合に、エラスティック プール内のデータの増大を許可する。
- 単一データベースまたはエラスティック プールの最大サイズの縮小を許可する。
- 単一データベースまたはエラスティック プールを、よりサイズ上限の低い異なるサービス レベルまたはパフォーマンス レベルに変更することを許可する。

> [!NOTE]
> 圧縮操作は、通常のメンテナンス操作と見なすことはできません。 通常の定期的なビジネス操作のために増加するデータ ファイルとログ ファイルには、圧縮操作は必要ではありません。 

### <a name="monitoring-file-space-usage"></a>ファイル領域の使用状況の監視

次の API に表示されるほとんどのストレージ領域メトリックでは、使用されているデータ ページのサイズだけが測定されます。

- PowerShell [get-metrics](/powershell/module/az.monitor/get-azmetric) などの Azure Resource Manager ベースのメトリック API

ただし、次の API は、データベースとエラスティック プールに割り当てられている領域のサイズを測ることもできます。

- T-SQL: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

## <a name="understanding-types-of-storage-space-for-a-database"></a>データベースの記憶域スペースの種類

データベースのファイル領域を管理するには、以下に示す記憶域スペースの量について理解することが重要です。

|データベースの量|定義|説明|
|---|---|---|
|**使用済みのデータ領域**|データベース データを格納するために使用された領域の量。|一般的に、使用済みの領域は挿入 (削除) で増加 (減少) します。 操作に関連するデータの量とパターン、および断片化によっては、挿入または削除時に使用される領域が変わらない場合があります。 たとえば、各データ ページから 1 行を削除しても、使用される領域が減らない場合があります。|
|**割り当て済みのデータ領域**|データベース データの格納に使用できるフォーマット済みファイル領域の量。|割り当て済みの領域の量は自動的に増えますが、削除後に自動的に減ることはありません。 このような動作で領域を再フォーマットする必要がないため、以降の挿入はより高速になります。|
|**割り当て済みで未使用のデータ領域**|割り当て済みのデータ領域と使用済みのデータ領域の差。|この量は、データベースのデータ ファイルを縮小して再利用できる空き領域の上限を表します。|
|**データの最大サイズ**|データベース データの格納に使用できる領域の最大量。|データの最大サイズを超えて割り当て済みのデータ領域を拡大することはできません。|

次の図は、データベースの異なる種類の記憶域スペース間の関係を示しています。

![記憶域スペースの種類と関係](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>記憶域スペースの情報について単一データベースのクエリを実行する

次のクエリを使用して、単一データベースの記憶域スペースの量を確認できます。  

### <a name="database-data-space-used"></a>使用済みのデータベース データ領域

使用されているデータベース データ領域の量を返すように次のクエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC;
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
HAVING type_desc = 'ROWS';
```

### <a name="database-data-max-size"></a>データベース データの最大サイズ

データベース データの最大サイズを返すように次のクエリを変更します。  クエリ結果の単位はバイトです。

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes;
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>エラスティック プールの記憶域スペースの種類

エラスティック プールのファイル領域を管理するには、以下に示す記憶域スペースの量について理解することが重要です。

|エラスティック プールの量|定義|説明|
|---|---|---|
|**使用済みのデータ領域**|エラスティック プール内のすべてのデータベースで使用されるデータ領域の合計。||
|**割り当て済みのデータ領域**|エラスティック プール内のすべてのデータベースで割り当て済みのデータ領域の合計。||
|**割り当て済みで未使用のデータ領域**|エラスティック プール内のすべてのデータベースで割り当て済みのデータ領域と使用済みのデータ領域の差。|この量は、データベースのデータ ファイルを縮小して再利用できる、エラスティック プールに割り当てられた領域の上限を表します。|
|**データの最大サイズ**|エラスティック プールのすべてのデータベースに使用できるデータ領域の最大量。|エラスティック プールに割り当てられた領域は、エラスティック プールの最大サイズを超えないようにする必要があります。  最大サイズを超えた場合は、割り当て済みで未使用の領域を、データベースのデータ ファイルを縮小して再利用できます。|

> [!NOTE]
> "エラスティック プールの記憶域が上限に達しました" というエラー メッセージは、データベース オブジェクトがエラスティック プールの記憶域の上限に達するほどの領域を割り当てられていることを示していますが、データ領域の割り当てに未使用の領域が存在する場合があります。 エラスティック プールの記憶域の上限を引き上げるか、短期的な解決策として、以下の「[**未使用の割り当て済み領域を再利用する**](#reclaim-unused-allocated-space)」セクションを使用してデータ領域を解放することを検討してください。 また、データベース ファイルの縮小によってパフォーマンスが低下するおそれがあることにも注意する必要があります。下の「[**インデックスの再構築**](#rebuild-indexes)」セクションを参照してください。

## <a name="query-an-elastic-pool-for-storage-space-information"></a>記憶域スペースの情報についてエラスティック プールのクエリを実行する

次のクエリを使用して、エラスティック プールの記憶域スペースの量を確認できます。  

### <a name="elastic-pool-data-space-used"></a>使用済みのエラスティック プールのデータ領域

使用済みのエラスティック プールのデータ領域の量を返すように次のクエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>割り当て済みのエラスティック プールのデータ領域と未使用の割り当て済み領域

次の例を変更して、エラスティック プール内の各データベースの割り当て済み領域と未使用の割り当て済み領域の一覧表を返すようにします。 この表では、未使用の割り当て済み領域があるデータベースが、最大サイズのデータベースから順に並んでいます。  クエリ結果の単位は MB です。  

エラスティック プールに割り当てられた領域の合計を確認するために、プール内の各データベースに割り当てられた領域を確認するためのクエリ結果も追加することができます。 割り当て済みエラスティック プール領域は、エラスティック プールの最大サイズを超えないようにする必要があります。  

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

PowerShell スクリプトには SQL Server PowerShell モジュールが必要です。インストール方法については、[PowerShell モジュールのダウンロード](/sql/powershell/download-sql-server-ps-module)に関するページを参照してください。

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

以下のスクリーンショットは、スクリプトの出力例です。

![エラスティック プールの割り当て済み領域と未使用の割り当て領域の例](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>エラスティック プール データの最大サイズ

最後に記録されたエラスティック プール データ最大サイズを返すように、次の T-SQL クエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

## <a name="reclaim-unused-allocated-space"></a>未使用の割り当て済み領域を再利用する

> [!IMPORTANT]
> 縮小コマンドは、実行中のデータベース パフォーマンスに影響を及ぼすため、可能であれば、使用率が低い期間中に実行してください。

### <a name="shrinking-data-files"></a>データ ファイルの圧縮

データベースのパフォーマンスに影響を与える可能性があるため、Azure SQL Database ではデータ ファイルは自動的には圧縮されません。 ただし、顧客は、任意の時点で、セルフサービスによってデータ ファイルを圧縮できます。 これは定期的にスケジュールされた操作ではなく、データ ファイルの使用領域の消費量が大幅に減少した場合の 1 回きりのイベントである必要があります。

Azure SQL Database では、`DBCC SHRINKDATABASE` または `DBCC SHRINKFILE` コマンドを使用してファイルを圧縮できます。

- `DBCC SHRINKDATABASE` ではすべてのデータベース データとログ ファイルが圧縮されますが、通常は必要ありません。 このコマンドでは、一度に 1 つのファイルを圧縮します。 また、[ログ ファイルも圧縮されます](#shrinking-transaction-log-file)。 必要に応じて、Azure SQL Database によってログ ファイルが自動的に圧縮されます。
- `DBCC SHRINKFILE` コマンドでは、より高度なシナリオがサポートされます。
    - データベース内のすべてのファイルを圧縮するのではなく、必要に応じて個々のファイルを対象にすることができます。
    - 各 `DBCC SHRINKFILE` コマンドを他の `DBCC SHRINKFILE` コマンドと並列して実行することで、データベースをより高速に圧縮できます。ただし、リソースの使用率が高くなり、圧縮中に実行されるユーザー クエリがブロックされる可能性が高くなります。
    - ファイルの末尾にデータが含まれていない場合は、TRUNCATEONLY 引数を指定することで、割り当てられたファイル サイズをはるかに高速に縮小できます。 この場合、ファイル内でのデータ移動は必要ありません。
- これらの圧縮コマンドの詳細については、「[DBCC SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)」または「[DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql)」を参照してください。

次の例は、`master` データベースではなく、ターゲット ユーザー データベースに接続しているときに実行する必要があります。

`DBCC SHRINKDATABASE` を使用して、特定のデータベースのすべてのデータ ファイルとログ ファイルを圧縮するには、次のようにします。

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'database_name');
```

Azure SQL Database では、1 つのデータベースに 1 つ以上のデータ ファイルを含めることができます。 追加のデータ ファイルは、自動的に作成することしかできません。 データベースのファイル レイアウトを確認するには、次のサンプル スクリプトを使用して `sys.database_files` カタログ ビューに対してクエリを実行します。

```sql
-- Review file properties, including file_id values to reference in shrink commands
SELECT file_id,
       name,
       CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8 / 1024. AS space_used_mb,
       CAST(size AS bigint) * 8 / 1024. AS space_allocated_mb,
       CAST(max_size AS bigint) * 8 / 1024. AS max_size_mb
FROM sys.database_files
WHERE type_desc IN ('ROWS','LOG');
GO
```

次の例のように、`DBCC SHRINKFILE` コマンドを使用して 1 つのファイルだけに対して圧縮を実行します。

```sql
-- Shrink database data file named 'data_0` by removing all unused at the end of the file, if any.
DBCC SHRINKFILE ('data_0', TRUNCATEONLY);
GO
```

また、データベース ファイルの縮小によってパフォーマンスが低下するおそれがあることにも注意する必要があります。下の[インデックスの再構築](#rebuild-indexes)に関するセクションを参照してください。 

### <a name="shrinking-transaction-log-file"></a>トランザクション ログ ファイルの圧縮

データ ファイルとは異なり、Azure SQL Database では、領域不足エラーが発生する可能性のある領域の過剰使用を防ぐために、トランザクション ログ ファイルが自動的に圧縮されます。 通常は、お客様がトランザクション ログ ファイルを圧縮する必要はありません。

Premium および Business Critical サービス レベルでは、トランザクション ログのサイズが大きくなると、ローカル ストレージの使用量が[最大ローカル ストレージ](resource-limits-logical-server.md#storage-space-governance)の制限に近づく大きな一因となる可能性があります。 ローカル ストレージの使用量が上限に近づいている場合は、次の例に示すように、[DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql) コマンドを使用してトランザクション ログを圧縮できます。 これにより、定期的な自動圧縮操作を待たずに、コマンドが完了するとすぐにローカル ストレージが解放されます。

次の例は、master データベースではなく、ターゲット ユーザー データベースに接続しているときに実行する必要があります。

```tsql
-- Shrink the database log file (always file_id = 2), by removing all unused space at the end of the file, if any.
DBCC SHRINKFILE (2, TRUNCATEONLY);
```

### <a name="auto-shrink"></a>自動圧縮

代わりに、データベースの自動圧縮を有効にすることもできます。 ただし、自動圧縮は、`DBCC SHRINKDATABASE` および `DBCC SHRINKFILE` よりもファイル領域の解放の効果が低くなる可能性があります。  

自動圧縮は、エラスティック プールに多くのデータベースが含まれており、使用されているデータ ファイル領域が大幅に増減する場合に役立ちます。 これは一般的なシナリオではありません。 

既定では、自動圧縮は無効になっています。これは、ほとんどのデータベースで推奨されます。 自動圧縮を有効にすることが必要になった場合は、永続的に有効にしておくのではなく、領域管理の目標を達成したら無効にすることをお勧めします。 詳細については、「[AUTO_SHRINK に関する考慮事項](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink)」を参照してください。

自動圧縮を有効にするには、(master データベースではなく) 対象のデータベースに接続しているときに次のコマンドを実行します。

```sql
-- Enable auto-shrink for the current database.
ALTER DATABASE CURRENT SET AUTO_SHRINK ON;
```

このコマンドの詳細については、[DATABASE SET](/sql/t-sql/statements/alter-database-transact-sql-set-options) オプションをご覧ください。

### <a name="index-maintenance-before-or-after-shrink"></a><a name="rebuild-indexes"></a>圧縮の前または後のインデックスのメンテナンス

データ ファイルに対して圧縮操作を完了すると、インデックスが断片化し、大規模なスキャンを使用するクエリなど、特定のワークロードに対するパフォーマンスの最適化の有効性が失われる可能性があります。 圧縮操作の完了後にパフォーマンスが低下する場合は、インデックスを再構築するためのインデックスのメンテナンスを検討してください。 

データベース内のページの密度が低い場合は、各データ ファイルで移動の必要なページ数が増えるため、圧縮時間が長くなります。 圧縮コマンドを実行する前に、平均ページ密度を確認することをお勧めします。 ページの密度が低い場合は、圧縮を実行する前にインデックスを再構築または再編成して、ページの密度を増やします。 ページ密度を確認するサンプル スクリプトなどの詳細については、「[クエリのパフォーマンスを向上させてリソースの消費を削減するためにインデックスのメンテナンスを最適化する](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)」を参照してください。

## <a name="next-steps"></a>次のステップ

- データベースの最大サイズについては、以下を参照してください。
  - [Azure SQL Database の単一データベースに対する仮想コアベースの購入モデルの制限](resource-limits-vcore-single-databases.md)
  - [DTU ベースの購入モデルを使用した単一データベースに対するリソース制限](resource-limits-dtu-single-databases.md)
  - [Azure SQL Database のエラスティック プールに対する仮想コアベースの購入モデルの制限](resource-limits-vcore-elastic-pools.md)
  - [DTU ベースの購入モデルを使用したエラスティック プールのリソース制限](resource-limits-dtu-elastic-pools.md)
