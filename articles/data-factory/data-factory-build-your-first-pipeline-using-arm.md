<properties
	pageTitle="初めてのデータ ファクトリの作成 (Resource Manager テンプレート) | Microsoft Azure"
	description="このチュートリアルでは、Azure Resource Manager テンプレートを使用して、サンプルの Azure Data Factory パイプラインを作成します。"
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
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# チュートリアル: Azure Resource Manager テンプレートを使用した初めての Azure Data Factory の作成
> [AZURE.SELECTOR]
- [概要と前提条件](data-factory-build-your-first-pipeline.md)
- [Azure ポータル](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

この記事では、Azure Resource Manager テンプレートを使用して最初の Azure データ ファクトリを作成します。

## 前提条件
- 「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」に目を通し、**前提条件**の手順を完了する必要があります。
- 「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」に記載されている手順に従って、コンピューターに Azure PowerShell の最新バージョンをインストールします。
- Azure Resource Manager テンプレートについては、「[Azure Resource Manager テンプレートの作成](../resource-group-authoring-templates.md)」を参照してください。

## Resource Manager テンプレートの作成

このセクションでは、以下の Data Factory エンティティを作成します。

1. **TutorialDataFactoryARM** という名前の**データ ファクトリ**。データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。パイプラインには、1 つまたは複数のアクティビティを含めることができます。たとえば、コピー元からコピー先のデータ ストアにデータをコピーするコピー アクティビティや、Hive スクリプトを実行して入力データを変換する HDInsight Hive アクティビティなどを含めることができます。
2. 2 つの**リンクされたサービス** (**StorageLinkedService** と **HDInsightOnDemandLinkedService**)。これらのリンクされたサービスでは、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクします。Azure ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。HDInsight のリンクされたサービスは、このサンプルのパイプラインのアクティビティに指定された Hive スクリプトを実行するために使用されます。自分のシナリオで使用するデータ ストアやコンピューティング サービスを特定し、リンクされたサービスを作成して、それらのサービスをデータ ファクトリにリンクします。
3. 2 つの (入力/出力) **データセット** (**AzureBlobInput** と **AzureBlobOutput**)。これらのデータセットは、Hive 処理の入力データと出力データを表します。これらのデータセットは、このチュートリアルで前に作成した **StorageLinkedService** を参照します。このリンクされたサービスは Azure ストレージ アカウントを指し、データセットは入力データと出力データを保持するストレージのコンテナー、フォルダー、ファイル名を指定します。

**[Data Factory エディターを使用する]** タブをクリックすると、このテンプレートで使用する JSON プロパティの詳細に関する記事に切り替わります。

以下の内容を含む **ADFTutorialARM.json** という名前の JSON ファイルを **C:\\ADFGetStarted** フォルダーに作成します。

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

**[Using Data Factory Editor (Data Factory エディターを使用する)]** タブをクリックすると、このテンプレートで使用する JSON プロパティの詳細が記載された記事に切り替わります。

以下の点に注意してください。

- Data Factory は、上記の JSON で **Windows ベース**の HDInsight クラスターを自動的に作成します。**Linux ベース**の HDInsight クラスターを作成させることもできます。詳細については、[オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)に関するセクションを参照してください。
- オンデマンド HDInsight クラスターの代わりに、**独自の HDInsight クラスター**を使用できます。詳細については、[HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)に関するセクションを参照してください。
- HDInsight クラスターは、JSON (**linkedServiceName**) で指定した Blob Storage に**既定のコンテナー**を作成します。クラスターを削除しても、HDInsight はこのコンテナーを削除しません。この動作は仕様です。オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスを処理する必要があるたびに HDInsight クラスターが作成され、処理が終了すると削除されます。

	処理されるスライスが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。これらのコンテナーの名前は、"adf**<データ ファクトリ名>**-**<リンクされたサービス名>**-<日時スタンプ>" というパターンに従います。Azure Blob Storage 内のコンテナーを削除するには、[Microsoft ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを使用します。

詳細については、[オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)に関するセクションを参照してください。

> [AZURE.NOTE] Azure データ ファクトリを作成するための Resource Manager テンプレートの例は、[Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json) にもあります。

## データ ファクトリの作成

1. **Azure PowerShell** を起動し、次のコマンドを実行します。
	- `Login-AzureRmAccount` を実行し、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。
	- `Get-AzureRmSubscription` を実行して、このアカウントのサブスクリプションをすべて表示します。
	- `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` を実行して、使用するサブスクリプションを選択します。このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。
1. 次のコマンドを実行し、手順 1. で作成した Resource Manager テンプレートを使用して Data Factory エンティティをデプロイします。

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## パイプラインを監視する
 
1.	[Azure Portal](https://portal.azure.com/) にログインした後、**[参照]** をクリックして **[データ ファクトリ]** を選択します。 ![Browse->Data factories](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	**[データ ファクトリ]** ブレードで、作成したデータ ファクトリ (**TutorialFactoryARM**) をクリックします。
2.	該当するデータ ファクトリの **[Data Factory]** ブレードで **[ダイアグラム]** をクリックします。 ![Diagram Tile](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	**ダイアグラム ビュー**に、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。
	
	![ダイアグラム ビュー](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png)
8. ダイアグラム ビューで、**AzureBlobOutput** データセットをダブルクリックします。現在処理中のスライスが表示されます。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. 処理が完了すると、スライスの状態に **[準備完了]** が表示されます。オンデマンド HDInsight クラスターの作成には通常しばらく時間がかかります (約 20 分)。そのため、パイプラインによるスライスの処理に**約 30 分**かかると想定してください。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)
10. スライスが**準備完了**状態になったら、Blob Storage の **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。

Azure ポータル ブレードを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については、[データセットとパイプラインの監視](data-factory-monitor-manage-pipelines.md)に関するページを参照してください。

データ パイプラインは、監視と管理アプリを使用して監視することもできます。このアプリケーションの使い方の詳細については、[監視アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。

> [AZURE.IMPORTANT] 入力ファイルは、スライスが正常に処理された時点で削除されます。そのためスライスを取得したり、このチュートリアルをもう一度行ったりする場合は、adfgetstarted コンテナーの inputdata フォルダーに入力ファイル (input.log) をアップロードしてください。

## ゲートウェイを作成するための Resource Manager テンプレート
背後で論理ゲートウェイを作成するためのサンプル Resource Manager テンプレートを次に示します。オンプレミス コンピューターまたは Azure IaaS VM にゲートウェイをインストールし、キーを使用して Data Factory サービスにゲートウェイを登録します。詳細については、[オンプレミスとクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を参照してください。

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "GatewayUsingArmDF",
	        "apiVersion": "2015-10-01",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "eastus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "gateways",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "name": "GatewayUsingARM",
	                    "properties": {
	                    	"description": "my gateway"
						}
	                }            
				]
	        }
	    ]
	}

このテンプレートでは、GatewayUsingARM という名前のゲートウェイで GatewayUsingArmDF という名前のデータ ファクトリを作成しています。

## 関連項目
| トピック | 説明 |
| :---- | :---- |
| [データ変換のアクティビティ](data-factory-data-transformation-activities.md) | この記事には、Azure Data Factory でサポートされているデータ変換のアクティビティ (このチュートリアルで使用した HDInsight Hive 変換など) の一覧を示します。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) | この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [パイプライン](data-factory-create-pipelines.md) | この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法を説明します。 |
| [データセット](data-factory-create-datasets.md) | この記事では、Azure Data Factory のデータセットについて説明します。
| [監視アプリを使用したパイプラインの監視と管理に関する記事](data-factory-monitor-manage-app.md) | この記事では、監視と管理アプリを使用してパイプラインを監視、管理、デバッグする方法について説明します。 

  

<!---HONumber=AcomDC_0921_2016-->