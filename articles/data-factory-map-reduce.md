<properties 
	pageTitle="Azure Data Factory から MapReduce プログラムを起動する" 
	description="Azure のデータのファクトリから、Azure の HDInsight クラスターで MapReduce プログラムを実行してデータを処理する方法について説明します。" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Data Factory から MapReduce プログラムを起動する
この記事を呼び出す方法について説明します、 **MapReduce** を使用して、Azure のデータのファクトリのパイプラインからプログラムを **HDInsight アクティビティ** で **MapReduce 変換**です。

## はじめに 
Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。この記事では、HDInsight アクティビティの MapReduce 変換を使用する方法について説明しています。
 
参照してください [の Pig の使用、およびデータのファクトリでの Hive][data-factory-pig-hive-activities] Pig と Hive の実行の詳細について、HDInsight のアクティビティの Pig と Hive の変換を使用してに、Azure のデータの工場出荷時のパイプラインからクラスター化、HDInsight でのスクリプトです。

## JSON の MapReduce の変換を使用して HDInsight アクティビティ 

で、HDInsight のアクティビティの JSON の定義を指定します。
 
1. 設定、 **型** の **アクティビティ** に **HDInsightActivity**です。
2. 設定、 **型** の **変換** に **MapReduce**です。
3. クラスの名前を指定 **className** プロパティです。
4. ファイル名を含む JAR ファイルへのパスを指定して **jarFilePath** プロパティです。
5. ための JAR ファイルが含まれている Azure Blob ストレージを参照するリンクのサービスを指定 **jarLinkedService** プロパティです。   
6. MapReduce プログラムの引数を指定して、 **引数** セクションです。 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

MapReduce 変換を使用すると、HDInsight クラスター上ですべての MapReduce jar ファイルを実行できます。次のサンプルのパイプラインの JSON 定義では、Mahout JAR ファイルを実行するために HDInsight アクティビティが構成されます。

## サンプル
MapReduce の変換で、HDInsight のアクティビティを使用するためのサンプルをダウンロードすることができます。 [GitHub でのデータのファクトリ サンプル][data-factory-samples]です。

## 関連項目

記事 | 説明
------ | ---------------
[チュートリアル: 移動し、データのファクトリを使用してログ ファイルの処理][adf-tutorial] | この記事では、実際のほぼを実装する方法について説明する、エンド ツー エンド チュートリアル insights にログ ファイルからデータを変換する Azure のデータのファクトリを使用して世界中のシナリオです。このチュートリアルでは両方を使用する Pig や Hive の変換をデータを処理します。 
[Azure のデータの工場出荷時の開発者向けリファレンス][developer-reference] | コマンドレット、JSON スクリプト、関数などの包括的な参照の内容を開発者向けリファレンスには. 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir-->