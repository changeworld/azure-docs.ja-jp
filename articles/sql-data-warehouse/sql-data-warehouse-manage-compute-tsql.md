<properties
   pageTitle="Azure SQL Data Warehouse のコンピューティング能力の管理 (T-SQL) | Microsoft Azure"
   description="DWU を調整することでパフォーマンスをスケールアウトする Transact-SQL (T-SQL) タスク。ピーク時以外にリソースをスケール バックして、コストを削減します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL Data Warehouse のコンピューティング能力の管理 (T-SQL)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-manage-compute-overview.md)
- [ポータル](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST ()](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


ワークロードの変化する需要に合わせてコンピューティング リソースとメモリをスケールアウトすることで、パフォーマンスをスケーリングします。ピーク時以外にリソースをスケール バックするか、すべてのコンピューティング処理を一時停止して、コストを削減します。

このタスクのコレクションでは、T-SQL を使用して次の操作を実行します。

- 現在の DWU 設定の表示
- DWU を調整することによるコンピューティング リソースの変更

データベースを一時停止または再開する方法については、この記事の上部にある他のプラットフォーム オプションのいずれかを選択してください。

これについては、[コンピューティング能力の管理の概要][]に関するページをご覧ください。

<a name="current-dwu-bk"></a>

## 現在の DWU 設定の表示

データベースの現在の DWU 設定を表示するには、次の手順に従います。

1. Visual Studio 2015 で SQL Server オブジェクト エクスプローラーを開きます。
2. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
2. sys.database\_service\_objectives 動的管理ビューから選択します。たとえば次のようになります。

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a> <a name="scale-compute-bk"></a>

## コンピューティングのスケーリング

[AZURE.INCLUDE [SQL Data Warehouse の DWU のスケーリングの説明](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU を変更するには、次の手順に従います。


1. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
2. [ALTER DATABASE][] TSQL ステートメントを使用します。次の例では、MySQLDW データベースのサービス レベル目標を DW1000 に設定します。

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## 次のステップ

他の管理タスクについては、[管理の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[管理の概要]: ./sql-data-warehouse-overview-manage.md
[コンピューティング能力の管理の概要]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0810_2016-->