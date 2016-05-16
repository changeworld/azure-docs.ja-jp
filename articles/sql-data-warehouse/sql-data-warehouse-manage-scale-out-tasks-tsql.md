<properties
pageTitle="Azure SQL Data Warehouse のスケーラビリティ管理タスク (TSQL) | Microsoft Azure"
   description="Azure SQL Data Warehouse のパフォーマンスをスケールアウトする TSQL タスク。TSQL を使用して DWU を調整することで、コンピューティング リソースを変更します。"
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
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL Data Warehouse のスケーラビリティ管理タスク (TSQL)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-overview-scalability.md)
- [ポータル](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST ()](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


コンピューティング リソースとメモリを、ワークロードの変化する需要に合わせて柔軟にスケール アウトし、ピーク時以外にはリソースをスケール バックしてコストを削減します。

このタスクのコレクションでは、TSQL を使用して次の操作を実行します。

- 現在の DWU 設定の表示
- DWU を調整することによるコンピューティング リソースの変更

データベースを一時停止または再開する方法については、この記事の上部にある他のプラットフォーム オプションのいずれかを選択してください。

詳細については、[パフォーマンスのスケーラビリティの概要][]に関する記事をご覧ください。

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

<a name="scale-dwu-bk"></a>

## DWU のスケーリング

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

他の管理タスクについては、[管理の概要][]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[管理の概要]: ./sql-data-warehouse-overview-manage.md
[パフォーマンスのスケーラビリティの概要]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->