<properties 
    pageTitle="PowerShell を使用した Azure SQL Database の作成"
	description="PowerShell を使用した Azure SQL Database の作成"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
    ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="data-management"
	ms.date="09/01/2015"
	ms.author="sstein"/>

# PowerShell を使用した SQL データベースの作成

**シングル データベース**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


## 概要

この記事では PowerShell を使用して、SQL Database を作成する方法を説明します。


この記事を完了するには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料評価版**をクリックしてからこの記事に戻り、最後まで完了してください。
- Azure PowerShell。Azure PowerShell モジュールは、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行してダウンロードおよびインストールすることができます。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」をご覧ください。

Azure SQL Database の作成と管理に使うコマンドレットは、Azure リソース マネージャー モジュールにあります。Azure PowerShell を開始するときに、Azure モジュールのコマンドレットが既定でインポートされます。Azure リソース マネージャー モジュールに切り替えるには、Switch-AzureMode コマンドレットを使用します。

	Switch-AzureMode -Name AzureResourceManager

**Switch-AzureMode** を実行して、警告*「Switch-AzureMode コマンドレットは廃止予定で、今後のリリースで削除される予定です。」*が出た場合は問題ありません。次の手順に進み、資格情報の構成を行ってください。

詳細については、「[リソース マネージャーでの Windows PowerShell の使用](powershell-azure-resource-manager.md)」をご覧ください。


## 資格情報を構成してサブスクリプションを選択

これで、Azure リソース マネージャー モジュールが実行され、SQL データベースを作成するために必要なすべてのコマンドレットにアクセスできるようになりました。

まず、Azure アカウントへのアクセスを確立させるため、以下のコマンドレットを実行してください。資格情報を入力するサインイン画面が表示されます。Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

	Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


### Azure サブスクリプションを選択します。

サブスクリプションを選択するには、サブスクリプション ID が必要です。前の手順からコピーするか、複数のサブスクリプションがある場合は **Get-AzureSubscription** コマンドレットを実行して結果セットから目的のサブスクリプション情報をコピーできます。ご利用のサブスクリプションを取得したら次のコマンドレットを実行します。

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

**Select-AzureSubscription** を正常に実行すると、PowerShell プロンプトに戻ります。実行できるサブクスリプションが 1 つ以上ある場合は、**Get-AzureSubscription** を実行して、使用するサブスクリプションが **IsCurrent: True** と表示されていることを確認してください。

## リソース グループ、サーバー、ファイアウォール規則の作成

これで選択した Azure サブスクリプションに対してコマンドレットを実行する準備ができましたので、次にデータベースを作成するサーバーを含むリソース グループを確立します。次のコマンドを編集して選択した任意の有効な場所で使用できます。**(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** を実行して有効な場所の一覧を取得します。

次のコマンドを実行して、新しいリソース グループを作成します。

	New-AzureResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

新しいリソース グループの作成に成功したら、**ProvisioningState : Succeeded** と表示する画面がでます。


### サーバーの作成 

SQL Database は Azure SQL Database サーバーの内部で作成されます。**New-AzureSqlServer** を実行して新しいサーバーを作成します。ServerName をご利用のサーバー名に置き換えます。サーバー名がすでに使われている場合はエラーが発生する可能性があるため、すべての Azure SQL Server で一意のサーバー名を使用する必要があります。このコマンドは完了するまでに数分かかる場合があることに注意してください。コマンドを編集して有効なロケーションを選択して使用できますが、前の手順でのリソース グループ作成時に使用したロケーションと同じものでなければなりません。

	New-AzureSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

コマンドを実行すると、**[ユーザー名]** と **[パスワード]** の入力を求めるウィンドウが表示されます。ここで入力するユーザー名とパスワードは Azure の資格情報ではなく、新しいサーバーの管理者の資格情報となります

サーバーが正常に作成されると、サーバーの詳細が表示されます。

### サーバーのファイアウォール規則を構成し、サーバーへのアクセスを許可します。

ファイアウォール規則を確立してサーバーにアクセスします。ご利用のコンピューターで有効な開始 IP アドレスと終了 IP アドレスに置き換え、次のコマンドを実行します。

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

規則が正常に作成されると、ファイアウォール規則の詳細が表示されます。

他の Azure サービスによるサーバーへのアクセスを許可するには、ファイアウォール規則を追加し、StartIpAddress と EndIpAddress を 0.0.0.0 に設定します。これにより、任意の Azure サブスクリプションからの Azure トラフィックがサーバーへアクセスできるようになる点に注意してください。

詳細については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」をご覧ください。


## SQL Database の作成

これでリソース グループ、サーバー、ファイアウォール規則が構成され、サーバーにアクセスできるようになりました。

次のコマンドは、S1 パフォーマンス レベル、Standard (標準) サービス階層で新しい (空の) SQL データベースを作成します。


	New-AzureSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


データベースが正常に作成されると、データベースの詳細が表示されます。

## SQL データベースの PowerShell スクリプト作成

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## 次のステップ

- [SQL Server Management Studio (SSMS) での接続](sql-database-connect-to-database.md)


## その他のリソース

- [Azure SQL Dtabase](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=September15_HO1-->