---
title: "Azure SQL データベースの作成 | Microsoft Docs"
description: "Azure Portal、PowerShell、Transact-SQL を使用して Azure SQL データベースを作成する方法のクイック リファレンス。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 11/14/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: aca33871d83a34769fd2b09be4b382c872a65f0a


---
# <a name="create-an-azure-sql-database"></a>Azure SQL データベースの作成

[Azure Portal](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API または C# を使用して、Azure SQL データベースを作成できます。 

## <a name="create-an-azure-sql-database-using-the-azure-portal"></a>Azure Portal を使用して Azure SQL データベースを作成する

1. [Azure Portal](https://portal.azure.com/) で **[SQL データベース]** ブレードを開きます。 

    ![SQL データベース](./media/sql-database-get-started/sql-databases.png)
2. [SQL データベース] ブレードで、**[追加]** をクリックします。

    ![SQL データベースの追加](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Azure Portal と SQL Server Management Studio の入門用チュートリアルについては、「[Azure Portal と SQL Server Management Studio を使用して Azure SQL Database のサーバー、データベース、ファイアウォール規則を使ってみる](sql-database-get-started.md)」をご覧ください。
>

## <a name="create-an-azure-sql-database-using-powershell"></a>PowerShell を使用して Azure SQL データベースを作成する

SQL Database を作成するには、[New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase) コマンドレットを使用します。 サブスクリプションにリソース グループとサーバーが既に存在している必要があります。 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```
> [!TIP]
> サンプル スクリプトについては、「 [SQL データベースの PowerShell スクリプト作成](sql-database-get-started-powershell.md)」を参照してください。
>

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>SQL Server Management Studio で Transact-SQL を使用して Azure SQL データベースを作成する

SQL Server Management Studio で Transact-SQL を使用して SQL Database を作成するには:

1. SQL Server Management Studio で、サーバー レベルのプリンシパル ログインまたは **dbmanager** ロールのメンバーのログインを使用して、Azure Database サーバーに接続します。 詳細については、[ログインの管理](sql-database-manage-logins.md)に関するページをご覧ください。
2. オブジェクト エクスプローラーで [データベース] ノードを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。
3. データベースを作成するには、 **CREATE DATABASE** ステートメントを使用します。 詳細については、「 [CREATE DATABASE (Azure SQL データベース)](https://msdn.microsoft.com/library/dn268335.aspx)」を参照してください。 次のステートメントは、 **myTestDB** という名前のデータベースを作成し、既定の最大サイズが 250 GB の Standard S0 エディションのデータベースとして指定します。
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. **[実行]** をクリックしてクエリを実行します。
5. オブジェクト エクスプローラーで [データベース] ノードを右クリックし、**[更新]** をクリックして新しいデータベースを表示します。 

> [!TIP]
> Azure Portal と SQL Server Management Studio の入門用チュートリアルについては、「[Azure Portal と SQL Server Management Studio を使用して Azure SQL Database のサーバー、データベース、ファイアウォール規則を使ってみる](sql-database-get-started.md)」をご覧ください。
>

## <a name="additional-resources"></a>その他のリソース
* 管理ツールの概要については、[管理ツールの概要](sql-database-manage-overview.md)に関するページをご覧ください。
* Azure Portal を使用して管理タスクを実行する方法については、「[Azure ポータルを使用した Azure SQL データベースの管理](sql-database-manage-portal.md)」をご覧ください。
* PowerShell を使用して管理タスクを実行する方法については、「[PowerShell を使用した Azure SQL Database の管理](sql-database-manage-powershell.md)」をご覧ください。
* SQL Server Management Studio を使用して管理タスクを実行する方法については、「[SQL Server Management Studio を使用した Azure SQL データベースの管理](sql-database-manage-azure-ssms.md)」をご覧ください。
* SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」をご覧ください。 
* Azure Database のサーバーとデータベースの機能については、「[Azure SQL Database の機能](sql-database-features.md)」をご覧ください。



<!--HONumber=Dec16_HO3-->


