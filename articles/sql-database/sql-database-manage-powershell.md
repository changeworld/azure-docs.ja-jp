---
title: "PowerShell を使用した Azure SQL Database の管理 | Microsoft Docs"
description: "PowerShell による Azure SQL Database 管理。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: adad6b8e27e0996559d5e6dacb8dd60fbf52a631
ms.openlocfilehash: 0c1ce1c29e447d9db4ef0df7873ef89cb835abee


---
# <a name="managing-azure-sql-database-using-powershell"></a>PowerShell を使用した Azure SQL Database の管理
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-manage-portal.md)
> * [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

このトピックで説明する PowerShell コマンドレットを使用すると、Azure SQL Database タスクの多くを実行できます。 完全な一覧については、「 [Azure SQL Database Cmdlets](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)」(Azure SQL Database コマンドレット) をご覧ください。

> [!TIP]
> サーバーの作成、サーバーベースのファイアウォールの作成、サーバー プロパティの表示、master データベースに対する接続とクエリ実行、サンプル データベースと空のデータベースの作成、データベース プロパティに対するクエリ実行、およびサンプル データベースに対する接続とクエリ実行の方法を示すチュートリアルについては、[入門用チュートリアル](sql-database-get-started-powershell.md)を参照してください。
>

## <a name="how-do-i-create-a-resource-group"></a>リソース グループの作成方法
SQL Database および関連する Azure リソースのリソース グループは、[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx) コマンドレットを使用して作成します。

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

詳細については、 [リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)をご覧ください。
完全なチュートリアルについては、「[Azure PowerShell を使用して Azure SQL Database のサーバー、データベース、ファイアウォール規則を使ってみる](sql-database-get-started-powershell.md)」を参照してください。

## <a name="how-do-i-create-a-sql-database-server"></a>SQL Database サーバーの作成方法
SQL Database サーバーを作成するには、[New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx) コマンドレットを使用します。 *server1* を、ご利用のサーバー名に置き換えます。 サーバー名は、すべての Azure SQL Database サーバーで一意であることが必要です。 サーバー名を既に取得している場合は、エラーが表示されます。 このコマンドは、完了までに数分かかる場合があります。 サブスクリプションにリソース グループが既に存在している必要があります。

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

サーバーの詳細については、[SQL Database の機能](sql-database-features.md)に関する記事を参照してください。 完全なチュートリアルについては、「[Azure PowerShell を使用して Azure SQL Database のサーバー、データベース、ファイアウォール規則を使ってみる](sql-database-get-started-powershell.md)」を参照してください。

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>SQL Database サーバー ファイアウォール規則の作成方法
サーバーにアクセスするためのファイアウォール規則を作成するには、[New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) コマンドレットを使用します。 次のコマンドを、開始 IP アドレスと終了 IP アドレスをご利用のクライアントの有効な値に置き換えて実行します。 サブスクリプションにリソース グループとサーバーが既に存在している必要があります。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

サーバーへの他の Azure サービスのアクセスを許可するには、ファイアウォール規則を作成し、`-StartIpAddress` と `-EndIpAddress` の両方を **0.0.0.0** に設定します。 この特別なファイアウォール規則により、すべての Azure トラフィックが、サーバーにアクセスできるようになります。

詳細については、「 [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」をご覧ください。 完全なチュートリアルについては、「[Azure PowerShell を使用して Azure SQL Database のサーバー、データベース、ファイアウォール規則を使ってみる](sql-database-get-started-powershell.md)」を参照してください。

## <a name="how-do-i-create-a-sql-database"></a>SQL Database の作成方法
SQL Database を作成するには、[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) コマンドレットを使用します。 サブスクリプションにリソース グループとサーバーが既に存在している必要があります。 

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

詳細については、 [SQL Database の概要](sql-database-technical-overview.md)に関するページを参照してください。 完全なチュートリアルについては、「[Azure PowerShell を使用して Azure SQL Database のサーバー、データベース、ファイアウォール規則を使ってみる](sql-database-get-started-powershell.md)」を参照してください。

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>SQL Database のパフォーマンス レベルの変更方法
パフォーマンス レベルを変更するには、[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) コマンドレットを使用して、データベースをスケールアップまたはスケールダウンします。 サブスクリプションにリソース グループ、サーバー、およびデータベースが既に存在している必要があります。 Basic レベルについては、(次のスニペットのように) `-RequestedServiceObjectiveName` を単一スペースに設定します。 他のレベルについては、前の例のように、*S0*、*S1*、*P1*、*P6* などに設定します。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

詳細については、「 [SQL Database のオプションとパフォーマンス: 各サービス階層で使用できる内容について理解します](sql-database-service-tiers.md)」を参照してください。 サンプル スクリプトについては、「 [SQL Database のサービス階層とパフォーマンス レベルを変更するサンプル PowerShell スクリプト](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database)」を参照してください。

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>SQL Database を同じサーバーにコピーする方法
SQL Database を同じサーバーにコピーするには、[New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx) コマンドレットを使用します。 `-CopyServerName` と `-CopyResourceGroupName` を、ソース データベース サーバーおよびリソース グループと同じ値に設定します。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

詳細については、「 [Azure SQL Database のコピー](sql-database-copy.md)」を参照してください。 サンプル スクリプトについては、 [SQL データベースの PowerShell スクリプトのコピー](sql-database-copy-powershell.md#example-powershell-script)に関するページをご覧ください。

## <a name="how-do-i-delete-a-sql-database"></a>SQL データベースを削除する方法
SQL Database を削除するには、 [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx) コマンドレットを使用します。 サブスクリプションにリソース グループ、サーバー、およびデータベースが既に存在している必要があります。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>SQL Database の削除方法
SQL Database サーバーを削除するには、[Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx) コマンドレットを使用します。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>PowerShell を使用したエラスティック プールの作成と管理の方法
PowerShell を使用したエラスティック プール作成の詳細については、「[PowerShell による新しいエラスティック プールの作成](sql-database-elastic-pool-create-powershell.md)」を参照してください。

PowerShell を使用したエラスティック プールの管理の詳細については、「[PowerShell でのエラスティック プールの監視と管理](sql-database-elastic-pool-manage-powershell.md)」を参照してください。

## <a name="related-information"></a>関連情報
* [Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Dec16_HO3-->


