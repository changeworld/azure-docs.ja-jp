<properties 
	pageTitle="Hadoop ストリーミング アクティビティ" 
	description="Azure Data Factory で Hadoop ストリーミング アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hadoop ストリーミング プログラミングを実行する方法について説明します。" 
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
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="spelluru"/>

# Hadoop ストリーミング アクティビティ
HDInsightStreamingActivity アクティビティを使用して、Azure Data Factory パイプラインから Hadoop ストリーミング ジョブを呼び出すことができます。次の JSON スニペットは、パイプライン JSON ファイルで HDInsightStreamingActivity を使用する構文です。

Data Factory [パイプライン](data-factory-create-pipelines.md)の HDInsight ストリーミング アクティビティは、[独自](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)の、または[オンデマンド](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)の Windows/Linux ベースの HDInsight クラスターで Hadoop ストリーミング プログラムを実行します。この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、[データ変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事に基づいています。

## JSON のサンプル
HDInsight クラスターには、サンプル プログラム (wc.exe および cat.exe) とデータ (davinci.txt) が自動的に設定されます。既定では、HDInsight クラスターで使用されるコンテナーの名前は、クラスター自体の名前になります。たとえば、クラスター名が myhdicluster の場合、関連付けられる BLOB コンテナーの名は myhdicluster になります。

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<nameofthecluster>/example/apps/wc.exe",
	                        "<nameofthecluster>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService",
	                    "getDebugInfo": "Failure"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

以下の点に注意してください。

1. **linkedServiceName** には、ストリーミングする mapreduce ジョブが実行される HDInsight クラスターを示す、リンクされたサービス名を設定します。
2. アクティビティの種類には **HDInsightStreaming** に設定します。
3. **mapper** プロパティには、mapper 実行可能ファイルの名前を指定します。例では、cat.exe が mapper 実行可能ファイルです。
4. **reducer** プロパティには、reducer 実行可能ファイルの名前を指定します。例では、wc.exe が reducer 実行可能ファイルです。
5. **input** プロパティには、mapper の入力ファイルを、場所を含めて指定します。例の "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample は BLOB コンテナー、example/data/Gutenberg はフォルダー、davinci.txt は BLOB です。
6. **output** プロパティには、reducer の出力ファイルを、場所を含めて指定します。Hadoop ストリーミング ジョブの出力は、このプロパティに指定されている場所に書き込まれます。
7. **filePaths** セクションには、mapper と reducer の実行可能ファイルのパスを指定します。例の "adfsample/example/apps/wc.exe"： adfsample は BLOB コンテナー、example/apps はフォルダー、wc.exe は実行可能ファイルです。
8. **fileLinkedService** プロパティには、filePaths セクションに指定されたファイルを含む Azure ストレージを表す Azure Storage のリンクされたサービスを指定します。
9. **arguments** プロパティには、ストリーミング ジョブの引数を指定します。
10. **getDebugInfo** プロパティは、省略可能な要素です。Failure に設定されていると、エラー時にのみログがダウンロードされます。All に設定されていると、ログは実行状態に関係なく常にダウンロードされます。

> [AZURE.NOTE] 例に示すように、**outputs** プロパティには、Hadoop ストリーミング アクティビティ用の出力データセットを指定します。このデータセットは、パイプラインのスケジュールを実行するために必要となる単なるダミーのデータセットです。**inputs**プロパティにアクティビティ用の入力データセットを指定する必要はありません。

	
## 例
このチュートリアルのパイプラインでは、Azure HDInsight クラスターで Word Count ストリーミングの Map/Reduce プログラムを実行します。

### リンクされたサービス

#### Azure Storage のリンクされたサービス
最初に、Azure HDInsight クラスターによって使用される Azure Storage を Azure データ ファクトリにリンクする、リンクされたサービスを作成します。次のコードをコピー/貼り付けする場合は、アカウント名とアカウント キーを、使用する Azure Storage のアカウント名とアカウント キーに必ず置き換えてください。

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Azure HDInsight のリンクされたサービス
次に、Azure HDInsight クラスターを Azure データ ファクトリにリンクする、リンクされたサービスを作成します。次のコードをコピー/貼り付けする場合は、HDInsight クラスター名を使用する HDInsight クラスターの名前に置き換え、ユーザー名とパスワードを変更してください。
	
	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### データセット

#### 出力データセット
この例のパイプラインには入力はありません。HDInsight ストリーミング アクティビティ用の出力データセットを指定します。このデータセットは、パイプラインのスケジュールを実行するために必要となる単なるダミーのデータセットです。

	{
	    "name": "StreamingOutputDataset",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "adftutorial/streamingdata/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### パイプライン

この例のパイプラインには、**HDInsightStreaming** という種類のアクティビティが 1 つだけあります。

HDInsight クラスターには、サンプル プログラム (wc.exe および cat.exe) とデータ (davinci.txt) が自動的に設定されます。既定では、HDInsight クラスターで使用されるコンテナーの名前は、クラスター自体の名前になります。たとえば、クラスター名が myhdicluster の場合、関連付けられる BLOB コンテナーの名は myhdicluster になります。

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<blobcontainer>/example/apps/wc.exe",
	                        "<blobcontainer>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

## 関連項目
- [Hive アクティビティ](data-factory-hive-activity.md)
- [Pig アクティビティ](data-factory-pig-activity.md)
- [MapReduce アクティビティ](data-factory-map-reduce.md)
- [Spark プログラムを呼び出す](data-factory-spark.md)
- [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0921_2016-->