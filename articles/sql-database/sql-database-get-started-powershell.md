---
title: "Powershell: Azure SQL Database の概要 | Microsoft Docs"
description: "PowerShell を使用して、SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、およびデータベースを作成する方法について説明します。 また、PowerShell を使用してデータベースに対してクエリを実行する方法についても説明します。"
keywords: "新しい SQL Database の作成、データベースの設定"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 02/23/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 78d9194f50bcdc4b0db7871f2480f59b26cfa8f6
ms.openlocfilehash: 7b7273edfa33f297cb5dc30ef380b6a737787b33
ms.lasthandoff: 02/27/2017


---

# <a name="tutorial-provision-and-access-an-azure-sql-database-using-powershell"></a>チュートリアル: PowerShell を使用した Azure SQL データベースのプロビジョニングとアクセス

この入門用チュートリアルでは、PowerShell を使用して次のことを行う方法を学習します。

* 新しい Azure リソース グループを作成する
* Azure SQL 論理サーバーを作成する
* Azure SQL サーバーのプロパティを表示する
* サーバーレベルのファイアウォール規則を作成する
* AdventureWorksLT サンプル データベースを単一データベースとして作成する
* AdventureWorksLT サンプル データベースのプロパティを表示する
* 空の単一データベースを作成する

このチュートリアルでは、次のことも行います。

* 論理サーバーとその master データベースに接続する
* master データベースのプロパティを表示する
* サンプル データベースに接続する
* ユーザー データベースのプロパティを表示する

このチュートリアルを完了すると、サンプル データベースと空のデータベースが Azure リソース グループで実行され、論理サーバーに接続された状態になります。 また、サーバーレベルのプリンシパルが指定の IP アドレス (または IP アドレス範囲) からサーバーにログインできるようにサーバーレベルのファイアウォール規則が構成されます。 

**推定所要時間**: このチュートリアルには約 30 分かかります (既に前提条件を満たしていることが前提です)。


> [!TIP]
> これらのタスクは、入門用チュートリアルで [Azure Portal](sql-database-get-started.md) を使用して実行することができます。
>


## <a name="prerequisites"></a>前提条件

* Azure アカウントが必要です。 [無料の Azure アカウントを作成する](https://azure.microsoft.com/free/)か、[Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)ことができます。 

* サブスクリプションの所有者または共同作成者ロールのメンバーであるアカウントを使用してサインインしていることが必要です。 ロールベースのアクセス制御 (RBAC) の詳細については、「[Azure Portal でのアクセス管理の概要](../active-directory/role-based-access-control-what-is.md)」を参照してください。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Azure PowerShell を使用して新しい論理 SQL サーバーを作成する

サーバーを含めるためのリソース グループが必要です。そのため、最初に、新しいリソース グループとサーバーを作成する ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup)、[New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)) か、既存のリソース グループとサーバーへの参照を取得します ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup)、[Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver))。


次のスニペットでは、リソース グループと Azure SQL サーバーがまだ存在しない場合に作成します。

有効な Azure の場所と文字列形式の一覧については、以下の「[ヘルパーのスニペット](#helper-snippets)」セクションを参照してください。
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>Azure PowerShell を使用して論理 SQL サーバーのプロパティを表示する

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Azure PowerShell を使用してサーバーレベルのファイアウォール規則を作成する

ファイアウォール規則を設定するには、パブリック IP アドレスを知っている必要があります。 自分の IP アドレスは、任意のブラウザーを使用して取得できます ("what is my IP address" を検索します)。 詳細については、[ファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。

次に、[Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) コマンドレットと [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule) コマンドレットを使用して、参照を取得するか新しい規則を作成します。 このスニペットでは、規則が既に存在する場合は、その規則への参照を取得するだけで、開始および終了 IP アドレスを更新することはありません。 作成または更新機能に [Set-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule) を使用するように **else** 句をいつでも変更できます。

> [!NOTE] 
> サーバー上の SQL Database ファイアウォールは、1 つの IP アドレスに対して開くことも、範囲全体のアドレスに対して開くこともできます。 ファイアウォールを開くと、SQL の管理者とユーザーは、有効な資格情報を持っているサーバー上の任意のデータベースにログインできます。
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule

# Allow Azure services to access the server
$serverFirewallRuleName2 = "allowAzureServices"
$serverFirewallStartIp2 = "0.0.0.0"
$serverFirewallEndIp2 = "0.0.0.0"

$myFirewallRule2 = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName2 -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule2)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName2"
   $myFirewallRule2 = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName2 -StartIpAddress $serverFirewallStartIp2 -EndIpAddress $serverFirewallEndIp2
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName2 already exists:"
}
$myFirewallRule2

