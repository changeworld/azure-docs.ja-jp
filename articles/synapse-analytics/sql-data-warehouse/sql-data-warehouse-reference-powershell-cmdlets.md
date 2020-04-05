---
title: PowerShell と REST API
description: データベースを一時停止および再開する方法など、Azure Synapse Analytics SQL プールの主な PowerShell コマンドレットを確認してください。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351383"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>Azure Synapse Analytics SQL プールの PowerShell と REST API

Azure PowerShell コマンドレットまたは REST API を使用して、多くの Azure Synapse Analytics SQL プール管理タスクを管理できます。  以下に示す例では、PowerShell コマンドを使用して、SQL プールで一般的なタスクを自動化する方法を示しています。  適切な REST の例については、 [REST を使用したのスケーラビリティの管理](sql-data-warehouse-manage-compute-rest-api.md)に関する記事をご覧ください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell コマンドレットの使用開始

1. Windows PowerShell を開きます。
2. PowerShell プロンプトで、次のコマンドを実行して Azure Resource Manager にサインインし、サブスクリプションを選択します。
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>データ ウェアハウスの一時停止の例
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

## <a name="start-data-warehouse-example"></a>データ ウェアハウスの開始の例

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
これらの PowerShell コマンドレットは、Azure Synapse Analytics データ ウェアハウスでサポートされています。

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

* [PowerShell を使用してデータ ウェアハウスを作成する](create-data-warehouse-powershell.md)
* [データベースの復元](sql-data-warehouse-restore-points.md)

PowerShell で自動化できるその他のタスクについては、[Azure SQL Database コマンドレット](https://docs.microsoft.com/powershell/module/az.sql)に関するページを参照してください。 Azure Synapse Analytics データ ウェアハウスでは、すべての Azure SQL Database コマンドレットがサポートされているわけではありません。  REST で自動化できるタスクの一覧については、「[Azure SQL Database の操作](/rest/api/sql/)」を参照してください。
