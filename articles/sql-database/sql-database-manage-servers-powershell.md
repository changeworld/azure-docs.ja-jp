---
title: "PowerShell: Azure SQL Database サーバーの作成と管理 | Microsoft Docs"
description: "PowerShell を使用して Azure SQL Database サーバーを作成および管理する方法のクイック リファレンス。"
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
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 144774c9106bf5a0e389c99075c822d1c5282692
ms.openlocfilehash: c22243db06c783bc4f5b311aa4d49ce12032ab02


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>PowerShell を使用した Azure SQL Database サーバーの作成と管理

[Azure Portal](https://portal.azure.com/)、PowerShell、REST API、または C＃を使用して、Azure SQL Database サーバーを作成および管理できます。 このトピックでは、PowerShell を使用した場合の方法について説明します。 Azure Portal を使用した場合の方法については、[Azure Portal を使用したサーバーの管理と作成](sql-database-manage-servers-portal.md)に関するページを参照してください。 

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
> サンプル スクリプトについては、[PowerShell スクリプトを使用した SQL データベースの作成](sql-database-get-started-powershell.md)に関するページを参照してください。
>

## <a name="next-steps"></a>次のステップ
* 管理ツールの概要については、[管理ツールの概要](sql-database-manage-overview.md)に関するページをご覧ください。
* Azure Portal を使用して管理タスクを実行する方法については、「[Azure ポータルを使用した Azure SQL データベースの管理](sql-database-manage-portal.md)」をご覧ください。
* PowerShell を使用して管理タスクを実行する方法については、「[PowerShell を使用した Azure SQL Database の管理](sql-database-manage-powershell.md)」をご覧ください。
* SQL Server Management Studio を使用してその他のタスクを実行する方法については、「[SQL Server Management Studio を使用した Azure SQL データベースの管理](sql-database-manage-azure-ssms.md)」をご覧ください。
* SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」をご覧ください。 
* Azure Database のサーバーとデータベースの機能については、[機能](sql-database-features.md)に関するページをご覧ください。



<!--HONumber=Feb17_HO3-->