```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Azure PowerShell を使用して SQL サーバーに接続する

master データベースに対して簡単なクエリを実行して、サーバーに接続できるかどうかを確認します。 次のスニペットでは、[.NET Framework Provider for SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) を使用して、サーバーの master データベースに接続してクエリを実行します。 これにより、前のスニペットで使用した変数に基づいた接続文字列が作成されます。 プレースホルダーは、前の手順でサーバーの作成に使用した SQL サーバーの管理者とパスワードに置き換えてください。


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Azure PowerShell を使用して新しい AdventureWorksLT サンプル データベースを作成する

次のスニペットでは、[New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport) コマンドレットを使用して、AdventureWorksLT サンプル データベースの bacpac をインポートします。 bacpac は、公開されている読み取り専用の Azure BLOB ストレージ アカウントにあります。 インポートのコマンドレットを実行した後、[Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus) コマンドレットを使用してインポート操作の進行状況を監視できます。


```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "SharedAccessKey"
$storageUri = "https://sqldbtutorial.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac"
$storageKey = "?"

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Azure PowerShell を使用してデータベースのプロパティを表示する

データベースを作成したら、そのプロパティの一部を表示します。

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Azure PowerShell を使用してサンプル データベースに接続してクエリを実行する

AdventureWorksLT データベースに対して簡単なクエリを実行して、接続できるかどうかを確認します。 次のスニペットでは、[.NET Framework Provider for SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) を使用して、データベースに接続してクエリを実行します。 これにより、前のスニペットで使用した変数に基づいた接続文字列が作成されます。 プレースホルダーを SQL サーバー管理パスワードに置き換えてください。

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Azure PowerShell を使用して新しい空のデータベースを作成する

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>サーバー、ファイアウォール規則、データベースを作成するための完全な Azure PowerShell スクリプト



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"


# Storage account details for locating
# and accessing the sample .bacpac 
# Do Not Edit for this tutorial
$myStorageKeyType = "SharedAccessKey"
$myStorageUri = "https://sqldbtutorial.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac"
$myStorageKey = "?"



# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin

# Create or update server firewall rules
########################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule

# Allows Azure services to access the server
$serverFirewallRuleName2 = "allowAzureServices"
$serverFirewallStartIp2 = "0.0.0.0"
$serverFirewallEndIp2 = "0.0.0.0"

$myFirewallRule2 = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName2 -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule2)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName2"
   $myFirewallRule2 = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName2 -StartIpAddress $serverFirewallStartIp2 -EndIpAddress $serverFirewallEndIp2
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName2 already exists:"
}
$myFirewallRule2


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> 使用していないデータベースを削除することで、学習中のコストを節約できます。 Basic エディションのデータベースの場合、7 日以内であればデータベースを復元できます。 ただし、サーバーを削除しないでください。 サーバーを削除した場合、サーバーも、そのサーバーの削除されたデータベースすべても復旧できません。

## <a name="helper-snippets"></a>ヘルパーのスニペット

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> 使用していないデータベースを削除することで、学習中のコストを節約できます。 Basic エディションのデータベースの場合、7 日以内であればデータベースを復元できます。 ただし、サーバーを削除しないでください。 サーバーを削除した場合、サーバーも、そのサーバーの削除されたデータベースすべても復旧できません。
>

## <a name="next-steps"></a>次のステップ
これで、この最初の入門用チュートリアルは完了です。サンプル データを含むデータベースを作成しました。このチュートリアルで学習した内容に基づいたチュートリアルが多数あるため、探してみてください。 

- SQL Server 認証のチュートリアルを開始するには、[SQL の認証と承認](sql-database-control-access-sql-authentication-get-started.md)に関するページを参照してください。
- Azure Active Directory 認証のチュートリアルを開始するには、[Azure AD の認証と承認](sql-database-control-access-aad-authentication-get-started.md)に関するページを参照してください。
* Azure Portal でサンプル データベースに対してクエリを実行する場合は、「[Public preview: Interactive query experience for SQL databases (パブリック プレビュー: SQL データベース用の対話型クエリ機能)](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/)」を参照してください。
* Excel に詳しい場合は、 [Excel を使用した Azure SQL データベースへの接続](sql-database-connect-excel.md)方法を参照してください。
* コーディングを開始する準備ができている場合、「 [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)」でプログラミング言語を選択します。
* オンプレミスの SQL Server データベースを Azure に移行する場合は、[SQL Database へのデータベースの移行](sql-database-cloud-migrate.md)に関する記事を参照してください。
* BCP コマンド ライン ツールを使用して CSV ファイルから新しいテーブルにデータを読み込む場合は、 [BCP を使用した CSV ファイルから SQL Database へのデータの読み込み](sql-database-load-from-csv-with-bcp.md)に関するページを参照してください。
* テーブルやその他のオブジェクトの作成を開始する場合は、「[テーブルの作成](https://msdn.microsoft.com/library/ms365315.aspx)」の「テーブルを作成するには」を参照してください。

## <a name="additional-resources"></a>その他のリソース
[SQL Database とは](sql-database-technical-overview.md)

