---
title: PowerShell を使用した新しい SQL Database の設定 | Microsoft Docs
description: PowerShell で SQL データベースを作成する方法について説明します。PowerShell コマンドレットを使用して、一般的なデータベース設定タスクを管理できます。
keywords: 新しい SQL Database の作成、データベースの設定
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: cgronlun

ms.service: sql-database
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/19/2016
ms.author: sstein

---
# PowerShell コマンドレットを使用して SQL データベースを作成し、一般的なデータベース設定タスクを実行する
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-get-started.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> * [C#](sql-database-get-started-csharp.md)
> 
> 

PowerShell コマンドレットを使用して SQL データベースを作成する方法について説明します。(Elastic Database の作成については、「[PowerShell による新しい Elastic Database プールの作成](sql-database-elastic-pool-create-powershell.md)」を参照してください。)

[!INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]

## データベースの設定: リソース グループ、サーバー、ファイアウォール規則を作成する
これで、選択した Azure サブスクリプションに対してコマンドレットを実行する準備ができたので、次にデータベースを作成するサーバーを含むリソース グループを確立します。次のコマンドを編集して選択した任意の有効な場所で使用できます。**(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location** を実行して有効な場所の一覧を取得します。

次のコマンドを実行して、リソース グループを作成します。

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### サーバーの作成
SQL データベースは Azure SQL Database サーバーの内部で作成されます。**New-AzureRmSqlServer** を実行してサーバーを作成します。サーバーの名前は、すべての Azure SQL Database サーバーに対して一意であることが必要です。サーバー名を既に取得している場合は、エラーが表示されます。このコマンドは完了するまでに数分かかる場合があることに注意してください。コマンドを編集して有効な場所を選択して使用できますが、前の手順でのリソース グループ作成時に使用した場所と同じでなければなりません。

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

このコマンドを実行すると、ユーザー名とパスワードの入力を求められます。Azure の資格情報は入力しないでください。代わりに、サーバー管理者として作成するユーザー名とパスワードを入力します。この記事の末尾のスクリプトは、コードでサーバー資格情報を設定する方法を示しています。

サーバーが正常に作成されると、サーバーの詳細が表示されます。

### サーバーのファイアウォール規則を構成し、サーバーへのアクセスを許可します。
サーバーにアクセスするには、ファイアウォール規則を確立する必要があります。ご利用のコンピューターで有効な開始 IP アドレスと終了 IP アドレスに置き換え、次のコマンドを実行します。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

規則が正常に作成されると、ファイアウォール規則の詳細が表示されます。

他の Azure サービスによるサーバーへのアクセスを許可するには、ファイアウォール規則を追加し、StartIpAddress と EndIpAddress を 0.0.0.0 に設定します。この規則は、すべての Azure サブスクリプションからの Azure トラフィックに、サーバーへのアクセスを許可します。

詳細については、「[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」をご覧ください。

## SQL Database の作成
これでリソース グループ、サーバー、ファイアウォール規則が構成され、サーバーにアクセスできるようになりました。

次のコマンドは、S1 パフォーマンス レベル、Standard サービス レベルで (空の) SQL データベースを作成します。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


データベースが正常に作成されると、データベースの詳細が表示されます。

## SQL データベースの PowerShell スクリプト作成
次の PowerShell スクリプトは、SQL データベースとそのすべての依存リソースを作成します。すべての `{variables}` を、自分のサブスクリプションおよびリソースに固有の値に置き換えます (値を設定するときに、**{}** は削除します)。

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation

    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"

    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds

    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip

    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp


    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"

    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo


    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## 次のステップ
SQL データベースを作成し、基本的なデータベース設定タスクを実行したら、次の手順を実行します。

* [PowerShell を使用して SQL Database を管理する](sql-database-manage-powershell.md)
* [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)

## その他のリソース
* [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1005_2016-->