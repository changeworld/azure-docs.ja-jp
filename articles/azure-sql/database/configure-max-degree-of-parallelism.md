---
title: 並列処理の最大限度 (MAXDOP) の構成
titleSuffix: Azure SQL Database
description: 並列処理の最大限度 (MAXDOP) について説明します。
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: 64eaa7d70d32ce88a98a12a38a5c59e13de58059
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020884"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Azure SQL Database での並列処理の最大限度 (MAXDOP) の構成
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  この記事では、Azure SQL Database での **並列処理の最大限度 (MAXDOP)** 構成設定について説明します。 

> [!NOTE]
> **この内容は Azure SQL Database が対象です。** Azure SQL Database は、Microsoft SQL Server データベース エンジンの最新の安定バージョンに基づいているため、トラブルシューティングと構成のオプションが異なる場合はありますが、コンテンツの大半は似ています。 SQL Server での MAXDOP の詳細については、「[max degree of parallelism サーバー構成オプションの構成](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)」を参照してください。

## <a name="overview"></a>概要
  MAXDOP により、データベース エンジンでのクエリ内並列処理が制御されます。 通常、MAXDOP 値が高いほど、クエリあたりの並列スレッドの数が増え、クエリの実行が高速化されます。 

  Azure SQL Database では、新しい単一データベースとエラスティック プール データベースの既定の MAXDOP 設定は 8 です。 この既定値では、不要なリソースの使用が防止される一方で、データベース エンジンで並列スレッドを使用してクエリの実行を高速化できます。 通常、Azure SQL Database ワークロードで MAXDOP をさらに構成する必要はありませんが、これは高度なパフォーマンス チューニングの演習として有益である場合があります。

> [!Note]
>   2020 年 9 月に、Azure SQL Database サービスの長年の利用統計情報に基づいて、幅広い顧客のワークロードに対する最適な値として MAXDOP 8 が[新しいデータベースの既定値](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)になりました。 この既定値は、過剰な並列処理によるパフォーマンスの問題を防ぐことに役立っています。 以前は、新しいデータベースの既定の設定は MAXDOP 0 でした。 2020 年 9 月より前に作成された既存のデータベースについては、MAXDOP は自動的には変更されていません。

  一般に、データベース エンジンでは、並列処理を使用してクエリを実行することを選択すると、実行時間が短縮されます。 ただし、過剰な並列処理では、クエリのパフォーマンスは向上せず、プロセッサ リソースが余分に消費される可能性があります。 大規模で過剰な並列処理は、同じデータベース エンジン インスタンスで実行されているすべてのクエリのクエリ パフォーマンスに悪影響を及ぼす可能性があります。 従来、並列処理の上限を設定することは、SQL Server ワークロードのパフォーマンス チューニングで一般的に実施されていました。

  次の表で、異なる MAXDOP 値を使用してクエリを実行する場合のデータベース エンジンの動作について説明します。

| MAXDOP | 動作 | 
|--|--|
| = 1 | データベース エンジンでは、1 つの直列スレッドを使用してクエリが実行されます。 並列スレッドは使用されません。 | 
| > 1 | データベース エンジンによって、並列スレッドで使用される追加の[スケジューラ](/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling)の数が、MAXDOP 値または論理プロセッサの合計数のいずれか小さい方に設定されます。 |
| = 0 | データベース エンジンによって、並列スレッドで使用される追加の[スケジューラ](/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling)の数が、論理プロセッサの合計数または 64 のいずれか小さい方に設定されます。 | 
| | |

> [!Note]
> 各クエリは、少なくとも 1 つのスケジューラと、そのスケジューラの 1 つのワーカー スレッドを使用して実行されます。
>
> 並列処理で実行されるクエリでは、追加のスケジューラと追加の並列スレッドが使用されます。 複数の並列スレッドが同じスケジューラで実行される可能性があるため、クエリの実行に使用されるスレッドの合計数は、指定した MAXDOP 値または論理プロセッサの合計数よりも多くなる場合があります。 詳細については、「[並列タスクのスケジューリング](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)」を参照してください。

##  <a name="considerations"></a><a name="Considerations"></a> 考慮事項  

