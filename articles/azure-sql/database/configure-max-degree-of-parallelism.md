---
title: 並列処理の最大限度 (MAXDOP) の構成
titleSuffix: Azure SQL Database
description: 並列処理の最大限度 (MAXDOP) について説明します。
ms.date: 03/29/2021
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
ms.openlocfilehash: 31ddf15975abdce70ea02b5de64ea5611e7e72b3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111117"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Azure SQL Database での並列処理の最大限度 (MAXDOP) の構成
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  この記事では、Azure SQL Database での **並列処理の最大限度 (MAXDOP)** と、それを構成する方法について説明します。 

> [!NOTE]
> **この内容は Azure SQL Database が対象です。** Azure SQL Database は、Microsoft SQL Server データベース エンジンの最新の安定バージョンに基づいているため、トラブルシューティングと構成のオプションが異なる場合はありますが、コンテンツの大半は似ています。 SQL Server での MAXDOP の詳細については、「[max degree of parallelism サーバー構成オプションの構成](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)」を参照してください。

## <a name="overview"></a>概要
  Azure SQL Database では、新しい単一データベースとエラスティック プール データベースの既定の MAXDOP 設定は 8 です。 これは、データベース エンジンによるクエリの実行に複数のスレッドが使用される可能性があることを意味します。 サーバー全体の既定の MAXDOP が 0 (無制限) の SQL Server とは異なり、既定では Azure SQL Database の新しいデータベースは MAXDOP 8 に設定されています。 この既定値によって、不要なリソース使用が阻止され、一貫したカスタマー エクスペリエンスが確保されます。 通常は、Azure SQL Database ワークロードで MAXDOP をさらに構成する必要はありませんが、これは高度なパフォーマンス チューニングの演習として有益である場合があります。

> [!Note]
>   2020 年 9 月に、Azure SQL Database サービスの長年の利用統計情報に基づいて、幅広い顧客のワークロードに対する最適な値として新しいデータベースの既定値に [MAXDOP 8 が選択されました](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)。 この既定値は、過剰な並列処理によるパフォーマンスの問題を防ぐことに役立っています。 以前は、新しいデータベースの既定の設定は MAXDOP 0 でした。 2020 年 9 月より前に作成された既存のデータベースについては、MAXDOP データベース スコープ構成オプションは変更されていません。

  一般に、データベース エンジンでは、並列処理を使用してクエリを実行することを選択すると、実行時間が短縮されます。 ただし、過剰な並列処理では、クエリのパフォーマンスを向上させることなく、過剰なプロセッサ リソースが消費される可能性があります。 大規模で過剰な並列処理は、同じデータベース エンジン インスタンスで実行されているすべてのクエリのクエリ パフォーマンスに悪影響を及ぼす可能性があります。そのため、並列処理の上限を設定することは、SQL Server のワークロードにおける一般的なパフォーマンス チューニングの演習になっています。

  次の表で、異なる MAXDOP 値を使用してクエリを実行する場合のデータベース エンジンの動作について説明します。

| MAXDOP | 動作 | 
|--|--|
| = 1 | データベース エンジンによるクエリの実行には、複数の同時実行スレッドは使用されません。 | 
| > 1 | データベース エンジンによって、並列スレッド数の上限が設定されます。 データベース エンジンによって、使用する余分なワーカー スレッドの数が選択されます。 クエリの実行に使用されたワーカー スレッドの合計数が、指定された MAXDOP 値を超えている可能性があります。 |
| = 0 | データベース エンジンは、論理プロセッサの合計数による上限付きで、多数の並列スレッドを使用できます。 データベース エンジンによって、使用する並列スレッドの数が選択されます。| 
| | |
  
##  <a name="considerations"></a><a name="Considerations"></a> 考慮事項  

-   Azure SQL Database では、既定の MAXDOP 値を変更できます。
    -   クエリ レベルでは、**MAXDOP** [クエリヒント](/sql/t-sql/queries/hints-transact-sql-query)を使用します。     
    -   データベース レベルでは、**MAXDOP** [データベース スコープ構成](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)を使用します。

-   Azure SQL Database には、従来からの SQL Server の MAXDOP に関する考慮事項と[推奨事項](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)が適用されます。 

-   MAXDOP は[タスク](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql)ごとに適用されます。 これは、[要求](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)ごとまたはクエリごとには適用されません。 これは、並列クエリの実行中に、MAXDOP に応じて決まる上限を持つ複数のタスクを 1 つの要求で生成できることを意味します。 詳細については、「[スレッドおよびタスクのアーキテクチャ ガイド](/sql/relational-databases/thread-and-task-architecture-guide)」の *並列タスクのスケジュール* に関するセクションを参照してください。 
  
-   インデックスを作成または再構築したり、クラスター化インデックスを削除するインデックス操作には、リソースを集中して使用するものがあります。 `CREATE INDEX` または `ALTER INDEX` ステートメントの MAXDOP インデックス オプションを指定して、インデックス操作のデータベースの max degree of parallelism 値をオーバーライドできます。 MAXDOP 値は実行時にステートメントに適用され、インデックス メタデータには保存されません。 詳細については、「 [並列インデックス操作の構成](/sql/relational-databases/indexes/configure-parallel-index-operations)」を参照してください。  
  
-   クエリおよびインデックスの操作だけでなく、この MAXDOP に対するデータベースのスコープ構成オプションも DBCC CHECKTABLE、DBCC CHECKDB、および DBCC CHECKFILEGROUP の並列処理を制御します。 

