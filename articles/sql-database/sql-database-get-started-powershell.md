<properties
    pageTitle="PowerShell を使用した新しい SQL Database の設定 | Microsoft Azure"
    description="PowerShell で新しい SQL Database を作成する方法について説明します。PowerShell コマンドレットを使用して、一般的なデータベース設定タスクを管理できます。"
    keywords="新しい SQL Database の作成、データベースの設定"
	services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/09/2016"
    ms.author="sstein"/>

# 新しい SQL Database を作成し、PowerShell コマンドレットで一般的なデータベース設定タスクを実行します。


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



PowerShell のコマンドレットを使用して新しい SQL データベースを作成する方法について説明します。(Elastic Database の作成については、「[PowerShell による新しい Elastic Database プールの作成](sql-database-elastic-pool-create-powershell.md)」を参照してください。)


[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]

## データベースの設定: リソース グループ、サーバー、ファイアウォール規則を作成する

これで選択した Azure サブスクリプションに対してコマンドレットを実行する準備ができましたので、次にデータベースを作成するサーバーを含むリソース グループを確立します。次のコマンドを編集して選択した任意の有効な場所で使用できます。**(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location** を実行して有効な場所の一覧を取得します。

次のコマンドを実行して、新しいリソース グループを作成します。

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

新しいリソース グループの作成に成功すると、"**ProvisioningState : Succeeded**" と表示されます。


### サーバーの作成

SQL データベースは Azure SQL Database サーバーの内部で作成されます。**New-AzureRmSqlServer** を実行して新しいサーバーを作成します。*ServerName* をご利用のサーバー名に置き換えます。この名前は、すべての Azure SQL Database サーバーに対して一意であることが必要です。既存のサーバー名を使用すると、エラーが発生します。このコマンドは完了するまでに数分かかる場合があることに注意してください。コマンドを編集して有効な場所を選択して使用できますが、前の手順でのリソース グループ作成時に使用した場所と同じでなければなりません。

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

このコマンドを実行すると、ユーザー名とパスワードの入力を求められます。Azure の資格情報は入力しないでください。ここで入力するユーザー名とパスワードは、新しいサーバーに作成する管理者資格情報です。

サーバーが正常に作成されると、サーバーの詳細が表示されます。

### サーバーのファイアウォール規則を構成し、サーバーへのアクセスを許可します。

ファイアウォール規則を確立してサーバーにアクセスします。ご利用のコンピューターで有効な開始 IP アドレスと終了 IP アドレスに置き換え、次のコマンドを実行します。

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

規則が正常に作成されると、ファイアウォール規則の詳細が表示されます。

他の Azure サービスによるサーバーへのアクセスを許可するには、ファイアウォール規則を追加し、StartIpAddress と EndIpAddress を 0.0.0.0 に設定します。これにより、任意の Azure サブスクリプションからの Azure トラフィックがサーバーへアクセスできるようになる点に注意してください。

詳細については、「[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」をご覧ください。


## 新しい SQL データベースの作成

これでリソース グループ、サーバー、ファイアウォール規則が構成され、サーバーにアクセスできるようになりました。

次のコマンドは、S1 パフォーマンス レベル、Standard サービス階層で新しい (空の) SQL データベースを作成します。


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


データベースが正常に作成されると、データベースの詳細が表示されます。

## 新しい SQL Database PowerShell スクリプトを作成する

次に、新しい SQL Database PowerShell スクリプトを示します。

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


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"

    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp

    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel

    $SqlDatabase



## 次のステップ
新しい SQL Database を作成し、基本的なデータベース設定タスクを実行したら、次の手順を実行します。

- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)。


## その他のリソース

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0803_2016-->