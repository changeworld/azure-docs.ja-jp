<properties 
	pageTitle="Azure Data Factory から MapReduce プログラムを起動する"
	description="Azure Data Factory から Azure HDInsight クラスターで MapReduce プログラムを実行してデータを処理する方法について説明します。"
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
	ms.date="07/31/2015"
	ms.author="spelluru"/>

# Data Factory から MapReduce プログラムを起動する
この記事では、**HDInsight の MapReduce アクティビティ**を使用して、Azure Data Factory パイプラインから **MapReduce** プログラムを起動する方法について説明しています。

## はじめに 
Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。この記事では、HDInsight アクティビティの MapReduce 変換を使用する方法について説明しています。
 
HDInsight アクティビティの Pig/Hive 変換を使用して、Azure Data Factory パイプラインから HDInsight クラスター上の Pig/Hive スクリプトを実行する方法の詳細については、[Pig](data-factory-pig-activity) と [Hive](data-factory-hive-activity.md) に関する記事を参照してください。

## MapReduce の変換を使用する HDInsight アクティビティの JSON 

HDInsight アクティビティの JSON の定義で、以下を設定します。
 
1. **activity** の **type** を **HDInsight** に設定します。
3. **className** プロパティでクラスの名前を指定します。
4. **jarFilePath** プロパティでファイル名を含む JAR ファイルへのパスを指定します。
5. **jarLinkedService** プロパティで、JAR ファイルを含む Azure BLOB ストレージを参照する、リンクされたサービスを指定します。   
6. **arguments** セクションで MapReduce プログラムに任意の引数を指定します。 

 

		{
		  "name": "MahoutMapReduceSamplePipeline",
		  "properties": {
		    "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		    "activities": [
		      {
		        "name": "MyMahoutActivity",
		        "description": "Custom Map Reduce to generate Mahout result",
		        "inputs": [
		          {
		            "Name": "MahoutInput"
		          }
		        ],
		        "outputs": [
		          {
		            "Name": "MahoutOutput"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "type": "HDInsightMapReduce",
		        "typeProperties": {
		          "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		          "jarFilePath": "<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		          "jarLinkedService": "StorageLinkedService",
		          "arguments": [
		            "-s",
		            "SIMILARITY_LOGLIKELIHOOD",
		            "--input",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Input/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--output",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Output/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--maxSimilaritiesPerItem",
		            "500",
		            "--tempDir",
		            "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		          ]
		        },
		        "policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "OldestFirst",
		          "retry": 3,
		          "timeout": "01:00:00"
		        }
		      }
		    ]
		  }
		}

MapReduce 変換を使用すると、HDInsight クラスター上ですべての MapReduce jar ファイルを実行できます。次のサンプルのパイプラインの JSON 定義では、Mahout JAR ファイルを実行するために HDInsight アクティビティが構成されます。

## サンプル
MapReduce の変換で HDInsight アクティビティを使用するためのサンプルは、[Data Factory Samples on GitHub](data-factory-samples.md) からダウンロードできます。


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=September15_HO1-->