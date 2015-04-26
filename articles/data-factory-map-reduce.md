<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="Azure Data Factory から MapReduce プログラムを起動する" description="Azure のデータのファクトリから、Azure の HDInsight クラスターで MapReduce プログラムを実行してデータを処理する方法について説明します。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Data Factory から MapReduce プログラムを起動する
この記事では、**MapReduce の変換**で **HDInsight アクティビティ**を使用して、Azure Data Factory パイプラインから **MapReduce** プログラムを起動する方法について説明しています。 

## はじめに 
Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。 

- **コピー アクティビティ**は、ソース ストレージからターゲット ストレージにデータをコピーします。コピー アクティビテの詳細については、「[Data Factory を使用してデータをコピーする][data-factory-copy-activity]」を参照してください。 
- **HDInsight アクティビティ**は、HDInsight クラスター上で Hive/Pig スクリプトまたは MapReduce プログラムを実行してデータを処理します。HDInsight アクティビティは、**Hive**、**Pig**、**MapReduce** の 3 つの変換をサポートします。HDInsight アクティビティは 1 つ以上の入力を消費し、1 つ以上の出力を生成することが可能です。
 
HDInsight アクティビティの Pig/Hive 変換を使用して、Azure Data Factory パイプラインから HDInsight クラスター上の Pig/Hive スクリプトを実行する方法の詳細については、「[Data Factory で Pig と Hive を使用する][data-factory-pig-hive-activities]」 を参照してください。この記事では、HDInsight アクティビティの MapReduce 変換を使用する方法について説明しています。

## パイプライン JSON 
パイプラインの JSON ファイルで、次のように設定します。
 
1. **activity** (アクティビティ) の **type** (種類) を **HDInsightActivity** に設定します。
2. **transformation** (変換) の **type** (種類) を **MapReduce** に設定します。
3. **className** プロパティでクラスの名前を指定します。
4. **jarFilePath** プロパティでファイル名を含む JAR ファイルへのパスを指定します。
5. **jarLinkedService** プロパティで、JAR ファイルを含む Azure BLOB ストレージを参照する、リンクされたサービスを指定します。   
6. **arguments** (引数) セクションで MapReduce プログラムに任意の引数を指定します。 

MapReduce 変換を使用すると、HDInsight クラスター上ですべての MapReduce jar ファイルを実行できます。次のサンプルのパイプラインの JSON 定義では、Mahout JAR ファイルを実行するために HDInsight アクティビティが構成されます。   
 

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

## サンプル
MapReduce の変換で HDInsight アクティビティを使用するためのサンプルは、[Data Factory Samples on GitHub][data-factory-samples] からダウンロードできます。  

## 関連項目

記事 | 説明
------ | ---------------
[Azure Data Factory の概要][data-factory-introduction] | この記事では、Azure Data Factory のサービス、コンセプト、利用価値、およびサポートするシナリオを紹介します。
[Azure Data Factory を使ってみる][adf-getstarted] | この記事には、Azure BLOB から Azure SQL データベースにデータをコピーする Azure Data Factory サンプルの作成方法についての詳細なチュートリアルが記載されています。
[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources] | この記事には、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[チュートリアル:Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
