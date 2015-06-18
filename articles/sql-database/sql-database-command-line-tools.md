<properties 
	pageTitle="PowerShell を使用した Azure SQL Database リソースの管理" 
	description="コマンド ラインを使用した Azure SQL Database の管理" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# PowerShell を使用した Azure SQL Database リソースの管理


このトピックでは、PowerShell スクリプトを使用して、Azure SQL Database 論理サーバー、データベース、ファイアウォール ルールを作成します。

## 手順 1: Azure SDK をインストールします

まだインストールしていない場合は、「[Azure PowerShell のインストールと構成方法](../powershell-install-configure.md)」の手順に従って、Azure PowerShell をご使用のローカル コンピューターにインストールします。次に、Azure PowerShell コマンド プロンプトを開きます。


## 手順 2: PowerShell スクリプトを構成します
この PowerShell スクリプトでは、サーバー、データベース、サーバーのファイアウォール ルールを作成します。


1. 次の PowerShell コマンドレットのブロックをテキスト エディターにコピーします。
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. < > 内のすべてを、必要な値で置き換えます。有効な Azure SQL Database サーバーの場所の一覧については、Azure Powershell コマンド プロンプトの次のコマンドレットを実行します。

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##手順 3: PowerShell スクリプトを実行する

組み合わせた Azure PowerShell コマンドレットを確認します。

手順 2 で構成した PowerShell コマンドレットをコピーし、これを Azure PowerShell のコマンド プロンプトに貼り付けます。これにより、一連の PowerShell コマンドがコマンドレットとして発行され、Azure SQL server、データベース、サーバーのファイアウォール ルールが作成されます。

これらの Azure SQL リソース再び作成するか、同様のリソースを作成する場合は、次のことができます。

- これを PowerShell スクリプト ファイル (*.ps1) として保存します
- Azure 管理ポータルの [オートメーション] セクションに、これを Azure の Automation Runbook として保存する。 

##例

この PowerShell スクリプトで米国西部にリソースを作成します。

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##リソース

Azure SQL PowerShell コマンドレットの詳細については、[ここをクリックしてください](https://msdn.microsoft.com/library/dn546726.aspx)

<!---HONumber=58--> 