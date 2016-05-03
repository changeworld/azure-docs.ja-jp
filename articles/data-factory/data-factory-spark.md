<properties 
	pageTitle="Azure Data Factory から Spark プログラムを呼び出す" 
	description="MapReduce アクティビティを使用して Azure Data Factory から Spark プログラムを呼び出す方法について説明します。" 
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
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# Data Factory から Spark プログラムを起動する
## はじめに
Data Factory パイプラインで MapReduce アクティビティを使用して、HDInsight Spark クラスターで Spark プログラムを実行することができます。この記事を読む前に、アクティビティの使用の詳細について、[MapReduce アクティビティ](data-factory-map-reduce.md)に関する記事を参照してください。

## GitHub 上の Spark サンプル
[GitHub 上の Spark - Data Factory サンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark)は、MapReduce アクティビティを使用して Spark プログラムを起動する方法を示しています。Spark プログラムは、単に、1 つの Azure BLOB コンテナーから別のコンテナーにデータをコピーします。

## Data Factory のエンティティ
**Spark-ADF/src/ADFJsons** フォルダーには、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) のファイルが含まれています。

このサンプルには、2 つの**リンクされたサービス**があります。Azure Storage と Azure HDInsight です。Azure ストレージの名前とキーの値を **StorageLinkedService.json** に指定し、clusterUri、userName、およびパスワードを **HDInsightLinkedService.json** に指定する必要があります。

このサンプルには、2 つの**データセット**があります。**input.json** と **output.json** です。これらのファイルは、**Datasets** フォルダーにあります。これらのファイルが表しているのは、MapReduce アクティビティの入力および出力データセットです。

サンプルには、1 つの**パイプライン**が **ADFJsons/Pipeline** フォルダーにあります。これは、MapReduce アクティビティを使用して Spark プログラムを呼び出す方法を理解するために確認する必要がある、最も重要なエンティティです。

	{
	    "name": "SparkSubmit",
	    "properties": {
	        "description": "Submit a spark job",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "com.adf.spark.SparkJob",
	                    "jarFilePath": "libs/spark-adf-job-bin.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "--jarFile",
	                        "libs/sparkdemoapp_2.10-1.0.jar",
	                        "--jars",
	                        "/usr/hdp/current/hadoop-client/hadoop-azure-2.7.1.2.3.3.0-3039.jar,/usr/hdp/current/hadoop-client/lib/azure-storage-2.2.0.jar",
	                        "--mainClass",
	                        "com.adf.spark.demo.Demo",
	                        "--master",
	                        "yarn-cluster",
	                        "--driverMemory",
	                        "2g",
	                        "--driverExtraClasspath",
	                        "/usr/lib/hdinsight-logging/*",
	                        "--executorCores",
	                        "1",
	                        "--executorMemory",
	                        "4g",
	                        "--sparkHome",
	                        "/usr/hdp/current/spark-client",
	                        "--connectionString",
	                        "DefaultEndpointsProtocol=https;AccountName=<YOUR_ACCOUNT>;AccountKey=<YOUR_KEY>",
	                        "input=wasb://input@<YOUR_ACCOUNT>.blob.core.windows.net/data",
	                        "output=wasb://output@<YOUR_ACCOUNT>.blob.core.windows.net/results"
	                    ]
	                },
	                "inputs": [
	                    {
	                        "name": "input"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "output"
	                    }
	                ],
	                "policy": {
	                    "executionPriorityOrder": "OldestFirst",
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "Spark Launcher",
	                "description": "Submits a Spark Job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2015-11-16T00:00:01Z",
	        "end": "2015-11-16T23:59:00Z",
	        "isPaused": false,
	        "pipelineMode": "Scheduled"
	    }
	}

見てわかるように、MapReduce アクティビティは、Azure ストレージの **libs** コンテナーの **spark-adf-job-bin.jar** を呼び出すように構成されています (StorageLinkedService.json で指定)。このプログラムのソース コードは Spark-ADF/src/main/java/com/adf/spark フォルダーにあり、spark-submit を呼び出して Spark ジョブを実行します。

HDInsight Spark クラスターで実行されている、この MapReduce プログラム (spark-adf-job-bin.jar) は、Spark プログラム **sparkdemoapp\_2.10-1.0.jar** を呼び出して、MapReduce アクティビティを通じて受け取った引数 (上の JSON を参照) を Spark プログラムに渡します。**sparkdemoapp\_2.10-1.0.jar** には、データを 1 つの Azure BLOB コンテナーから他のコンテナーにコピーする Scala ソース コードが含まれています。このデモ アプリ jar を、Spark を使用して実行しようとしている任意のジョブが含まれている他の任意の jar と置き換えることができます。

要約すると、**MapReduce アクティビティ**は、Spark プログラム **sparkdemoapp\_2.10-1.0.jar** を呼び出す MapReduce プログラム **spark-adf-job-bin.jar** を呼び出すということになります。独自の Spark プログラムを実行するには、sparkdemoapp\_2.10-1.0.jar を独自のものに置き換えます。

> [AZURE.NOTE] MapReduce アクティビティを使用して Spark プログラムを呼び出すには、この方法で独自の HDInsight Spark クラスターを使用する必要があります。オンデマンド HDInsight クラスターの使用は、サポートされていません。


## 関連項目
- [Hive アクティビティ](data-factory-hive-activity.md)
- [Pig アクティビティ](data-factory-pig-activity.md)
- [MapReduce アクティビティ](data-factory-map-reduce.md)
- [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
- [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0420_2016-->