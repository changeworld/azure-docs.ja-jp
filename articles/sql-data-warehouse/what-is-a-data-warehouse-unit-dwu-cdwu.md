---
title: Azure SQL Data Warehouse の Data Warehouse ユニット (DWU、cDWU) の概要 | Microsoft Docs
description: Azure SQL Data Warehouse のパフォーマンス スケールアウト機能。 DWU、cDWU を調整してスケールアウトする方法や、コンピューティング リソースを一時停止および再開してコストを節約する方法を説明します。
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/15/2018
ms.author: jrj;barbkess
ms.openlocfilehash: f634bdde2c71f7563df11f686d7ce217311df81d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Data Warehouse ユニット (DWU) とコンピューティング Data Warehouse ユニット (cDWU)
ここでは、Azure SQL Data Warehouse の Data Warehouse ユニット (DWU) とコンピューティング Data Warehouse ユニット (cDWU) について説明します。 また、最適な Data Warehouse ユニットの数の選択についての推奨事項と、Data Warehouse ユニットの数を変更する方法も示します。 

## <a name="what-are-data-warehouse-units"></a>Data Warehouse ユニットとは
SQL Data Warehouse では、CPU、メモリ、および IO が Data Warehouse ユニット (DWU) と呼ばれるコンピューティング スケール単位にまとめられます。 DWU は、コンピューティング リソースとパフォーマンスの抽象的な正規化された単位を表します。 サービス レベルを変更すると、システムに割り当てられている DWU の数が変更され、システムのパフォーマンスとコストが調整されます。 

パフォーマンスの向上にコストをかける場合は、Data Warehouse ユニットの数を増やすことができます。 パフォーマンスのコストを抑える場合は、Data Warehouse ユニットの数を減らします。 ストレージのコストとコンピューティングのコストは、別々に請求されます。したがって、Data Warehouse ユニットの数を変更してもストレージ コストには影響しません。

Data Warehouse ユニットのパフォーマンスは、次のようなデータ ウェアハウスのワークロードのメトリックに基づいています。

