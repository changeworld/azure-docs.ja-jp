<properties 
	pageTitle="Hive アクティビティ" 
	description="Azure データ ファクトリで Hive アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hive クエリを実行する方法について説明します。" 
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
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Hive アクティビティ

Data Factory [パイプライン](data-factory-create-pipelines.md)の HDInsight Hive アクティビティでは、[独自](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンド](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)の HDInsight クラスターで Hive クエリを実行します。この記事は、データ変換とサポートされる変換アクティビティの概要について説明する記事「[データ変換のアクティビティ](data-factory-data-transformation-activities.md)」を基に作成されています。

## 構文

	{
		"name": "Hive Activity",
	    "description": "description",
	    "type": "HDInsightHive",
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
	      "script": "Hive script",
	      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
	      "defines": {
	        "param1": "param1Value"
	      }
	    },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
	}
	
## 構文の詳細

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | アクティビティの名前 | あり
description | アクティビティの用途を説明するテキストです。 | いいえ
type | HDinsightHive | あり
inputs | Hive アクティビティによって使用される入力 | いいえ
outputs | Hive アクティビティによって生成される出力 | あり 
linkedServiceName | Data Factory のリンクされたサービスとして登録されている HDInsight クラスターへの参照 | あり 
script (スクリプト) | Hive スクリプトをインラインに指定します | いいえ
スクリプトのパス | Hive スクリプトを Azure BLOB ストレージに格納し、ファイルへのパスを指定します。'script' プロパティまたは 'scriptPath' プロパティを使用します。両方を同時に使用することはできません | いいえ 
defines | 'hiveconf' を使用して Hive スクリプト内で参照するキーと値のペアとしてパラメーターを指定します | いいえ

## 例

ゲームのログ分析の例について考えてみましょう。ここでは、お客様の会社が発売したゲームをユーザーがプレイした時間を特定します。

以下はゲーム ログのサンプルです。コンマ (,) で区切られていて、ProfileID、SessionStart、Duration、SrcIPAddress、GameType の各フィールドが含まれています。

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

このデータを処理する **Hive スクリプト**は、次のようになります。

	DROP TABLE IF EXISTS HiveSampleIn; 
	CREATE EXTERNAL TABLE HiveSampleIn 
	(
		ProfileID		string, 
	    SessionStart 	string, 
	    Duration 		int, 
	    SrcIPAddress 	string, 
	    GameType 		string
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
	
	DROP TABLE IF EXISTS HiveSampleOut; 
	CREATE EXTERNAL TABLE HiveSampleOut 
	(	
		ProfileID 	string, 
	    Duration 	int
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
	
	INSERT OVERWRITE TABLE HiveSampleOut
	Select 
		ProfileID,
		SUM(Duration)
	FROM HiveSampleIn Group by ProfileID

Data Factory パイプラインでこの Hive スクリプトを実行するには、以下の手順を実行する必要があります。

1. リンクされたサービスを作成し、[独自の HDInsight コンピューティング クラスター](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)に登録するか、または[オンデマンドの HDInsight コンピューティング クラスター](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)を構成します。このリンクされたサービスを "HDInsightLinkedService" と呼ぶことにしましょう。
2. [リンクされたサービス](data-factory-azure-storage-connector.md)を作成し、データをホストする Azure BLOB ストレージへの接続を構成します。このリンクされたサービスを "StorageLinkedService" と呼ぶことにしましょう。
3. 入力と出力のデータを指定する[データセット](data-factory-create-datasets.md)を作成します。入力データセットは "HiveSampleIn"、出力データセットは "HiveSampleOut" と呼ぶことにしましょう。
4. 上記の手順 2. で構成した Azure BLOB Storage にファイルとして Hive クエリをコピーします。データをホストするリンクされたサービスがこのクエリ ファイルをホストするものと異なる場合には、別の Azure Storage のリンクされたサービスを作成し、アクティビティの構成でこのリンクされたサービスを参照します。**scriptPath** を使用して Hive クエリ ファイルへのパスを指定し、**scriptLinkedService** を使用して、このスクリプト ファイルを含む Azure Storage を指定します。

	> [AZURE.NOTE]また、**script** プロパティを使えば、アクティビティ定義で Hive スクリプトをインライン化することもできます。ただし、JSON ドキュメント内でスクリプトのすべての特殊文字をエスケープする必要があり、デバッグの問題の原因となる場合があるため、この方法はお勧めできません。手順 4 の使用をお勧めします。
5.	HDInsightHive アクティビティで下のパイプラインを作成してデータを処理します。

		{
		  "name": "HiveActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "HiveActivitySample",
		        "type": "HDInsightHive",
		        "inputs": [
		          {
		            "name": "HiveSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "HiveSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Hour",
          			"interval": 1
        		}
		      }
		    ]
		  }
		}

6.	パイプラインをデプロイします。詳細については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。
7.	データ ファクトリの監視と管理のビューを使用して、パイプラインを監視します。詳細については、[Data Factory パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)に関する記事を参照してください。 


## defines 要素を使用する Hive スクリプトのパラメーターの指定 

ゲームのログが Azure BLOB ストレージに毎日取り込まれ、日付と時刻で分割されたフォルダーに格納される例を考えてみましょう。Hive スクリプトをパラメーター化し、実行時に入力フォルダーの場所を動的に渡し、さらに、日付と時刻で分割された出力も生成する必要があるとします。

パラメーター化された Hive スクリプトを使用するには、次の手順に従います。

- **defines** でパラメーターを定義します。

		{
			"name": "HiveActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		     	{
		        	"name": "HiveActivitySample",
		        	"type": "HDInsightHive",
			        "inputs": [
			          	{
				            "name": "HiveSampleIn"
				          }
		        	],
		        	"outputs": [
		          		{
				            "name": "HiveSampleOut"
				        }
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            		"Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		},
       					"scheduler": {
          					"frequency": "Hour",
          					"interval": 1
        				}
		        	}
		      	}
		    ]
		  }
		}

- Hive スクリプトでは、**${hiveconf:parameterName}** を使用してパラメーターを参照します。

		DROP TABLE IF EXISTS HiveSampleIn; 
		CREATE EXTERNAL TABLE HiveSampleIn 
		(
			ProfileID 	string, 
		    SessionStart 	string, 
		    Duration 	int, 
		    SrcIPAddress 	string, 
		    GameType 	string
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
		
		DROP TABLE IF EXISTS HiveSampleOut; 
		CREATE EXTERNAL TABLE HiveSampleOut 
		(
		    ProfileID 	string, 
		    Duration 	int
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
		
		INSERT OVERWRITE TABLE HiveSampleOut
		Select 
			ProfileID,
			SUM(Duration)
		FROM HiveSampleIn Group by ProfileID



## フィードバックの送信
この記事に関するフィードバックをお待ちしています。少しのお時間をとって、[電子メール](mailto:adfdocfeedback@microsoft.com?subject=data-factory-hive-activity.md)でフィードバックをお寄せください。

<!---HONumber=Oct15_HO4-->