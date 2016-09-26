<properties
	pageTitle="PowerShell を使用した Azure SQL Database の管理 | Microsoft Azure"
	description="PowerShell による Azure SQL Database 管理。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="sstein"/>

# PowerShell を使用した Azure SQL Database の管理


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

このトピックで説明する PowerShell コマンドレットを使用すると、Azure SQL Database タスクの多くを実行できます。完全な一覧については、「[Azure SQL Database Cmdlets (Azure SQL Database コマンドレット)](https://msdn.microsoft.com/library/mt574084.aspx)」を参照してください。


## リソース グループの作成

SQL Database および関連する Azure リソースのリソース グループは、[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx) コマンドレットを使用して作成します。

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

詳細については、[リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)をご覧ください。サンプル スクリプトについては、「[SQL データベースの PowerShell スクリプト作成](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)」を参照してください。

## SQL Database サーバーの作成

[New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) コマンドレットを使用して、SQL Database サーバーを作成します。*server1* を、ご利用のサーバー名に置き換えます。サーバー名は、すべての Azure SQL Database サーバーで一意であることが必要です。サーバー名を既に取得している場合は、エラーが表示されます。このコマンドは、完了までに数分かかる場合があります。サブスクリプションにリソース グループが既に存在している必要があります。

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

詳細については、[SQL Database の概要](sql-database-technical-overview.md)に関するページをご覧ください。サンプル スクリプトについては、「[SQL データベースの PowerShell スクリプト作成](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)」を参照してください。


## SQL Database サーバー ファイアウォール規則の作成

サーバーにアクセスするためのファイアウォール規則は、[New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) コマンドレットを使用して作成します。次のコマンドを、開始 IP アドレスと終了 IP アドレスをご利用のクライアントの有効な値に置き換えて実行します。サブスクリプションにリソース グループとサーバーが既に存在している必要があります。

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

サーバーへの他の Azure サービスのアクセスを許可するには、ファイアウォール規則を作成し、`-StartIpAddress` と `-EndIpAddress` の両方を **0.0.0.0** に設定します。この特別なファイアウォール規則により、すべての Azure トラフィックが、サーバーにアクセスできるようになります。

詳細については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」をご覧ください。サンプル スクリプトについては、「[SQL データベースの PowerShell スクリプト作成](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)」を参照してください。


## SQL データベースの作成 (空白)

[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) コマンドレットを使用して、データベースを作成します。サブスクリプションにリソース グループとサーバーが既に存在している必要があります。

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

詳細については、[SQL Database の概要](sql-database-technical-overview.md)に関するページをご覧ください。サンプル スクリプトについては、「[SQL データベースの PowerShell スクリプト作成](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)」を参照してください。


## SQL Database のパフォーマンス レベルを変更します

[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) コマンドレットを使用して、データベースをスケールアップまたはスケールダウンします。サブスクリプションにリソース グループ、サーバー、およびデータベースが既に存在している必要があります。Basic レベルについては、(次のスニペットのように) `-RequestedServiceObjectiveName` を単一スペースに設定します。他のレベルについては、前の例のように、*S0*、*S1*、*P1*、*P6* などに設定します。

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

詳細については、「[SQL Database のオプションとパフォーマンス: 各サービス階層で使用できる内容について理解します](sql-database-service-tiers.md)」を参照してください。サンプル スクリプトについては、「[SQL Database のサービス階層とパフォーマンス レベルを変更するサンプル PowerShell スクリプト](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database)」を参照してください。

## SQL データベースを同じサーバーにコピーする

[New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644.aspx) コマンドレットを使用して、SQL データベースを同じサーバーにコピーします。`-CopyServerName` と `-CopyResourceGroupName` を、ソース データベース サーバーおよびリソース グループと同じ値に設定します。

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

詳細については、「[Azure SQL Database のコピー](sql-database-copy.md)」を参照してください。サンプル スクリプトについては、[SQL データベースの PowerShell スクリプトのコピー](sql-database-copy-powershell.md#example-powershell-script)に関するページをご覧ください。


## SQL Database の削除

[Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) コマンドレットを使用して、SQL データベースを削除します。サブスクリプションにリソース グループ、サーバー、およびデータベースが既に存在している必要があります。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## SQL Database サーバーの削除

[Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) コマンドレットを使用して、サーバーを削除します。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## PowerShell を使用したエラスティック データベース プールの作成と管理

PowerShell を使用したエラスティック データベース プール作成の詳細については、「[PowerShell による新しい Elastic Database プールの作成](sql-database-elastic-pool-create-powershell.md)」を参照してください。

PowerShell を使用したエラスティック データベース プール管理の詳細については、「[PowerShell でのエラスティック データベース プールの監視と管理](sql-database-elastic-pool-manage-powershell.md)」を参照してください。



## 関連情報

- [Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn708514.aspx)

<!---HONumber=AcomDC_0914_2016-->