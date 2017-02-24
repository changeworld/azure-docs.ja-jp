---
title: "REST を使って Azure SQL Data Warehouse の一時停止、再開、スケーリングを行う | Microsoft Docs"
description: "コンピューティング能力を管理するための PowerShell のタスク。 DWU を調整することで、コンピューティング リソースをスケーリングします。 また、コストを節約するために、コンピューティング リソースを一時停止および再開します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: barbkess
editor: 
ms.assetid: 21de7337-9356-49bb-a6eb-06c1beeba2c4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: f589111d3a4da061e1cc3313632dd0b5403dc278
ms.openlocfilehash: 56651c2eb2f702b2ec51070b25239f1acb415289


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Azure SQL Data Warehouse のコンピューティング能力の管理 (REST)
> [!div class="op_single_selector"]
> * [概要](sql-data-warehouse-manage-compute-overview.md)
> * [ポータル](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST ()](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>コンピューティング能力のスケーリング
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU を変更するには、[データベースの作成または更新][Create or Update Database] REST API を使います。 次の例では、MyServer サーバーにホストされているデータベース MySQLDW のサービスレベル目標を DW1000 に設定します。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

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

## <a name="pause-compute"></a>コンピューティングの一時停止
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

データベースを一時停止するには、[データベースの一時停止][Pause Database] REST API を使います。 次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが一時停止されます。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>コンピューティングの再開
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

データベースを開始するには、[データベースの再開][Resume Database] REST API を使います。 次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが開始されます。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>次のステップ
他の管理タスクについては、[管理の概要][Management overview]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Pause Database]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Resume Database]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Create or Update Database]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/



<!--HONumber=Jan17_HO4-->