- 標準的なデータ ウェアハウス クエリが大量の行をスキャンして、複雑な集計を実行するときの速度。 これは I/O と CPU を集中的に使用する操作です。
- データ ウェアハウスが Azure Storage Blob または Azure Data Lake からのデータを取り込むときの速度。 これはネットワークと CPU を集中的に使用する操作です。 
- [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL コマンドがテーブルをコピーするときの速度。 この操作には、ストレージからのデータの読み取り、アプライアンスのノード間でのデータ分散、ストレージへのデータの再書き込みが必要です。 これは CPU、IO、ネットワークを集中的に使用する操作です。

DWU の数を増やすと、次のメリットが得られます。
- スキャン、集計、CTAS ステートメントに関するシステムのパフォーマンスが直線的に向上します。
- PolyBase の読み込み操作のリーダーとライターの数が増えます。
- 同時クエリと同時実行スロットの最大数が増えます。

## <a name="performance-tiers-and-data-warehouse-units"></a>パフォーマンス レベルと Data Warehouse ユニット

各パフォーマンス レベルで、Data Warehouse ユニットの測定単位は若干異なります。 スケールの単位は課金に直接つながるため、この違いは請求書に反映されます。

- [エラスティック用に最適化] パフォーマンス レベルは、Data Warehouse ユニット (DWU) で測定されます。
- [計算用に最適化] パフォーマンス レベルは、コンピューティング Data Warehouse ユニット (cDWU) で測定されます。 

DWU と cDWU はいずれも、コンピューティングのスケール アップとスケール ダウン、データ ウェアハウスの使用が不要になった場合のコンピューティングの一時停止をサポートしています。 これらの操作はすべて、オンデマンドで実行できます。 また、[計算用に最適化] パフォーマンス レベルでは、パフォーマンス向上のためにコンピューティング ノードでのローカル ディスク ベースのキャッシュを使用します。 スケール操作やシステムの一時停止を行うと、このキャッシュが無効化されるため、最適なパフォーマンスを実現する前にキャッシュの準備期間が必要となります。  

Data Warehouse ユニットを増やすと、コンピューティング リソースが直線的に増加します。 [計算用に最適化] パフォーマンス レベルは最適なクエリ パフォーマンスと最大のスケールを提供しますが、エントリ価格が高くなります。 これは常にパフォーマンスを必要とする企業向けに設計されています。 これらのシステムは、キャッシュを最大限に活用します。 

### <a name="capacity-limits"></a>容量制限
各 SQL Server (たとえば myserver.database.windows.net) には、特定の数の Data Warehouse ユニットを許可する[データベース トランザクション ユニット (DTU)](../sql-database/sql-database-what-is-a-dtu.md) クォータがあります。 詳細については、[ワークロード管理の容量制限](sql-data-warehouse-service-capacity-limits.md#workload-management)に関する記事を参照してください。


## <a name="how-many-data-warehouse-units-do-i-need"></a>必要な Data Warehouse ユニットの数
理想的な Data Warehouse ユニットの数は、ワークロードと、システムに読み込まれているデータの量に大きく依存します。

ワークロードに最適な DWU を特定する手順は次のとおりです。

1. 開発中は、[エラスティック用に最適化] パフォーマンス レベルを使用して小規模な DWU を選択することから始めます。  この段階で重要なのは機能の検証なので、[エラスティック用に最適化] オプションを選択するのが妥当です。 手始めとしては、DW200 が適しています。 
2. システムへのデータの読み込みをテストする際に、アプリケーションのパフォーマンスを監視し、選択した DWU の数に対するパフォーマンスの変化を観察します。
3. 定期的なピーク アクティビティ期間のための追加要件があれば識別します。 ワークロードがアクティビティに顕著なピークと谷があることを示しており、頻繁にスケール操作を行う正当な理由がある場合は、[エラスティック用に最適化] レベルを選択することをお勧めします。
4. 1000 を超える DWU が必要な場合は、最適なパフォーマンスが得られるように、[計算用に最適化] パフォーマンス レベルを選択することをお勧めします。

SQL Data Warehouse は、膨大な量のコンピューティングをプロビジョニングし、相当な量のデータのクエリを実行することができるスケールアウト システムです。 特に大規模な DWU での実際のスケール機能を確認する場合は、CPU に十分なデータをフィードできるように、スケール時のデータ セットのスケールを決定してください。 スケールのテストでは、少なくとも 1 TB を使用することをお勧めします。

> [!NOTE]
>
> コンピューティング ノード間で作業を分割することができる場合、クエリのパフォーマンスを引き上げるには、並列処理を増やすしかありません。 スケーリングしてもパフォーマンスが変わらない場合は、テーブルのデザインやクエリのチューニングが必要な可能性があります。 クエリのチューニングのガイダンスについては、次の[パフォーマンス](sql-data-warehouse-overview-manage-user-queries.md)に関する記事をご覧ください。 

## <a name="permissions"></a>アクセス許可

Data Warehouse ユニットを変更するには、「[ALTER DATABASE][ALTER DATABASE]」で説明されているアクセス許可が必要です。 

## <a name="view-current-dwu-settings"></a>現在の DWU 設定の表示

現在の DWU 設定を表示するには、次の手順に従います。

1. Visual Studio で SQL Server オブジェクト エクスプローラーを開きます。
2. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
3. sys.database_service_objectives 動的管理ビューから選択します。 たとえば次のようになります。 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Data Warehouse ユニットの変更

### <a name="azure-portal"></a>Azure ポータル
DWU または cDWU を変更するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) を開き、データベースを開いて、**[スケール]** をクリックします。

2. **[スケール]** で、スライダーを左または右に移動して DWU 設定を変更します。

3. **[Save]** をクリックします。 確認メッセージが表示されます。 **[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

### <a name="powershell"></a>PowerShell
DWU または cDWU を変更するには、[Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase] PowerShell コマンドレットを使用します。 次の例では、MyServer サーバーにホストされているデータベース MySQLDW のサービスレベル目標を DW1000 に設定します。

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
T-SQL で現在の DWU または cDWU の設定を表示したり、設定を変更したり、進行状況を確認したりできます。 

DWU または cDWU を変更するには、次の手順に従います。

1. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
2. [ALTER DATABASE][ALTER DATABASE] TSQL ステートメントを使います。 次の例では、MySQLDW データベースのサービス レベル目標を DW1000 に設定します。 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API

DWU を変更するには、[データベースの作成または更新][データベースの作成または更新] REST API を使用します。 次の例では、MyServer サーバーにホストされているデータベース MySQLDW のサービスレベル目標を DW1000 に設定します。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


## <a name="check-status-of-dwu-changes"></a>DWU 変更の状態の確認

DWU の変更は、完了までに数分かかる場合があります。 スケーリングを自動的に行う場合は、別のアクションに進む前に特定の操作が完了していることを確認するロジックを実装することを検討してください。 

さまざまなエンドポイントを通じてデータベースの状態を確認することで、自動化を適切に実装できます。 ポータルでは、操作の完了通知とデータベースの現在の状態が提供されます。ただし、プログラムを使って状態を確認することはできません。 

Azure Portal でスケールアウト操作のデータベースの状態を確認することはできません。

DWU の変更の状態を確認するには、次の手順に従います。

1. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
2. 次のクエリを送信して、データベースの状態を確認します。


```sql
SELECT    *
FROM      sys.databases
;
```

3. 次のクエリを送信して、操作の状態を確認します。

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

この DMV は、SQL Data Warehouse 上の操作と操作の状態 (IN_PROGRESS または COMPLETED のいずれか) などのさまざまな管理操作に関する情報を返します。

## <a name="the-scaling-workflow"></a>スケーリングのワークフロー

スケール操作を開始すると、システムは最初にすべての開いているトランザクションを中止し、すべての開いているトランザクションをロールバックして一貫性のある状態を確保します。 スケール操作の場合、このトランザクションのロールバックが完了した後でのみスケーリングが行われます。  

- スケールアップ操作の場合、システムは追加のコンピューティングをプロビジョニングした後で、ストレージ レイヤーに再アタッチします。 
- スケールダウン操作の場合は、不要なノードがストレージからデタッチされ、残りのノードに再アタッチします。

## <a name="next-steps"></a>次の手順
その他の主要なパフォーマンスの概念を理解するために、次の記事を参照してください。

* [ワークロードと同時実行の管理][Workload and concurrency management]
* [テーブル設計の概要][Table design overview]
* [テーブルの分散][Table distribution]
* [テーブルのインデックス作成][Table indexing]
* [テーブルのパーティション分割][Table partitioning]
* [テーブルの統計][Table statistics]
* [ベスト プラクティス][Best practices]

<!--Image reference-->

<!--Article references-->

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./resource-classes-for-workload-management.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
