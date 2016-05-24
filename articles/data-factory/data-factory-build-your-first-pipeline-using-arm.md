<properties
	pageTitle="最初の Data Factory の作成 (ARM テンプレート) | Microsoft Azure"
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
	ms.date="05/16/2016"
	ms.author="spelluru"/>

# チュートリアル: Azure Resource Manager テンプレートを使用した初めての Azure Data Factory の作成
> [AZURE.SELECTOR]
- [チュートリアルの概要](data-factory-build-your-first-pipeline.md)
- [Data Factory エディターの使用](data-factory-build-your-first-pipeline-using-editor.md)
- [PowerShell の使用](data-factory-build-your-first-pipeline-using-powershell.md)
- [Visual Studio の使用](data-factory-build-your-first-pipeline-using-vs.md)
- [Resource Manager テンプレートの使用](data-factory-build-your-first-pipeline-using-arm.md)


この記事では、Azure リソース マネージャー (ARM) テンプレートを使用して最初の Azure データ ファクトリを作成する方法を学習します。


## 前提条件
チュートリアルの「概要」トピックに記載されている前提条件とは別に、次をインストールする必要があります。

- 先に進む前に、「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」に目を通し、前提条件の手順を完了する**必要があります**。 
- **Azure PowerShell をインストールします**。「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」に記載されている手順に従って、コンピューターに Azure PowerShell の最新バージョンをインストールします。
- この記事では、Azure Data Factory サービスの概念については説明しません。サービスの詳細については、[Azure Data Factory の概要](data-factory-introduction.md)に関するページを参照してください。 
- 「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」を参照して、Azure リソース マネージャー (ARM) テンプレートについて確認してください。 

> [AZURE.IMPORTANT]
この記事のチュートリアルを実行する前に、こちらの「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」に記載された前提条件の手順を完了する必要があります。

## ARM テンプレートの作成

以下の内容を含む **ADFTutorialARM.json** という名前の JSON ファイルを **C:\\ADFGetStarted** フォルダーに作成します。

このテンプレートでは、次の Data Factory エンティティを作成できます。

1. **TutorialDataFactoryARM** という名前の**データ ファクトリ**。データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。パイプラインには、1 つまたは複数のアクティビティを含めることができます。たとえば、コピー元からコピー先のデータ ストアにデータをコピーするコピー アクティビティや、Hive スクリプトを実行し、入力データを変換して出力データを生成する HDInsight Hive アクティビティなどを含めることができます。 
2. 2 つの**リンクされたサービス** (**StorageLinkedService** と **HDInsightOnDemandLinkedService**)。これらのリンクされたサービスでは、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクします。Azure ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。HDInsight のリンクされたサービスは、このサンプルのパイプラインのアクティビティに指定された Hive スクリプトを実行するために使用されます。自分のシナリオで使用するデータ ストアやコンピューティング サービスを特定し、リンクされたサービスを作成して、それらのサービスをデータ ファクトリにリンクする必要があります。 
3. 2 つの (入力/出力) **データセット** (**AzureBlobInput** と **AzureBlobOutput**)。これらのデータセットは、Hive 処理における入力データと出力データを表します。これらのデータセットは、このチュートリアルで前に作成した **StorageLinkedService** を参照します。このリンクされたサービスは Azure ストレージ アカウントを指し、データセットは入力データと出力データを保持するストレージのコンテナー、フォルダー、ファイル名を指定します。   

**[Data Factory エディターを使用する]** タブをクリックすると、このテンプレートで使用する JSON プロパティの詳細に関する記事に切り替わります。

> [AZURE.IMPORTANT] **storageAccountName** 変数と **storageAccountKey** 変数は、実際の値に変更してください。また、**dataFactoryName** も変更してください。この名前は一意であることが必要です。


	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
	        "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "AzureStorageLinkedService",
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
                                "clusterSize": 4,
                                "version":  "3.2",
            					"timeToLive": "00:05:00",
                                "osType": "windows",
            					"linkedServiceName": "[variables('storageLinkedServiceName')]",
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
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
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
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
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
						                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
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
						        "start": "2016-04-01T00:00:00Z",
						        "end": "2016-04-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}

**[Data Factory エディターを使用する]** タブをクリックすると、このテンプレートで使用する JSON プロパティの詳細が記載された記事に切り替わります。

以下の点に注意してください。

- Data Factory は、上記の JSON で **Windows ベース**の HDInsight クラスターを自動的に作成します。**Linux ベース**の HDInsight クラスターを作成させることもできます。詳細については、「[オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)」を参照してください。 
- オンデマンド HDInsight クラスターの代わりに、**独自の HDInsight クラスター**を使用できます。詳細については、「[Azure HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)」を参照してください。
- HDInsight クラスターは、JSON (**linkedServiceName**) で指定した Blob Storage に**既定のコンテナー**を作成します。クラスターを削除しても、HDInsight はこのコンテナーを削除しません。これは設計によるものです。オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスを処理する必要があるたびに HDInsight クラスターが作成され、処理が終了すると削除されます。

	処理されるスライスが多いほど、Azure Blob Storage 内のコンテナーも増えます。ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。コンテナーの名前は、"adf**<データ ファクトリ名>**-**<リンクされたサービス名>**-<日時スタンプ>" というパターンになります。Azure Blob Storage 内のコンテナーを削除するには、[Microsoft ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを使用します。

詳細については、「[オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)」を参照してください。

> [AZURE.NOTE] Azure Data Factory を作成するための ARM テンプレートの例は、[Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json) にもあります。

## データ ファクトリの作成

1. **Azure PowerShell** を起動し、次のコマンドを実行します。 
	- **Login-AzureRmAccount** を実行し、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。  
	- 次のコマンドを実行して、Data Factory を作成するサブスクリプションを選択します。Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
1. 次のコマンドを実行し、手順 1. で作成した ARM テンプレートを使用して Data Factory エンティティをデプロイします。 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## パイプラインの監視
 
1.	[Azure ポータル](https://portal.azure.com/)にログインした後、**[参照]** をクリックして **[データ ファクトリ]** を選択します。 ![Browse->Data factories](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	**[データ ファクトリ]** ブレードで、作成したデータ ファクトリ (**TutorialFactoryARM**) をクリックします。	
2.	該当するデータ ファクトリの **[Data Factory]** ブレードで **[ダイアグラム]** をクリックします。 ![Diagram Tile](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	**ダイアグラム ビュー**に、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。
	
	![Diagram View](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. ダイアグラム ビューで、**AzureBlobOutput** データセットをダブルクリックします。現在処理中のスライスが表示されます。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. 処理が完了すると、スライスの状態に **[準備完了]** が表示されますオンデマンド HDInsight クラスターの作成には通常しばらく時間がかかることに注意してください (約 20 分)。 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. スライスが**準備完了**状態になったら、Blob Storage の **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。  

Azure ポータルを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については、[データセットとパイプラインの監視](data-factory-monitor-manage-pipelines.md)に関するセクションを参照してください。

データ パイプラインは、監視と管理アプリを使用して監視することもできます。このアプリケーションの使い方について詳しくは、「[新しい監視と管理アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)」を参照してください。

> [AZURE.IMPORTANT] 入力ファイルは、スライスが正常に処理された時点で削除されます。そのためスライスを取得したり、このチュートリアルをもう一度行ったりする場合は、adfgetstarted コンテナーの inputdata フォルダーに入力ファイル (input.log) をアップロードしてください。

<!---HONumber=AcomDC_0518_2016-->