-   Azure SQL Database では、既定の MAXDOP 値を変更できます。
    -   クエリ レベルでは、**MAXDOP** [クエリヒント](/sql/t-sql/queries/hints-transact-sql-query)を使用します。     
    -   データベース レベルでは、**MAXDOP** [データベース スコープ構成](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)を使用します。

-   Azure SQL Database には、従来からの SQL Server の MAXDOP に関する考慮事項と[推奨事項](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)が適用されます。 

-   インデックスを作成または再構築したり、クラスター化インデックスを削除するインデックス操作には、リソースを集中して使用するものがあります。 `CREATE INDEX` または `ALTER INDEX` ステートメントで MAXDOP インデックス オプションを指定することで、インデックス操作のデータベース MAXDOP 値をオーバーライドできます。 MAXDOP 値は実行時にステートメントに適用され、インデックス メタデータには保存されません。 詳細については、「 [並列インデックス操作の構成](/sql/relational-databases/indexes/configure-parallel-index-operations)」を参照してください。  
  
-   クエリおよびインデックスの操作に加えて、MAXDOP のデータベース スコープ構成オプションでも、DBCC CHECKTABLE、DBCC CHECKDB、DBCC CHECKFILEGROUP など、並列実行を使用する場合がある他のステートメントの並列処理が制御されます。 

##  <a name="recommendations"></a><a name="Recommendations"></a> 推奨事項  

  データベースの MAXDOP を変更すると、クエリのパフォーマンスとリソースの使用率に、良くも悪くも大きな影響を与える可能性があります。 ただし、すべてのワークロードに最適な単一の MAXDOP 値はありません。 MAXDOP の設定に関する[推奨事項](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)は微妙であり、多くの要因に依存しています。 

  ピーク時の同時実行ワークロードによっては、他のものとは異なる MAXDOP で動作が向上する場合があります。 MAXDOP を適切に構成すると、パフォーマンスと可用性のインシデントのリスクを軽減でき、場合によっては、不要なリソースの使用を回避することでコストを削減でき、それにより、より小さいサービス目標にスケールダウンできます。

### <a name="excessive-parallelism"></a>過剰な並列処理

  多くの場合、MAXDOP が高いほど、CPU 集約型のクエリの実行時間が短縮されます。 ただし、過剰な並列処理によって CPU とワーカー スレッド リソースの他のクエリが逼迫し、他の同時実行ワークロードのパフォーマンスが悪化する場合があります。 極端なケースでは、過剰な並列処理によってすべてのデータベースまたはエラスティック プールのリソースが消費され、クエリのタイムアウト、エラー、アプリケーションの停止が発生します。 

> [!Tip]
> MAXDOP は 0 に設定しないことをお勧めします。これは、現時点でこれによる問題が発生していないと考えられる場合でも同様です。

  過剰な並列処理が最も問題になるのは、サービス目標によって提供されている CPU とワーカー スレッド リソースでサポートできるよりも多くの同時要求がある場合です。 データベースがスケールアップされている場合、または Azure SQL Database の将来のハードウェア世代が同じデータベースのサービス目標に対してより多くのコアを提供する場合に、過剰な並列処理が原因で将来発生する可能性のある問題のリスクを減らすには、MAXDOP 0 を使用しないようにします。

