---
title: PowerShell コマンドレット
description: データベースの一時停止と再開など、Azure SQL Data Warehouse でよく使用される PowerShell コマンドレットを紹介します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c5f85f102d72ac2e4a0315109748d48573f49407
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721185"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>SQL Data Warehouse の PowerShell コマンドレットと REST API
SQL Data Warehouse の管理タスクの多くは、Azure PowerShell コマンドレットまたは REST API を使用して管理できます。  以下に示す例では、PowerShell コマンドを使用して、SQL Data Warehouse で一般的なタスクを自動化する方法を示しています。  適切な REST の例については、 [REST を使用したのスケーラビリティの管理](sql-data-warehouse-manage-compute-rest-api.md)に関する記事をご覧ください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell コマンドレットの使用開始
1. Windows PowerShell を開きます。
2. PowerShell プロンプトで、次のコマンドを実行して Azure Resource Manager にサインインし、サブスクリプションを選択します。
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>SQL Data Warehouse の一時停止の例
"Server01" という名前のサーバーでホストされている "Database02" という名前のデータベースを一時停止します。  サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
バリエーションであるこの例では、取得したオブジェクトを [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase) にパイプ処理します。  その結果、データベースが一時停止されます。 最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>SQL Data Warehouse の開始の例
"Server01" という名前のサーバーでホストされている "Database02" という名前のデータベースを再開します。 サーバーは "ResourceGroup1" という名前のリソース グループ内にあります。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースを取得します。 取得したオブジェクトを [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase) にパイプ処理します。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として "foo" を使用してください。
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>その他のサポートされている PowerShell コマンドレット
Azure SQL Data Warehouse でサポートされている PowerShell コマンドレットを次に示します。

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>次のステップ
PowerShell のその他の例については、次のトピックをご覧ください。

* [PowerShell を使用して SQL Data Warehouse を作成する](create-data-warehouse-powershell.md)
* [データベースの復元](sql-data-warehouse-restore-database-powershell.md)

PowerShell で自動化できるその他のタスクについては、[Azure SQL Database コマンドレット](https://docs.microsoft.com/powershell/module/az.sql)に関するページを参照してください。 Azure SQL Data Warehouse ではサポートされていない Azure SQL Database コマンドレットがあります。  REST で自動化できるタスクの一覧については、「[Azure SQL Database の操作](https://msdn.microsoft.com/library/azure/dn505719.aspx)」を参照してください。
