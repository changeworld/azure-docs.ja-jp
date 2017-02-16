---
title: "Azure SQL Database サーバーの作成 | Microsoft Docs"
description: "Azure Portal と PowerShell を使用して Azure SQL Database サーバーを作成する方法のクイック リファレンス。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: e8fe805da2446895b6616926fe1326dc30b9d0d0


---

# <a name="create-azure-sql-database-servers"></a>Azure SQL Database サーバーの作成

[Azure Portal](https://portal.azure.com/)、PowerShell、REST API、または C# 使用して、Azure SQL Database サーバーを作成できます。 

## <a name="create-an-azure-sql-database-server-using-the-azure-portal"></a>Azure Portal を使用して Azure SQL Database サーバーを作成する

1. [Azure Portal](https://portal.azure.com/) で **[SQL Server]** ブレードを開きます。 

    ![SQL Server](./media/sql-database-get-started/new-sql-server.png)

2. **[追加]** をクリックして SQL Server を作成します。

    ![新しい SQL Server の追加](./media/sql-database-get-started/new-sql-server-add.png)

> [!TIP]
> Azure Portal と SQL Server Management Studio の入門用チュートリアルについては、「[Azure Portal と SQL Server Management Studio を使用して Azure SQL Database のサーバー、データベース、ファイアウォール規則を使ってみる](sql-database-get-started.md)」をご覧ください。
>

## <a name="create-an-azure-sql-database-server-using-powershell"></a>PowerShell を使用して Azure SQL Database サーバーを作成する

SQL Database サーバーを作成するには、[New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver) コマンドレットを使用します。 *server1* を、ご利用のサーバー名に置き換えます。 サーバー名は、すべての Azure SQL Database サーバーで一意であることが必要です。 サーバー名を既に取得している場合は、エラーが表示されます。 このコマンドは、完了までに数分かかる場合があります。 サブスクリプションにリソース グループが既に存在している必要があります。

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

> [!TIP]
> サンプル スクリプトについては、「 [SQL データベースの PowerShell スクリプト作成](sql-database-get-started-powershell.md)」を参照してください。
>

## <a name="additional-resources"></a>その他のリソース
* 管理ツールの概要については、[管理ツールの概要](sql-database-manage-overview.md)に関するページをご覧ください。
* Azure Portal を使用して管理タスクを実行する方法については、「[Azure ポータルを使用した Azure SQL データベースの管理](sql-database-manage-portal.md)」をご覧ください。
* PowerShell を使用して管理タスクを実行する方法については、「[PowerShell を使用した Azure SQL Database の管理](sql-database-manage-powershell.md)」をご覧ください。
* SQL Server Management Studio を使用してその他のタスクを実行する方法については、「[SQL Server Management Studio を使用した Azure SQL データベースの管理](sql-database-manage-azure-ssms.md)」をご覧ください。
* SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」をご覧ください。 
* Azure Database のサーバーとデータベースの機能については、「[Azure SQL Database の機能](sql-database-features.md)」をご覧ください。



<!--HONumber=Dec16_HO3-->


