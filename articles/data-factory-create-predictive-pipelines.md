<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="Data Factory - Data Factory および Machine Learning を使用して予測パイプラインを作成する | Azure" description="作成する方法について説明します Azuer データ ファクトリと Azure の機械学習を使用して予測のパイプラインの作成。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する 
発行された [Azure Machine Learning][azure-machine-learning] モデルを Azure Data Factory パイプライン内で運用できます。これらのパイプラインは、予測パイプラインと呼ばれます。予測パイプラインを作成するには、次が必要です。

-	発行されたワークスペース モデルの API キーとバッチ スコアリング URL (下図を参照)。
-	スコアの入力 CSV ファイルを保持する Azure BLOB ストレージ。
-	スコア結果の CSV ファイルを保持する Azure BLOB ストレージ。

	![Machine Learning Dashboard][machine-learning-dashboard]

	AzureMLLinkedService のバッチ スコアリング URL が上図に示すように取得されます (-**help**)。:  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

**予測パイプライン**は、次から構成されます。

-	入力テーブルと出力テーブル
-	Azure ストレージと Azure ML のリンクされたサービス
-	Azure ML バッチ スコアリング アクティビティのパイプライン

## 例



1. Azure ストレージ用のリンクされたサービスを作成します。スコアリングの入力ファイルと出力ファイルが異なるストレージ アカウントにある場合は、リンクされたサービスが 2 つ必要です。JSON の例を次に示します。

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. 入力と出力の Azure Data Factory テーブルを作成します。他の Data Factory テーブルとは異なり、この 2つのテーブルは **folderPath** 値と **fileName** 値を含む必要があります。パーティション分割を使用すると、各バッ チ実行 (各データ スライス) で一意の入力ファイルと出力ファイルを処理または生成できます。入力を CSV ファイル形式に変換し、これを各スライスのストレージ アカウントに配置するには、いくつかのアップストリーム アクティビティを含める必要があります。その場合は、次の例に示す「waitOnExternal」設定を含めないと、ScoringInputBlob は別のアクティビティの出力テーブルになります。

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}

3. この出力例では、パーティション分割を使用して各スライスの実行ごとに一意の出力パスを作成します。これを行わないと、アクティビティはファイルを上書きします。

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. type: **AzureMLLinkedService** というリンクされたサービスを作成し、API キーとモデルのバッチ スコアリング URL を入力します。
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. 最終的に、**AzureMLBatchScoringActivity** を含むパイプラインが作成されます。これは、入力テーブルから入力ファイルの場所を取得し、AzureML バッチ スコアリング API を呼び出し、出力テーブルで指定された BLOB にバッチ スコアリング出力をコピーします。他の Data Factory テーブルとは異なり、AzureMLBatchScoringActivity は 1 つの入力テーブルと 1 つの出力テーブルのみを含むことができます。

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}

## 関連項目

記事 | 説明
------ | ---------------
[Azure Data Factory の概要][adf-introduction] | この記事では、Azure Data Factory のサービスおよびサポートするシナリオの概要について説明します。
[Azure Data Factory を使ってみる][adf-getstarted] | この記事には、Data Factory サンプルを作成して監視するための詳細な手順を説明する基本的なチュートリアルが記載されています。
[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources] | この記事には、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[Data Factory で Pig と Hive を使用する][use-pig-and-hive-with-data-factory] | この記事には、HDInsight アクティビティを使用して hive/pig スクリプトを実行し、入力データを処理して出力データを生成する方法を説明したチュートリアルが記載されています。
[チュートリアル:Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。エラーを発生させて (Azure SQL データベースのテーブルを削除する)、ADFTutorialDataFactory でこの記事のチュートリアルを試すことができます。 
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/ja-jp/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->
