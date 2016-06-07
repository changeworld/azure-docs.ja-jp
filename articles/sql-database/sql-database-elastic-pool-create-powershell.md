<properties
    pageTitle="PowerShell による新しい Elastic Database プールの作成 | Microsoft Azure"
    description="PowerShell を使用し、複数のデータベースを管理するスケーラブルなエラスティック データベース プールを作成して、Azure SQL Database リソースをスケールアウトする方法について説明します。"
	services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# PowerShell による新しい Elastic Database プールの作成

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


PowerShell コマンドレットを使った[エラスティック データベース プール](sql-database-elastic-pool.md)の作成方法について説明します。

一般的なエラー コードについては、「[SQL Database クライアント アプリケーションの SQL エラー コード: データベース接続エラーとその他の問題](sql-database-develop-error-messages.md)」を参照してください。

> [AZURE.NOTE] エラスティック データベース プールは現在プレビュー段階であり、SQL Database V12 サーバーでのみ使用できます。SQL Database V11 サーバーがある場合は、[PowerShell を使用して V12 へのアップグレードとプールの作成](sql-database-upgrade-server-portal.md)を 1 回の手順で実行できます。


Azure PowerShell 1.0 以降を実行している必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。

## 新しいプールの作成

プールの新規作成は、[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) コマンドレットで行います。プールあたりの eDTU、最小 DTU、最大 DTU は、サービス レベルの値 (Basic、Standard、Premium) によって制限されます。「[エラスティック プールとエラスティック データベースの eDTU と記憶域の上限](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)」を参照してください。

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## プールに新しいエラスティック データベースを作成する

[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) コマンドレットを使用して **ElasticPoolName** パラメーターを対象のプールに設定します。既存のデータベースをプールに移動する方法については、「[エラスティック プールへのデータベースの移動](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool)」を参照してください。

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## プールを作成して複数の新しいデータベースを追加する 

ポータルまたは PowerShell コマンドレットで一度に作成できるデータベースは 1 つのみであり、多数のデータベースをプールに作成しようとすると時間がかかる場合があります。新しいプールへの作成処理を自動化するには、「[CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)」を参照してください。

## 例: PowerShell を使用したプールの作成 

これは、新しい Azure リソース グループと新しいサーバーを作成するスクリプトです。メッセージが表示されたら、(自分の Azure 資格情報ではなく) 新しいサーバーの管理者のユーザー名とパスワードを指定してください。

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
- [エラスティック ジョブを作成する](sql-database-elastic-jobs-overview.md): エラスティック ジョブを使用すると、プール内にある任意の数のデータベースに対して T-SQL スクリプトを実行できます。
- [Azure SQL Database によるスケールアウト](sql-database-elastic-scale-introduction.md): Elastic Database のツールを使用してスケールアウトを実施します。

<!---HONumber=AcomDC_0601_2016-->