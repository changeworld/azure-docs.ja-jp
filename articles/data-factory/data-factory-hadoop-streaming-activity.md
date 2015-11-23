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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Hadoop ストリーミング アクティビティ
HDInsightStreamingActivity アクティビティを使用して、Azure Data Factory パイプラインから Hadoop ストリーミング ジョブを呼び出すことができます。次の JSON スニペットは、パイプライン JSON ファイルで HDInsightStreamingActivity を使用する構文です。

Data Factory [パイプライン](data-factory-create-pipelines.md)の HDInsight Streaming アクティビティは、[独自](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)の、または[オンデマンド](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)の Windows/Linux ベースの HDInsight クラスターで Hadoop ストリーミング プログラムを実行します。この記事は、データ変換とサポートされる変換アクティビティの概要について説明する記事「[データ変換のアクティビティ](data-factory-data-transformation-activities.md)」を基に作成されています。

## 例

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
	               },
	               "policy":
	               {
	                   "concurrency": 1,
	                   "executionPriorityOrder": "NewestFirst",
	                   "retry": 1,
	                   "timeout": "01:00:00"
	               }
	            }
	        ]
	    }
	}

以下の点に注意してください。

1. **linkedServiceName** を、ストリーミングするmapreduce ジョブを実行する HDInsight クラスターを示すリンク サービス名に設定します。
2. activity の type を **HDInsightStreaming** に設定します。
3. **mapper** プロパティの場合、mapper 実行可能ファイルの名前を指定します。上記の例では、cat.exe が mapper 実行可能ファイルです。
4. **reducer** プロパティの場合、reducer 実行可能ファイルの名前を指定します。上記の例では、wc.exe が reducer 実行可能ファイルです。
5. **input** type プロパティの場合、mapper の入力ファイル (場所を含む) を指定します。例: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample は BLOB コンテナー、example/data/Gutenberg はフォルダー、davinci.txt は BLOB です。
6. **output** type プロパティの場合、reducer の出力ファイル (場所を含む) を指定します。Hadoop ストリーミング ジョブの出力は、このプロパティに指定されている場所に書き込まれます。
7. **filePaths** セクションには、mapper と reducer の実行可能ファイルのパスを指定します。例: "adfsample/example/apps/wc.exe", adfsample is the blob container: example/apps はフォルダー、wc.exe は実行可能ファイルです。
8. **fileLinkedService** プロパティの場合、filePaths セクションに指定されたファイルを含む Azure ストレージを表す Azure Storage リンク サービスを指定します。
9. **arguments** プロパティの場合、ストリーミング ジョブの引数を指定します。

> [AZURE.NOTE]例に示すように、 **出力** プロパティには、Hadoop ストリーミング アクティビティの出力データセットを指定する必要があります。これは、パイプラインのスケジュールを実行するために必要となる単なるダミーのデータセットです。**入力**プロパティに、アクティビティのすべての入力データセットを指定する必要はありません。

	

<!---HONumber=Nov15_HO3-->