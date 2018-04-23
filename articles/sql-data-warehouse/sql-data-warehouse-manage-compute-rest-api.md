---
title: REST を使って Azure SQL Data Warehouse の一時停止、再開、スケーリングを行う | Microsoft Docs
description: REST API を介して、SQL Data Warehouse でコンピューティング能力を管理します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 21423d69bf2cf06bcd208082ce492bf5dd038e29
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の REST API
Azure SQL Data Warehouse でコンピューティング能力を管理するための REST API について説明します。

## <a name="scale-compute"></a>コンピューティングのスケーリング
Data Warehouse ユニットを変更するには、[データベースの作成または更新](/rest/api/sql/databases/createorupdate) REST API を使います。 次の例では、MyServer サーバーにホストされているデータベース MySQLDW の Data Warehouse ユニットを DW1000 に設定します。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>コンピューティングの一時停止

データベースを一時停止するには、[データベースの一時停止](/rest/api/sql/databases/pause) REST API を使います。 次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが一時停止されます。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>コンピューティングの再開

データベースを開始するには、[データベースの再開](/rest/api/sql/databases/resume) REST API を使います。 次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが開始されます。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>データベース状態の確認

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>次の手順
詳細については、[コンピューティングの管理](sql-data-warehouse-manage-compute-overview.md)に関するページを参照してください。