### <a name="modifying-maxdop"></a>MAXDOP の変更 

  既定値とは異なる MAXDOP 設定がご自分の Azure SQL Database ワークロードに対して最適であると判断した場合は、`ALTER DATABASE SCOPED CONFIGURATION` T-SQL ステートメントを使用できます。 例については、下の「[Transact-SQL を使用した例](#examples)」を参照してください。 作成する新しいデータベースごとに MAXDOP を既定値以外の値に変更する場合は、この手順をデータベース デプロイ プロセスに追加します。

  ワークロードのクエリの小規模サブセットでのみ既定値ではない MAXDOP が有益である場合は、OPTION (MAXDOP) ヒントを追加することで、クエリ レベルで MAXDOP をオーバーライドできます。 例については、下の「[Transact-SQL を使用した例](#examples)」を参照してください。 

  リアルな同時クエリ負荷に関連するロード テストを使用して、MAXDOP 構成の変更を徹底的にテストします。 

  読み取り/書き込みワークロードおよび読み取り専用ワークロードに対して異なる MAXDOP 設定が最適である場合、プライマリ レプリカとセカンダリ レプリカの MAXDOP を個別に構成できます。 これは、Azure SQL Database の[読み取りスケールアウト](read-scale-out.md)、[geo レプリケーション](active-geo-replication-overview.md)、[Hyperscale](service-tier-hyperscale.md) セカンダリ レプリカに適用されます。 既定では、すべてのセカンダリ レプリカで、プライマリ レプリカの MAXDOP 構成が継承されます。

## <a name="security"></a><a name="Security"></a> セキュリティ  
  
###  <a name="permissions"></a><a name="Permissions"></a> Permissions  
  `ALTER DATABASE SCOPED CONFIGURATION` ステートメントは、サーバー管理者、データベース ロール `db_owner` のメンバー、または `ALTER ANY DATABASE SCOPED CONFIGURATION` 権限が許可されているユーザーとして実行する必要があります。
 
## <a name="examples"></a>例   

  これらの例では、Azure SQL Database の新しい単一データベースに対して `SAMPLE` オプションを選択したときに、最新の **AdventureWorksLT** サンプル データベースを使用します。

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP データベース スコープ構成   

  この例は、[ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) ステートメントを使用して、`MAXDOP` 構成を `2` に設定する方法を示しています。 この設定は、新しいクエリに対して直ちに有効になります。 PowerShell コマンドレット [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) では、設定する T-SQL クエリが実行され、MAXDOP データベース スコープ構成が返されます。 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

この例は、[読み取りスケールアウト レプリカが有効になっている](read-scale-out.md)Azure SQL Database、[geo レプリケーション](active-geo-replication-overview.md)、および [Azure SQL Database Hyperscale のセカンダリ レプリカ](service-tier-hyperscale.md)で使用するためのものです。 たとえば、プライマリ レプリカがセカンダリ レプリカとして別の既定の MAXDOP に設定されている場合、読み取り/書き込みと読み取り専用ワークロードの間に違いがあると予測されます。

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP_primary + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  [Azure portal クエリ エディター](connect-query-portal.md)、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)、または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、Azure SQL Database に対して T-SQL クエリを実行できます。

1.  新しいクエリ ウィンドウを開きます。

2.  MAXDOP を変更するデータベースに接続します。 master データベースのデータベース スコープ構成は変更できません。
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** を選択します。 

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP データベース スコープ構成

  この例では、[sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) システム カタログ ビューを使用して、現在のデータベースの MAXDOP データベース スコープ構成を確認する方法を示します。

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  この例は、[ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) ステートメントを使用して、`MAXDOP` 構成を `8` に設定する方法を示しています。 設定はすぐに反映されます。  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

この例は、[読み取りスケールアウト レプリカが有効になっている](read-scale-out.md) Azure SQL Database、[geo レプリケーション](active-geo-replication-overview.md)、[Hyperscale](service-tier-hyperscale.md) セカンダリ レプリカで使用するためのものです。 たとえば、プライマリ レプリカがセカンダリ レプリカとは異なる MAXDOP に設定されている場合、読み取り/書き込みワークロードと読み取り専用ワークロードの間に違いがあると予測されます。 すべてのステートメントがプライマリ レプリカで実行されます。 `sys.database_scoped_configurations` の `value_for_secondary` 列には、セカンダリ レプリカ用の設定が含まれています。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP クエリ ヒント

  この例では、`max degree of parallelism` を強制的に `2` にするクエリ ヒントを使用してクエリを実行する方法を示します。  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP インデックス オプション

  この例では、`max degree of parallelism` を強制的に `12` にするインデックス オプションを使用してインデックスを再構築する方法を示します。  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>関連項目  
* [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [並列インデックス操作の構成](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [クエリ ヒント &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [インデックス オプションの設定](/sql/relational-databases/indexes/set-index-options)     
* [Azure SQL Database のブロックの問題の概要と解決策](understand-resolve-blocking.md)

## <a name="next-steps"></a>次のステップ

* [パフォーマンスの監視とチューニング](/sql/relational-databases/performance/monitor-and-tune-for-performance)
