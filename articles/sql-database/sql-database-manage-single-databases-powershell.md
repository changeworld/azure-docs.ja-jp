---
title: "PowerShell: 単一の Azure SQL データベースの作成と管理 | Microsoft Docs"
description: "Azure Portal を使用して単一の Azure SQL データベースを作成および管理する方法のクイック リファレンス"
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
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>PowerShell を使用した単一の Azure SQL データベースの作成と管理

[Azure Portal](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API、または C# を使用して、単一の Azure SQL データベースを作成および管理できます。 このトピックでは、PowerShell を使用した場合の方法について説明します。 Azure Portal を使用した場合の方法については、[Azure Portal を使用した単一のデータベースの管理と作成](sql-database-manage-single-databases-powershell.md)に関するページを参照してください。 Transact-SQL を使用した場合の方法については、[Transact-SQL を使用した単一のデータベースの作成と管理](sql-database-manage-single-databases-tsql.md)に関するページを参照してください。 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

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

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>単一データベースのサービス レベルとパフォーマンス レベルの変更

[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) コマンドレットを実行し、**-RequestedServiceObjectiveName** を目的の価格レベルのパフォーマンス レベルに設定します (*S0*、*S1*、*S2*、*S3*、*P1*、*P2* など)。

```{variables}``` は独自の値に置き換えてください (中かっこは不要です)。

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>次のステップ
* 管理ツールの概要については、[管理ツールの概要](sql-database-manage-overview.md)に関するページをご覧ください。
* Azure Portal を使用して管理タスクを実行する方法については、「[Azure ポータルを使用した Azure SQL データベースの管理](sql-database-manage-portal.md)」をご覧ください。
* PowerShell を使用して管理タスクを実行する方法については、「[PowerShell を使用した Azure SQL Database の管理](sql-database-manage-powershell.md)」をご覧ください。
* SQL Server Management Studio を使用して管理タスクを実行する方法については、「[SQL Server Management Studio を使用した Azure SQL データベースの管理](sql-database-manage-azure-ssms.md)」をご覧ください。
* SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」をご覧ください。 
* Azure Database のサーバーとデータベースの機能については、[機能](sql-database-features.md)に関するページをご覧ください。



<!--HONumber=Feb17_HO2-->