##  <a name="recommendations"></a><a name="Security"></a> 推奨事項  

  データベースの MAXDOP を変更すると、クエリのパフォーマンスとリソースの使用率に、良くも悪くも大きな影響を与える可能性があります。 ただし、すべてのワークロードに最適な単一の MAXDOP 値はありません。 MAXDOP の設定についての推奨事項は微妙であり、多くの要因に依存しています。 

  ピーク時の同時実行ワークロードによっては、他のものとは異なる MAXDOP で動作が向上する場合があります。 適切に構成された MAXDOP を使用すると、パフォーマンスと可用性のインシデントのリスクを軽減することができ、場合によっては、不要なリソースの使用を回避してコストを削減し、より小さいサービス目標にスケールダウンできます。

### <a name="excessive-parallelism"></a>過剰な並列処理

  多くの場合、MAXDOP が高いほど、CPU 集約型のクエリの実行時間が短縮されます。 ただし、過剰な並列処理によって CPU とワーカー スレッド リソースの他のクエリが逼迫し、他の同時実行ワークロードのパフォーマンスが悪化する場合があります。 極端なケースでは、過剰な並列処理によってすべてのデータベースまたはエラスティック プールのリソースが消費され、クエリのタイムアウト、エラー、アプリケーションの停止が発生します。 

  現時点で問題の原因になっていないと思われる場合でも、MAXDOP 0 を使用しないことをお勧めします。 過剰な並列処理が最も問題になるのは、CPU とワーカー スレッドがサービス目標でサポートされているよりも多くの同時要求を受け取る場合です。 データベースがスケールアップされている場合、または Azure SQL Database の将来のハードウェア世代が同じデータベースのサービス目標に対してより多くのコアを提供する場合に、過剰な並列処理が原因で将来発生する可能性のある問題のリスクを減らすには、MAXDOP 0 を使用しないようにします。

### <a name="modifying-maxdop"></a>MAXDOP の変更 

  ご自分の Azure SQL Database ワークロードに対して異なる MAXDOP 設定が最適であると判断した場合は、`ALTER DATABASE SCOPED CONFIGURATION` T-SQL ステートメントを使用できます。 例については、下の「[Transact-SQL を使用した例](#examples)」を参照してください。 データベースの作成後に MAXDOP を変更するには、この手順をデプロイ プロセスに追加します。

  既定でない MAXDOP がワークロード内のクエリのサブセットのみを使用する場合は、OPTION (MAXDOP) ヒントを追加することで、クエリ レベルで MAXDOP をオーバーライドできます。 例については、下の「[Transact-SQL を使用した例](#examples)」を参照してください。 

  リアルな同時クエリ負荷に関連するロード テストを使用して、MAXDOP 構成の変更を徹底的にテストします。 

  プライマリとセカンダリ レプリカの MAXDOP は、読み取り/書き込みと読み取り専用ワークロードにそれぞれ最適な MAXDOP 設定を利用できるように個別に構成できます。 これは、Azure SQL Database の[読み取りスケールアウト](read-scale-out.md)、[geo レプリケーション](active-geo-replication-overview.md)、[Azure SQL Database Hyperscale のセカンダリ レプリカ](service-tier-hyperscale.md)に適用されます。 既定では、すべてのセカンダリ レプリカで、プライマリ レプリカの MAXDOP 構成が継承されます。

## <a name="security"></a><a name="Security"></a> セキュリティ  
  
###  <a name="permissions"></a><a name="Permissions"></a> Permissions  
  `ALTER DATABASE SCOPED CONFIGURATION` ステートメントは、サーバー管理者、データベース ロール `db_owner` のメンバー、または `ALTER ANY DATABASE SCOPED CONFIGURATION` 権限が許可されているユーザーとして実行する必要があります。
 
## <a name="examples"></a>例   

  これらの例では、Azure SQL Database の新しい単一データベースに対して `SAMPLE` オプションを選択したときに、最新の **AdventureWorksLT** サンプル データベースを使用します。

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP データベース スコープ構成   

  この例では、[ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) ステートメントを使用して、`max degree of parallelism` オプションを `2` に構成する方法を示します。 設定はすぐに反映されます。 PowerShell コマンドレット [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) では、設定する T-SQL クエリが実行され、MAXDOP データベース スコープ構成が返されます。 

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
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  [Azure portal クエリ エディター](connect-query-portal.md)、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)、または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、Azure SQL Database に対して T-SQL クエリを実行できます。

1.  Azure SQL Database に接続します。 master データベース内のデータベース スコープ構成を変更することはできません。
  
2.  標準バーから、 **[新しいクエリ]** を選択します。   
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** を選択します。 


#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP データベース スコープ構成

  この例では、[sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) システム カタログ ビューを使用して、現在のデータベースの MAXDOP データベース スコープ構成を確認する方法を示します。

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  この例では、[ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) ステートメントを使用して、`max degree of parallelism` オプションを `8` に構成する方法を示します。 設定はすぐに反映されます。  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

この例は、[読み取りスケールアウト レプリカが有効になっている](read-scale-out.md)Azure SQL Database、[geo レプリケーション](active-geo-replication-overview.md)、および [Azure SQL Database Hyperscale のセカンダリ レプリカ](service-tier-hyperscale.md)で使用するためのものです。 たとえば、プライマリ レプリカがセカンダリ レプリカとして別の既定の MAXDOP に設定されている場合、読み取り/書き込みと読み取り専用ワークロードの間に違いがあると予測されます。 `sys.database_scoped_configurations` の `value_for_secondary` 列には、セカンダリ レプリカ用の設定が含まれています。

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
