<properties 
	pageTitle="PowerShell を使用して Azure SQL サーバーを V12 にアップグレードする" 
	description="PowerShell を使用して Azure SQL サーバーを V12 にアップグレードします。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>

# PowerShell を使用し、SQL Database V12 にアップグレードします


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


この記事では PowerShell を使用して、SQL Database V12 にアップグレードする方法を示します。

SQL Database V12 へのアップグレードの処理中に、すべての Web/Business データベースを新しいサービス層に更新する必要もあります。次の手順には、サーバー上の[すべての Web/Business データベースをアップグレードする](sql-database-upgrade-new-service-tiers.md)ために役立つ、価格レベルとエラスティック プールの推奨の使用が含まれています。


## 前提条件 

サーバーを PowerShell を使用する V12 にアップグレードするには、Azure PowerShell がインストールされ実行されている必要があります。バージョンによっては、リソース マネージャー モードに切り替えて Azure リソース マネージャーの PowerShell コマンドレットにアクセスする必要があります。

> [AZURE.IMPORTANT]Azure PowerShell 1.0 Preview のリリースから、Switch-AzureMode コマンドレットは利用できなくなりました。また、Azure ResourceManger モジュールで使用されていたコマンドレットは名前が変更されました。この記事の例では、新しい PowerShell 1.0 Preview の名付け規則が使用されています。詳細については、[Azure PowerShell での Switch-AzureMode の廃止](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell)に関するページを参照してください。

PowerShell コマンドレットを実行するには、Azure PowerShell をインストールして実行する必要があります。Switch-AzureMode が削除されたため、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行し、最新の Azure PowerShell をダウンロードしてインストールする必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。


## 資格情報を構成してサブスクリプションを選択

Azure サブスクリプションに PowerShell コマンドレットを実行するには、Azure アカウントにまずアクセスできるようにする必要があります。次を実行すると資格情報を入力するサインイン画面が表示されます。Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

	Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。

使用するサブスクリプションを選択するには、サブスクリプション ID (**SubscriptionId**) またはサブスクリプション名 (**SubscriptionName**) が必要になります。前の手順からコピーするか、複数のサブスクリプションがある場合は **Get-AzureSubscription** コマンドレットを実行して結果セットから目的のサブスクリプション情報をコピーできます。

現在のサブスクリプションを設定するには、サブスクリプション情報と共に次のコマンドレットを実行します。

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

上で選択したサブスクリプションに対し、次のコマンドが実行されます。

## 推奨の取得

サーバーのアップグレードに関する推奨設定を取得するには、次のコマンドレットを実行します。

    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

詳細については、[Azure SQL Database のエラスティック データベース プールの提案](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)と[Azure SQL Database の価格レベルに関する提案](sql-database-service-tier-advisor.md)の情報を参照してください。



## アップグレードを開始する

サーバーのアップグレードを開始するには、次のコマンドレットを実行します。

    Start-AzureRMSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


このコマンドを実行すると、アップグレード プロセスが開始されます。推奨設定の出力をカスタマイズし、編集した推奨設定をこのコマンドレットに提供できます。


## サーバーをアップグレードする


    # Adding the account
    #
    Add-AzureAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRMSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## カスタム アップグレードのマッピング

推奨設定がお使いのサーバーとビジネス ケースに適していない場合は、データベースをどのようにアップグレードするかを選択でき、それらを単一のデータベースまたはエラスティック データベースにマップできます。

ElasticPoolCollection と DatabaseCollection parameters は省略可能です。
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap1.Name = "DBMain1" 
    $databaseMap1.TargetEdition = "Standard" 
    $databaseMap1.TargetServiceLevelObjective = "S0"
    
    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap2.Name = "DBMain2" 
    $databaseMap2.TargetEdition = "Standard" 
    $databaseMap2.TargetServiceLevelObjective = "S2"
     
    # Starting the upgrade
    #
    Start-AzureRMSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    




## 関連情報

- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=Oct15_HO3-->