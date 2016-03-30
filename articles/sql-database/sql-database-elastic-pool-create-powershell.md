<properties 
    pageTitle="エラスティック データベース プールの作成 (PowerShell) | Microsoft Azure" 
    description="PowerShell を使用し、複数のデータベースを管理するエラスティック データベース プールを作成して、Azure SQL Database リソースをスケールアウトする方法について説明します。" 
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# エラスティック データベース プールの作成 (PowerShell) 

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


PowerShell コマンドレットを使った[エラスティック データベース プール](sql-database-elastic-pool.md)の作成方法について説明します。

一般的なエラー コードについては、「[SQL Database クライアント アプリケーションの SQL エラー コード: データベース接続エラーとその他の問題](sql-database-develop-error-messages.md)」を参照してください。

> [AZURE.NOTE] エラスティック データベース プールは現在プレビュー段階であり、SQL Database V12 サーバーでのみ使用できます。SQL Database V11 サーバーがある場合は、[PowerShell を使用して V12 へのアップグレードとプールの作成](sql-database-upgrade-server-portal.md)を 1 回の手順で実行できます。


Azure PowerShell 1.0 以降を実行している必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。



## エラスティック データベース プールの作成

[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) コマンドレットにより、エラスティック データベース プールが作成されます。

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## エラスティック データベース プールでの新しいエラスティック データベースの作成

プール内に直接新しいデータベースを作成するには、[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) コマンドレットを使用して **ElasticPoolName** パラメーターを設定します。


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## エラスティック データベース プールへのスタンドアロン データベースの移動

プール内に既存のデータベースを移動するには、[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) コマンドレットを使用して **ElasticPoolName** パラメーターを設定します。

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## エラスティック データベース プールの作成例 (PowerShell)

このスクリプトでは、新しいサーバーを作成します。そのため、ユーザー名とパスワードの入力を求められた場合は、(Azure の資格情報ではなく) 新しいサーバーの管理ログインと管理パスワードを入力します。

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## 次のステップ

- [プールを管理する](sql-database-elastic-pool-manage-powershell.md)
- [エラスティック ジョブを作成する](sql-database-elastic-jobs-overview.md): エラスティック ジョブは、プール内にある任意の数のデータベースに対して T-SQL スクリプトの実行を容易にします。


## エラスティック データベースのリファレンス

エラスティック データベースとエラスティック データベース プールの詳細については、「[SQL Database エラスティック データベース プールのリファレンス](sql-database-elastic-pool-reference.md)」を参照してください。

<!---HONumber=AcomDC_0323_2016-->