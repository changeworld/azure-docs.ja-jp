<properties 
   pageTitle="Azure PowerShell を使用する Azure Data Lake Analytics の管理 | Azure" 
   description="Data Lake Analytics のジョブ、データ ソース、ユーザーの管理方法について説明します。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/26/2016"
   ms.author="edmaca"/>

# Azure PowerShell を使用する Azure Data Lake Analytics の管理

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure PowerShell を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。他のツールを使用する管理のトピックを表示する場合は、上のタブ セレクターをクリックします。

**前提条件**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。


<!-- ################################ -->
<!-- ################################ -->


##Azure PowerShell 1.0 以上をインストールする

「[Azure リソース マネージャーでの Azure PowerShell の使用](powershell-azure-resource-manager.md#prerequisites)」の「前提条件」セクションを参照してください。
	
## アカウントの管理

Data Lake Analytics ジョブを実行するには、Data Lake Analytics アカウントが必要です。Azure HDInsight とは異なり、ジョブを実行しなければ、Analytics アカウントには課金されません。ジョブの実行時にのみ課金されます。詳細については、「[Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

###アカウントの作成

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeStoreName = "<DataLakeAccountName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$location = "<Microsoft Data Center>"
	
	Write-Host "Create a resource group ..." -ForegroundColor Green
	New-AzureRmResourceGroup `
		-Name  $resourceGroupName `
		-Location $location
	
	Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
	New-AzureRmDataLakeStoreAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeStoreName `
		-Location $location 
	
	Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
	New-AzureRmDataLakeAnalyticsAccount `
		-Name $dataLakeAnalyticsAccountName `
		-ResourceGroupName $resourceGroupName `
		-Location $location `
		-DefaultDataLake $dataLakeStoreName
	
	Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
	Get-AzureRmDataLakeAnalyticsAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeAnalyticsAccountName  

Azure リソース グループのテンプレートを使用することもできます。Data Lake Analytics アカウントと従属する Data Lake Store アカウントを作成するためのテンプレートは、「[付録 A](#appendix-a)」にあります。.json テンプレートを使用してファイルにテンプレートを保存してから、以下の PowerShell スクリプトを使用してそれを呼び出します。


	$AzureSubscriptionID = "<Your Azure Subscription ID>"
	
	$ResourceGroupName = "<New Azure Resource Group Name>"
	$Location = "EAST US 2"
	$DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
	$DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
	
	$DeploymentName = "MyDataLakeAnalyticsDeployment"
	$ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
	
	Login-AzureRmAccount
	
	Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
	
	# Create the resource group
	New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
	
	# Create the Data Lake Analytics account with the default Data Lake Store account.
	$parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
	New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###アカウントのリスト

現在のサブスクリプション内の Data Lake Analytics アカウントをリストします。

	Get-AzureRmDataLakeAnalyticsAccount
	
出力は次のようになります。

	Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
	Location   : eastus2
	Name       : learn1021adla
	Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
	Tags       : {}
	Type       : Microsoft.DataLakeAnalytics/accounts

特定のリソース グループ内の Data Lake Analytics アカウントをリストします。

	Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

特定の Data Lake Analytics アカウントの詳細を取得します。

	Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

特定の Data Lake Analytics アカウントの存在をテストします。

	Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

コマンドレットは、**True** または **False** を返します。

###Data Lake Analytics アカウントの削除

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Analytics アカウントを削除しても、従属する Data Lake ストレージ アカウントは削除されません。次の例では、Data Lake Analytics アカウントと既定の Data Lake Store アカウントを削除します。

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

	Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
	Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## アカウント データ ソースの管理

Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

- [Azure Data Lake Storage](data-lake-storage-overview.md)
- [Azure Storage](storage-introduction.md)

Analytics アカウントを作成する際には、既定のストレージ アカウントとして Azure Data Lake ストレージ アカウントを指定する必要があります。既定の Data Lake Store アカウントは、ジョブ メタデータとジョブ監査ログの格納に使用されます。Analytics アカウントを作成したら、さらに Data Lake ストレージ アカウントや Azure Storage アカウントを追加することができます。

### 既定の Data Lake Store アカウントの検索

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### 他の Azure BLOB ストレージ アカウントの追加

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureStorageAccountName = "<AzureStorageAccountName>"
	$AzureStorageAccountKey = "<AzureStorageAccountKey>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### 他の Data Lake Store アカウントの追加

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureDataLakeName = "<DataLakeStoreName>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### データ ソースの一覧表示:

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts
	


<!-- ################################ -->
<!-- ################################ -->
## ジョブの管理

ジョブを作成するには、Data Lake Analytics アカウントが必要です。詳細については、「[Data Lake Analytics アカウントの管理](#manage-data-lake-analytics-accounts)」を参照してください。

### ジョブのリスト

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
	#States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
	#Results: Cancelled, Failed, None, Successed 
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

	# List all jobs submitted on January 1 (local time)
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-SubmittedAfter "2015/01/01"
		-SubmittedBefore "2015/01/02"	

	# List all jobs that succeeded on January 1 after 2 pm (UTC time)
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-State Ended
		-Result Succeeded
		-SubmittedAfter "2015/01/01 2:00 PM -0"
		-SubmittedBefore "2015/01/02 -0"

	# List all jobs submitted in the past hour
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-SubmittedAfter (Get-Date).AddHours(-1)

### ジョブの詳細の取得

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>
	
### ジョブの送信

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	#Pass script via path
	Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-Name $jobName `
		-ScriptPath $scriptPath

	#Pass script contents
	Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-Name $jobName `
		-Script $scriptContents

> [AZURE.NOTE] ジョブの既定の優先度は 1000 で、ジョブの並列処理の既定の次数は 1 です。


### ジョブの取り消し

	Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-JobID $jobID


## カタログ項目の管理

U-SQL カタログを使用して、U-SQL スクリプトで共有できるように、データとコードを構成します。カタログでは、Azure Data Lake のデータを使用して可能な限り最高のパフォーマンスを実現できます。詳細については、「[U-SQL カタログの使用](data-lake-analytics-use-u-sql-catalog.md)」を参照してください。

###カタログ項目のリスト

	#List databases
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Database
	
	
	
	#List tables
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo"

###カタログ項目の詳細の取得 

	#Get a database
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"
	
	#Get a table
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo.mytable"

###カタログ項目の存在のテスト

	Test-AzureRmDataLakeAnalyticsCatalogItem  `
		-Account $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"

###カタログ シークレットの作成
	New-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")

### カタログ シークレットの変更
	Set-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")



###カタログ シークレットの削除
	Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master"


## Azure リソース マネージャー グループの使用

アプリケーションは通常、Web アプリケーション、データベース、データベース サーバー、ストレージ、サード パーティのサービスなどの、複数のコンポーネントで構成されます。Azure リソース マネージャー (ARM) を使用すると、アプリケーション内の複数のリソースを 1 つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。デプロイメントにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。

Data Lake Analtyics サービスには、次のコンポーネントを含めることができます。

- Azure Data Lake Analytics アカウント
- 必要な既定の Azure Data Lake Storage アカウント
- 追加の Azure Data Lake Storage アカウント
- 追加の Azure Storage アカウント

管理しやすくするために 1 つの ARM グループの下にこれらすべてのコンポーネントを作成することができます。

![Azure Data Lake Analytics のアカウントとストレージ](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics アカウントと従属するストレージ アカウントは同じ Azure データ センターに配置する必要があります。ただし、ARM グループは別のデータ センターに配置できます。

##関連項目 

- [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
- [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)
- [Azure ポータルを使用する Azure Data Lake Analytics ジョブの監視とトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##付録 A - Data Lake Analytics ARM テンプレート

以下の ARM テンプレートを使用して、Data Lake Analytics アカウントとそれに従属する Data Lake Store アカウントをデプロイすることができます。それを json ファイルとして保存してから、PowerShell スクリプトを使用してテンプレートを呼び出します。詳細については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../resource-group-template-deploy.md#deploy-with-powershell)」および「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」を参照してください。

	{
	  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "adlAnalyticsName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the Data Lake Analytics account to create."
	      }
	    },
	    "adlStoreName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the Data Lake Store account to create."
	      }
	    }
	  },
	  "resources": [
	    {
	      "name": "[parameters('adlStoreName')]",
	      "type": "Microsoft.DataLakeStore/accounts",
	      "location": "East US 2",
	      "apiVersion": "2015-10-01-preview",
	      "dependsOn": [ ],
	      "tags": { }
	    },
	    {
	      "name": "[parameters('adlAnalyticsName')]",
	      "type": "Microsoft.DataLakeAnalytics/accounts",
	      "location": "East US 2",
	      "apiVersion": "2015-10-01-preview",
	      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
	      "tags": { },
	      "properties": {
	        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
	        "dataLakeStoreAccounts": [
	          { "name": "[parameters('adlStoreName')]" }
	        ]
	      }
	    }
	  ],
	  "outputs": {
	    "adlAnalyticsAccount": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
	    },
	    "adlStoreAccount": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
	    }
	  }
	}

<!---HONumber=AcomDC_0504_2016-->