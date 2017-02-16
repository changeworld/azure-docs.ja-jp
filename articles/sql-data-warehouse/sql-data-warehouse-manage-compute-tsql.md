---
title: "T-SQL を使って Azure SQL Data Warehouse の一時停止、再開、スケーリングを行う | Microsoft Docs"
description: "DWU を調整することでパフォーマンスをスケールアウトする Transact-SQL (T-SQL) タスク。 ピーク時以外にリソースをスケール バックして、コストを削減します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: f589111d3a4da061e1cc3313632dd0b5403dc278
ms.openlocfilehash: f93e5802141b16862f5e37126196069bd32c1f19


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Azure SQL Data Warehouse のコンピューティング能力の管理 (T-SQL)
> [!div class="op_single_selector"]
> * [概要](sql-data-warehouse-manage-compute-overview.md)
> * [ポータル](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST ()](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>現在の DWU 設定の表示
データベースの現在の DWU 設定を表示するには、次の手順に従います。

1. Visual Studio 2015 で SQL Server オブジェクト エクスプローラーを開きます。
2. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
3. sys.database_service_objectives 動的管理ビューから選択します。 たとえば次のようになります。 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>コンピューティングのスケーリング
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU を変更するには、次の手順に従います。

1. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
2. [ALTER DATABASE][ALTER DATABASE] TSQL ステートメントを使います。 次の例では、MySQLDW データベースのサービス レベル目標を DW1000 に設定します。 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>次のステップ
他の管理タスクについては、[管理の概要][Management overview]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/



<!--HONumber=Jan17_HO4-->


