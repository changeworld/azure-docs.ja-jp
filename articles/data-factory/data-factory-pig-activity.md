<properties 
	pageTitle="Pig アクティビティ" 
	description="Azure データ ファクトリで Pig アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Pig スクリプトを実行する方法について説明します。" 
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
	ms.date="06/15/2016" 
	ms.author="spelluru"/>

# Pig アクティビティ

Data Factory [パイプライン](data-factory-create-pipelines.md)の HDInsight Pig アクティビティでは、[独自](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンド](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)の Windows/Linux ベースのHDInsight クラスターで Pig クエリを実行します。この記事は、データ変換とサポートされる変換アクティビティの概要について説明する記事「[データ変換のアクティビティ](data-factory-data-transformation-activities.md)」を基に作成されています。

## 構文

	{
		"name": "HiveActivitySamplePipeline",
	  	"properties": {
	    "activities": [
	    	{
	        	"name": "Pig Activity",
	        	"description": "description",
	        	"type": "HDInsightPig",
	        	"inputs": [
	          		{
	            		"name": "input tables"
	          		}
	        	],
	        	"outputs": [
	          		{
	            		"name": "output tables"
	          		}
        		],
	        	"linkedServiceName": "MyHDInsightLinkedService",
	        	"typeProperties": {
	          		"script": "Pig script",
	          		"scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
	          		"defines": {
	            		"param1": "param1Value"
	          		}
	        	},
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
	      	}
	    ]
	  }
	}

## 構文の詳細

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | アクティビティの名前 | はい
description | アクティビティの用途を説明するテキストです。 | いいえ
type | HDInsightPig | あり
inputs | Pig のアクティビティによって使用される入力 | いいえ
outputs | Pig のアクティビティによって生成される出力 | あり
linkedServiceName | Data Factory のリンクされたサービスとして登録されている HDInsight クラスターへの参照 | あり
script (スクリプト) | Pig スクリプトをインラインに指定します | いいえ
スクリプトのパス | Pig スクリプトを Azure BLOB ストレージに格納し、ファイルへのパスを指定します。'script' プロパティまたは 'scriptPath' プロパティを使用します。両方を同時に使用することはできません。ファイル名は大文字と小文字が区別されます。 | いいえ
defines | Pig スクリプト内で参照するキーと値のペアとしてパラメーターを指定します | いいえ

## 例

ゲームのログ分析の例について考えてみましょう。ここでは、お客様の会社が発売したゲームをユーザーがプレイした時間を特定します。
 
以下はゲーム ログのサンプルです。コンマ (,) で区切られていて、ProfileID、SessionStart、Duration、SrcIPAddress、GameType の各フィールドが含まれています。

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

このデータを処理する **Pig スクリプト**は、次のようになります。

	PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
	
	GroupProfile = Group PigSampleIn all;
	
	PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
	
	Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Data Factory パイプラインでこの Pig スクリプトを実行するには、以下の手順を実行する必要があります。

1. リンクされたサービスを作成し、[独自の HDInsight コンピューティング クラスター](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)に登録するか、または[オンデマンドの HDInsight コンピューティング クラスター](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)を構成します。このリンクされたサービスを "HDInsightLinkedService" と呼ぶことにしましょう。
2.	[リンクされたサービス](data-factory-azure-blob-connector.md)を作成し、データをホストする Azure BLOB ストレージへの接続を構成します。このリンクされたサービスを "StorageLinkedService" と呼ぶことにしましょう。
3.	入力と出力のデータを指定する[データセット](data-factory-create-datasets.md)を作成します。入力データセットは "PigSampleIn"、出力データセットは "PigSampleOut" と呼ぶことにしましょう。
4.	上記の手順 2. で構成した Azure BLOB ストレージのファイルに Pig クエリをコピーします。データをホストするリンクされたサービスがこのクエリ ファイルをホストするものと異なる場合には、別の Azure Storage のリンクされたサービスを作成し、アクティビティの構成でこのリンクされたサービスを参照します。**scriptPath** を使用して pig スクリプト ファイルへのパスを指定し、**scriptLinkedService** を使用して、このスクリプト ファイルを含む Azure Storage を指定します。
	
	> [AZURE.NOTE] また、**script** プロパティを使えば、アクティビティ定義で Pig スクリプトをインライン化することもできます。ただし、JSON ドキュメント内でスクリプトのすべての特殊文字をエスケープする必要があり、デバッグの問題の原因となる場合があるため、この方法はお勧めできません。手順 4. の使用をお勧めします。
5. HDInsightPig アクティビティで以下のパイプラインを作成し、データを処理します。

		{
		  "name": "PigActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "PigActivitySample",
		        "type": "HDInsightPig",
		        "inputs": [
		          {
		            "name": "PigSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "PigSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
		      }
		    ]
		  }
		} 
6. パイプラインをデプロイします。詳細については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。
7. データ ファクトリの監視と管理のビューを使用して、パイプラインを監視します。詳細については、[Data Factory パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)に関する記事を参照してください。

## defines 要素を使用する Pig スクリプトのパラメーターの指定

ゲームのログが Azure BLOB ストレージに毎日取り込まれ、日付と時刻で分割されたフォルダーに格納される例を考えてみましょう。Pig スクリプトをパラメーター化し、実行時に入力フォルダーの場所を動的に渡し、さらに、日付と時刻で分割された出力も生成する必要があるとします。
 
パラメーター化された Pig スクリプトを使用するには、次の手順に従います。

- **defines** でパラメーターを定義します。

		{
			"name": "PigActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		    	{
		        	"name": "PigActivitySample",
		        	"type": "HDInsightPig",
		        	"inputs": [
		          		{
		            		"name": "PigSampleIn"
		          		}
		        	],
		        	"outputs": [
		          		{
		            		"name": "PigSampleOut"
		          		}
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
				            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		}
		        	},
       				"scheduler": {
          				"frequency": "Day",
	          			"interval": 1
    	    		}
		      	}
		    ]
		  }
		}  
	  
- 次の例に示すように、'**$parameterName**' を使用するパラメーターを Pig スクリプトで参照します。

		PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);	
		GroupProfile = Group PigSampleIn all;		
		PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);		
		Store PigSampleOut into '$Output' USING PigStorage (','); 


## 関連項目
- [Hive アクティビティ](data-factory-hive-activity.md)
- [MapReduce アクティビティ](data-factory-map-reduce.md)
- [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
- [Spark プログラムを呼び出す](data-factory-spark.md)
- [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0706_2016-->