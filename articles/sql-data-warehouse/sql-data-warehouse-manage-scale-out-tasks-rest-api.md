<properties
   pageTitle="Azure SQL Data Warehouse のスケーラビリティ管理タスク (REST) | Microsoft Azure"
   description="Azure SQL Data Warehouse のパフォーマンスをスケールアウトする REST API タスク。DWU を調整してコンピューティング リソースを変更します。また、コストを節約するために、コンピューティング リソースを一時停止および再開します。"
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

# Azure SQL Data Warehouse のスケーラビリティ管理タスク (REST)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-overview-scalability.md)
- [ポータル](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST ()](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

コンピューティング リソースとメモリを、ワークロードの変化する需要に合わせて柔軟にスケール アウトし、ピーク時以外にはリソースをスケール バックしてコストを削減します。

このタスクのコレクションでは、REST API を使用して次の操作を実行します。

- DWU の調整によるパフォーマンスのスケーリング
- コンピューティング リソースの一時停止
- コンピューティング リソースの再開

詳細については、[パフォーマンスのスケーラビリティの概要][]に関する記事をご覧ください。

<a name="scale-performance-bk"></a>

## パフォーマンスのスケーリング

[AZURE.INCLUDE [SQL Data Warehouse の DWU のスケーリングの説明](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU を変更するには、[データベースの作成または更新][] REST API を使用します。次の例では、MyServer サーバーにホストされているデータベース MySQLDW のサービスレベル目標を DW1000 に設定します。サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## コンピューティングの一時停止

[AZURE.INCLUDE [SQL Data Warehouse の一時停止の説明](../../includes/sql-data-warehouse-pause-description.md)]

データベースを一時停止するには、[データベースの一時停止][] REST API を使用します。次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが一時停止されます。サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## コンピューティングの再開

[AZURE.INCLUDE [SQL Data Warehouse の再開の説明](../../includes/sql-data-warehouse-resume-description.md)]

データベースを開始するには、[データベースの再開][] REST API を使用します。次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが開始されます。サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## 次のステップ

他の管理タスクについては、[管理の概要][]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->
[管理の概要]: ./sql-data-warehouse-overview-manage.md
[パフォーマンスのスケーラビリティの概要]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[データベースの一時停止]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[データベースの再開]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[データベースの作成または更新]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->