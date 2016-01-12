<properties
	pageTitle="Azure Data Factory を使ってみる (Azure リソース マネージャー テンプレート)"
	description="このチュートリアルでは、Azure リソース マネージャー テンプレートを使用して、サンプルの Azure Data Factory パイプラインを作成します。"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/05/2016"
	ms.author="spelluru"/>

# Azure リソース マネージャー テンプレートを使用した初めての Azure Data Factory パイプラインの作成
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


この記事では、Azure リソース マネージャー (ARM) テンプレートを使用して最初の Azure データ ファクトリを作成する方法を学習します。


## 前提条件
チュートリアルの「概要」トピックに記載されている前提条件とは別に、次をインストールする必要があります。

- 先に進む前に、「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」に目を通し、前提条件の手順を完了する**必要があります**。 
- **Azure PowerShell をインストールします**。「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」に記載されている手順に従って、コンピューターに Azure PowerShell の最新バージョンをインストールします。
- この記事では、Azure Data Factory サービスの概念については説明しません。サービスの詳細については、[Azure Data Factory の概要](data-factory-introduction.md)に関するページを参照してください。 
- 「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」を参照して、Azure リソース マネージャー (ARM) テンプレートについて確認してください。 
 

## 手順 1: ARM テンプレートを作成する

以下の内容を含む **ADFTutorialARM.json** という名前の JSON ファイルを **C:\ADFGetStarted** フォルダーに作成します。

> [AZURE.IMPORTANT]**storageAccountName** 変数と **storageAccountKey** 変数は、実際の値に変更してください。また、**dataFactoryName** も変更してください。この名前は一意であることが必要です。

このテンプレートでは、次の Data Factory エンティティを作成できます。

1. **TutorialDataFactoryARM** という名前の**データ ファクトリ**。データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。パイプラインには、1 つまたは複数のアクティビティを含めることができます。たとえば、コピー元からコピー先のデータ ストアにデータをコピーするコピー アクティビティや、Hive スクリプトを実行し、入力データを変換して出力データを生成する HDInsight Hive アクティビティなどを含めることができます。 
2. 2 つの**リンクされたサービス** (**StorageLinkedService** と **HDInsightOnDemandLinkedService**)。これらのリンクされたサービスでは、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクします。Azure ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。HDInsight のリンクされたサービスは、このサンプルのパイプラインのアクティビティに指定された Hive スクリプトを実行するために使用されます。自分のシナリオで使用するデータ ストアやコンピューティング サービスを特定し、リンクされたサービスを作成して、それらのサービスをデータ ファクトリにリンクする必要があります。 
3. 2 つの (入力/出力) **データセット** (**AzureBlobInput** と **AzureBlobOutput**)。これらのデータセットは、Hive 処理における入力データと出力データを表します。これらのデータセットは、このチュートリアルで前に作成した **StorageLinkedService** を参照します。このリンクされたサービスは Azure ストレージ アカウントを指し、データセットは入力データと出力データを保持するストレージのコンテナー、フォルダー、ファイル名を指定します。   

**[Data Factory エディターを使用する]** タブをクリックすると、このテンプレートで使用する JSON プロパティの詳細に関する記事に切り替わります。

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<stroage account name>" ,
	        "storageAccountKey":  "<storage account key>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "StorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobInputDataset": "AzureBlobInput",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
	                        "typeProperties": {
	                            "version": "3.2",
	                            "clusterSize": 1,
	                            "timeToLive": "00:30:00",
	                            "linkedServiceName": "StorageLinkedService"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "StoraegLinkedService",
						        "typeProperties": {
						            "fileName": "input.log",
						            "folderPath": "adfgetstarted/inputdata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        },
						        "external": true,
						        "policy": {}
						    }
	                    },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "published": false,
						        "type": "AzureBlob",
						        "linkedServiceName": "StorageLinkedService",
						        "typeProperties": {
						            "folderPath": "adfgetstarted/partitioneddata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        }
						    }
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "StorageLinkedService",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
						                    }
						                },
						                "inputs": [
						                    {
						                        "name": "AzureBlobInput"
						                    }
						                ],
						                "outputs": [
						                    {
						                        "name": "AzureBlobOutput"
						                    }
						                ],
						                "policy": {
						                    "concurrency": 1,
						                    "retry": 3
						                },
						                "scheduler": {
						                    "frequency": "Month",
						                    "interval": 1
						                },
						                "name": "RunSampleHiveActivity",
						                "linkedServiceName": "HDInsightOnDemandLinkedService"
						            }
						        ],
						        "start": "2014-02-01T00:00:00Z",
						        "end": "2014-02-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}



## 手順 2: ARM テンプレートを使用して Data Factory エンティティをデプロイする

1. Azure PowerShell を起動し、次のコマンドを実行します。Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
	- **Login-AzureRmAccount** を実行し、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。  
	- **Get-AzureSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
	- **Select-AzureSubscription SubscriptionName** を実行して、使用するサブスクリプションを選択します。このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。
1. 次のコマンドを実行し、手順 1. で作成した ARM テンプレートを使用して Data Factory エンティティをデプロイします。 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## パイプラインの監視
 
1.	[Azure ポータル](http://portal.azure.com/)にログインした後、**[参照]** をクリックして **[データ ファクトリ]** を選択します。 
		![Browse->Data factories](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	**[データ ファクトリ]** ブレードで、作成したデータ ファクトリ (**TutorialFactoryARM**) をクリックします。	
2.	該当するデータ ファクトリの **[Data Factory]** ブレードで **[ダイアグラム]** をクリックします。 
		![Diagram Tile](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	**ダイアグラム ビュー**に、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。
	
	![Diagram View](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. ダイアグラム ビューで、**AzureBlobOutput** データセットをダブルクリックします。現在処理中のスライスが表示されます。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. 処理が完了すると、スライスの状態に **[準備完了]** が表示されますオンデマンド HDInsight クラスターの作成には通常しばらく時間がかかることに注意してください (約 20 分)。 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. スライスが**準備完了**状態になったら、Blob Storage の **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。  
 

<!---HONumber=AcomDC_0107_2016-->