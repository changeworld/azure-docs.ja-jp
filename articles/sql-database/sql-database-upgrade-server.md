<properties 
	pageTitle="Azure SQL サーバーを PowerShell を使用する V12 にアップグレードする" 
	description="Azure SQL サーバーを PowerShell を使用する V12 にアップグレードします。" 
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
	ms.date="09/05/2015" 
	ms.author="sstein"/>

# Azure SQL サーバーを PowerShell を使用する V12 にアップグレードする
 

この記事では、SQL Database サーバーを価格レベルとエラスティック プールの推奨設定を使用して V12 にアップグレードする方法を示します。



## 前提条件 

サーバーを PowerShell を使用する V12 にアップグレードするには、Azure PowerShell がインストールされ実行されている必要があります。バージョンによっては、リソース マネージャー モードに切り替えて Azure リソース マネージャーの PowerShell コマンドレットにアクセスする必要があります。

Azure PowerShell モジュールは、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行してダウンロードおよびインストールすることができます。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。

Azure SQL Database の作成と管理に使うコマンドレットは、Azure リソース マネージャー モジュールにあります。Azure PowerShell を開始するときに、Azure モジュールのコマンドレットが既定でインポートされます。Azure リソース マネージャー モジュールに切り替えるには、**Switch-AzureMode** コマンドレットを使用します。

	Switch-AzureMode -Name AzureResourceManager

「Switch-AzureMode コマンドレットは廃止予定で、今後のリリースで削除される予定です。」という警告が表示される場合、 無視して次のセクションに移動してください。

詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」をご覧ください。



## 資格情報の構成

Azure サブスクリプションに PowerShell コマンドレットを実行するには、Azure アカウントにまずアクセスできるようにする必要があります。次を実行すると資格情報を入力するサインイン画面が表示されます。Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

	Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


## Azure サブスクリプションを選択します。

使用するサブスクリプションを選択するには、サブスクリプション ID (**-SubscriptionId**) とサブスクリプション名 (**-SubscriptionName**) が必要になります。前の手順からコピーするか、複数のサブスクリプションがある場合は **Get-AzureSubscription** コマンドレットを実行して結果セットから目的のサブスクリプション情報をコピーできます。

現在のサブスクリプションを設定するには、サブスクリプション情報と共に次のコマンドレットを実行します。

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

上で選択したサブスクリプションに対し、次のコマンドが実行されます。

## 推奨の取得

サーバーのアップグレードに関する推奨設定を取得するには、次のコマンドレットを実行します。

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

詳細については、[Azure SQL Database のエラスティック データベース プールの提案](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)と[Azure SQL Database の価格レベルに関する提案](sql-database-service-tier-advisor.md)の情報を参照してください。



## アップグレードを開始する

サーバーのアップグレードを開始するには、次のコマンドレットを実行します。

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


このコマンドを実行すると、アップグレード プロセスが開始されます。推奨設定の出力をカスタマイズし、編集した推奨設定をこのコマンドレットに提供できます。


## Azure SQL サーバーをアップグレードする


    # Adding the account
    #
    Add-AzureAccount
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName 
    $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## カスタム アップグレードのマッピング

推奨設定がお使いのサーバーとビジネス ケースに適していない場合は、データベースをどのようにアップグレードするかを選択でき、それらを単一のデータベースまたはエラスティック データベースにマップできます。

複数のデータベースをエラスティック データベース プールにアップグレードする:

    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100  
    $elasticPool.DatabaseDtuMin = 0  
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"  
    $elasticPool.DatabaseCollection = ("DB1")  
    $elasticPool.Name = "elasticpool_1"  


複数のデータベースを単一のデータベースにアップグレードする:

    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties  $databaseMap.Name = "DB2"
    $databaseMap.TargetEdition = "Standard"
    $databaseMap.TargetServiceLevelObjective = "S0"
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool)
    




## 関連情報

- [Azure SQL Database リソース マネージャー コマンドレット](https://msdn.microsoft.com/library/mt163521.aspx)
- [Azure SQL Database サービス管理コマンドレット](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=Sept15_HO2-->