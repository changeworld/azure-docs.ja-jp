---
title: "REST を使って Azure SQL Data Warehouse の一時停止、再開、スケーリングを行う | Microsoft Docs"
description: "REST API を介して、SQL Data Warehouse でコンピューティング能力を管理します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: kfile
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/13/2018
ms.author: barbkess
ms.openlocfilehash: cb5b6221a5fc1d02ed1d93d56fd3db4858923307
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
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
他の管理タスクについては、[管理の概要](./sql-data-warehouse-overview-manage.md)に関する記事をご覧ください。

