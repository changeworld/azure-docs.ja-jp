<properties 
	pageTitle="Data Factory を使用したスケジュール設定と実行" 
	description="Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。" 
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
	ms.date="08/22/2016" 
	ms.author="spelluru"/>

# Data Factory を使用したスケジュール設定と実行
  
この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。この記事は、[パイプラインの作成](data-factory-create-pipelines.md)および[データセットの作成](data-factory-create-datasets.md)に関する記事に基づいており、Data Factory アプリケーション モデルの基本的な概念 (アクティビティ、パイプライン、リンクされたサービス、データセット) を理解していることを前提としています。

## スケジュール設定のアクティビティ

アクティビティ JSON の **scheduler** セクションでは、アクティビティの定期的なスケジュールを指定できます。たとえば、次のように毎時間実行されるアクティビティをスケジュールできます。

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![スケジューラの例](./media/data-factory-scheduling-and-execution/scheduler-example.png)

図に示すように、アクティビティのスケジュールを指定すると、一連のタンブリング ウィンドウが作成されます。タンブリング ウィンドウとは、固定サイズで重複しない一連の連続する時間間隔です。アクティビティに対するこれらの論理的なタンブリング ウィンドウは、**アクティビティ ウィンドウ**と呼ばれます。
 
現在実行中のアクティビティ ウィンドウでは、アクティビティ JSON の [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) および [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) システム変数を使用して、アクティビティ ウィンドウに関連付けられた時間間隔にアクセスできます。これらの変数はアクティビティ JSON でさまざまな目的に使用できます。たとえば、これらの変数を使用して、時系列データを表す入力データセットと出力データセットからデータを選択できます。

**scheduler** プロパティは、データセットの **availability** プロパティと同じサブプロパティをサポートしています。詳細については、「[データセットの可用性](data-factory-create-datasets.md#Availability)」をご覧ください。例: 特定の時間オフセットでのスケジュール設定、アクティビティ ウィンドウの間隔の開始時または終了時に処理を調整するモードの設定。

アクティビティのスケジューラ プロパティの指定は任意です。指定する場合は、出力データセットの定義と指定するパターンとを一致させる必要があります。現在、スケジュールは出力データセットによって開始されるため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。

## 時系列のデータセットとデータ スライス

時系列データは、連続するシーケンスのデータ ポイントです。通常、一定の間隔で行われる連続的な測定値で構成されます。時系列データの一般的な例として、センサー データ、アプリケーション テレメトリ データなどがあります。

Azure Data Factory を使用すると、アクティビティ実行で時系列データを一括処理できます。通常、入力データを受信してから、出力データを生成する必要がある定期的なパターンがあります。このパターンは、次のようにデータセットの **availability** セクションを指定してモデル化します。

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

アクティビティ実行で使用および生成されるデータの各ユニットは、データ **スライス**と呼ばれます。次の図は、可用性セットが 1 時間ごとの頻度にそれぞれ設定された入力データセットと出力データセットを含むアクティビティの例を示しています。

![可用性スケジューラ](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

この図には、入力データセットと出力データセットの 1 時間ごとのデータ スライスが示されています。図では、処理の準備ができた 3 つの入力スライスがあり、10-11 AM 出力スライスを生成中の 10-11 AM アクティビティ実行があります。

現在生成中のスライスに関連付けられた時間間隔にアクセスするには、データセット JSON で変数 [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) と [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) を使用します。

現在、Data Factory では、アクティビティに指定されたスケジュールが、出力データセットの availability に指定されたスケジュールと正確に一致する必要があります。したがって、WindowStart、WindowEnd、SliceStart、SliceEnd は、常に同じ期間と 1 つの出力スライスにマップされます。

availability セクションで使用できるさまざまなプロパティの詳細については、[データセットの作成](data-factory-create-datasets.md)に関する記事をご覧ください。

## サンプル - Azure SQL のアクティビティ移動データを Azure BLOB にコピーする

Azure SQL テーブルのデータを Azure BLOB に 1 時間ごとにコピーするパイプラインを作成して、データを一括処理しましょう。

**入力: Azure SQL データセット**

	{
	    "name": "AzureSqlInput",
	    "properties": {
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}


**availability** セクションで **frequency** は **Hour**、**interval** は **1** に設定されています。

**出力: Azure BLOB データセット**
	
	{
	    "name": "AzureBlobOutput",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
	            "format": {
	                "type": "TextFormat"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**availability** セクションで **frequency** は **Hour**、**interval** は **1** に設定されています。



**アクティビティ: コピー アクティビティ**

	{
	    "name": "SamplePipeline",
	    "properties": {
	        "description": "copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "name": "AzureSQLtoBlob",
	                "description": "copy activity",	
	                "typeProperties": {
	                    "source": {
	                        "type": "SqlSource",
	                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 100000,
	                        "writeBatchTimeout": "00:05:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AzureSQLInput"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutput"
	                    }
	                ],
	       			"scheduler": {
	          			"frequency": "Hour",
	          			"interval": 1
	        		}
	            }
	        ],
	        "start": "2015-01-01T08:00:00Z",
	        "end": "2015-01-01T11:00:00Z"
	    }
	}


このサンプルでは、アクティビティ スケジュールとデータセットの availability セクションが 1 時間ごとの頻度に設定されています。サンプルでは、**WindowStart** と **WindowEnd** を使用して、アクティビティ実行の関連データを選択し、適切な **folderPath** を持つ BLOB にコピーする方法を示しています。folderPath は、1 時間ごとに個別のフォルダーを使用するようにパラメーター化されています。

午前 8 時～ 11 時の 3 つのスライスを実行すると、Azure SQL のデータは次のようになります。

![サンプル入力](./media/data-factory-scheduling-and-execution/sample-input-data.png)

パイプラインをデプロイすると、Azure BLOB が次のように設定されます。

1.	データが含まれている mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt ファイル

			10002345,334,2,2015-01-01 08:24:00.3130000
			10002345,347,15,2015-01-01 08:24:00.6570000
			10991568,2,7,2015-01-01 08:56:34.5300000

	> [AZURE.NOTE] &lt;Guid&gt; は実際の GUID に置き換えられます。ファイル名の例: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	データが含まれている mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt ファイル

			10002345,334,1,2015-01-01 09:13:00.3900000
			24379245,569,23,2015-01-01 09:25:00.3130000
			16777799,21,115,2015-01-01 09:47:34.3130000
3.	データが含まれていない mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt ファイル


## データ スライス、パイプラインと同時スライス実行のためのアクティブな期間

[パイプラインの作成](data-factory-create-pipelines.md)に関する記事で、**start** プロパティと **end** プロパティを設定して指定したパイプラインのアクティブな期間の概念を紹介しました。
 
パイプラインの過去のアクティブな期間の開始日を設定できます。この場合、Data Factory によって過去のすべてのデータ スライスが自動的に計算 (バック フィル) され、処理が開始されます。

バック フィルされたデータ スライスでは、並行実行するように構成できます。そのためには、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事に示すように、アクティビティ JSON の **policy** セクションで **concurrency** プロパティを設定します。

## 再実行が失敗したデータ スライスと自動データ依存関係追跡

わかりやすい画面でスライスの実行を監視できます。詳細については、[Azure ポータルのブレード](data-factory-monitor-manage-pipelines.md)**を使用したパイプラインの監視と管理**に関する記事または[アプリの監視と管理](data-factory-monitor-manage-app.md)に関する記事をご覧ください。

2 つのアクティビティがある次の例について考えてみます。Activity1 では、出力としてスライスを含む時系列データセットが生成されます。この出力は、Activity2 で入力として使用され、最終的な出力の時系列データセットが生成されます。

![失敗したスライス](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

この図は、最近の 3 つのスライスのうち、**Dataset2** の 9-10 AM スライスの生成時にエラーが発生したことを示しています。Data Factory では、時系列データセットの依存関係が自動的に追跡され、結果として、9-10 AM ダウンストリーム スライスのアクティビティ実行の開始が保留されます。


Data Factory の監視および管理ツールを使用すると、失敗したスライスの診断ログを詳細に調べ、問題の根本原因を簡単に見つけて修正できます。問題を解決したら、アクティビティ実行を簡単に開始して失敗したスライスを生成することもできます。再実行するには、データ スライスの状態遷移について理解する必要があります。詳細については、[Azure ポータルのブレード](data-factory-monitor-manage-pipelines.md)**を使用したパイプラインの監視と管理**に関する記事または[アプリの監視と管理](data-factory-monitor-manage-app.md)に関する記事をご覧ください。

dataset2 の 9-10 AM スライスを再実行し、スライスの準備ができると、次の図に示すように、最終データセットに対して 9-10 AM 依存スライスの実行が開始されます。

![失敗したスライスの再実行](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

アクティビティの連鎖の依存関係を指定および追跡する方法の詳細については、以下のセクションをご覧ください。

## 連鎖するアクティビティ
2 つのアクティビティを連鎖させるには、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。このとき、同じパイプラインのアクティビティと、別のパイプラインのアクティビティを指定できます。2 つ目のアクティビティは、1 つ目のアクティビティが正常に完了した後にのみ実行されます。

たとえば、次の場合を考えてみましょう。
 
1.	パイプライン P1 には、外部入力データセット D1 を必要とし、**出力**データセット **D2** を生成するアクティビティ A1 があります。
2.	パイプライン P2 には、データセット **D2** からの**入力**を必要とし、出力データセット D3 を生成するアクティビティ A2 があります。
 
このシナリオでは、外部データが使用可能なときにアクティビティ A1 が実行され、スケジュールされた可用性の頻度に達します。D2 のスケジュールされたスライスが使用可能になると、アクティビティ A2 が実行され、スケジュールされた可用性の頻度に達します。データセット D2 のスライスのいずれかでエラーが発生した場合、スライスが使用可能になるまで、そのスライスに対して A2 は実行されません。

ダイアグラム ビューは次の図のようになります。

![2 つのパイプラインでのアクティビティの連鎖](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

同じパイプラインに両方のアクティビティがあるダイアグラム ビューは、次の図のようになります。

![同じパイプラインでのアクティビティの連鎖](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### 順序指定されたコピー
複数のコピー操作を、順番にまたは順序を指定して 1 つずつ実行できます。パイプラインには、CopyActivity1 と CopyActivity 2 という 2 つのコピー アクティビティがあり、それぞれ入力データと出力データセットは次のようになります。

CopyActivity1: 入力: Dataset1 出力: Dataset2

CopyActivity2: 入力: Dataset2 出力: Dataset4

CopyActivity2 は、CopyActivity1 が正常に実行していて、Dataset2 を使用できた場合にのみ実行します。

この例では、CopyActivity2 で別の入力 (Dataset3 など) を使用できますが、CopyActivity2 への入力として Dataset2 も指定しているため、CopyActivity1 が完了するまでこのアクティビティは実行されません。次に例を示します。

CopyActivity1: 入力: Dataset1 出力: Dataset2

CopyActivity2: 入力: Dataset3、Dataset2 出力: Dataset4

複数の入力を指定すると、データのコピーに使用されるのは最初の入力データセットのみで、他のデータセットは依存関係として使用されます。CopyActivity2 は、次の条件が満たされた場合にのみ実行を開始します。

- CopyActivity1 が正常に完了していて、Dataset2 を使用できる。データを Dataset4 にコピーするときに、このデータセットは使用されません。これは、CopyActivity2 のスケジュールの依存関係としてのみ機能します。
- Dataset3 を使用できる。このデータセットは、コピー先にコピーされるデータを表します。



## 頻度が異なるデータセットのモデル化

各サンプルでは、入力および出力データセットとアクティビティ スケジュール ウィンドウの頻度は同じでした。シナリオによっては、1 つまたは複数の入力の頻度とは異なる頻度で出力を生成できる必要があります。Data Factory は、このようなシナリオのモデル化をサポートしています。

### サンプル 1: 毎時取得できる入力データの出力レポートを 1 日に 1 回生成する

センサーからの入力測定データを Azure BLOB で 1 時間ごとに使用できるシナリオについて考えてみます。Data Factory の [Hive アクティビティ](data-factory-hive-activity.md)を使用して、1 日の平均値、最大値、最小値などの統計情報を含む日次集計レポートを生成します。

Data Factory を使用して、このシナリオをモデル化する方法を次に示します。

**入力 Azure BLOB データセット:**

特定の日のフォルダーに毎時の入力ファイルが生成されます。入力の availability は Hourly に設定されています (frequency: Hour、interval: 1)。

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**出力 Azure BLOB データセット**

毎日、その日のフォルダーに出力ファイルが 1 つ作成されます。出力の availability は Daily に設定されています (frequency: Day、interval: 1)。


	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**アクティビティ: パイプラインの Hive アクティビティ**

次のスニペットに示すように、Hive スクリプトは **WindowStart** 変数を使用したパラメーターとして適切な日時情報を受け取ります。Hive スクリプトは、この変数を使用して、その日の適切なフォルダーからデータを読み込み、集計を実行して出力を生成します。

		{  
		    "name":"SamplePipeline",
		    "properties":{  
		    "start":"2015-01-01T08:00:00",
		    "end":"2015-01-01T11:00:00",
		    "description":"hive activity",
		    "activities": [
		        {
		            "name": "SampleHiveActivity",
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
		            "linkedServiceName": "HDInsightLinkedService",
		            "type": "HDInsightHive",
		            "typeProperties": {
		                "scriptPath": "adftutorial\\hivequery.hql",
		                "scriptLinkedService": "StorageLinkedService",
		                "defines": {
		                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
		                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
		                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
		                }
		            },
		            "scheduler": {
		                "frequency": "Day",
		                "interval": 1
		            },			
		            "policy": {
		                "concurrency": 1,
		                "executionPriorityOrder": "OldestFirst",
		                "retry": 2,
		                "timeout": "01:00:00"
		            }
	             }
		     ]
		   }
		}

次の図は、データ依存関係の観点からこのシナリオを示しています。

![データ依存関係](./media/data-factory-scheduling-and-execution/data-dependency.png)

毎日の出力スライスは、入力データセットの 24 時間のスライスに依存しています。Data Factory では、生成する出力スライスと同じ期間に属する入力データ スライスを特定して、これらの依存関係を自動的に計算します。24 個の入力スライスのいずれかを使用できない場合、Data Factory では入力スライスの準備が完了するまで待機してから、毎日のアクティビティ実行を開始します。


### サンプル 2: 式と Data Factory の関数を使用して依存関係を指定する

別のシナリオについて考えてみましょう。2 つのデータセットを処理する Hive アクティビティがあるとします。1 つは毎日新しいデータを取得しますが、もう 1 つは毎週新しいデータを取得します。この例では 2 つの入力を結合し、出力を毎日生成します。
 
出力データ スライスの期間に合わせることで、処理対象の適切な入力スライスを Data Factory で自動的に特定するという簡単な方法は、この例では使用できません。

個々のアクティビティ実行で、毎週の入力データセットについて最新の週のデータ スライスが Data Factory に使用されるように指定する方法が必要です。そのためには、次のスニペットに示すように、Azure Data Factory の関数を使用します。

**Input1: Azure BLOB**

最初の入力は、**毎日**更新される Azure BLOB です。
	
	{
	  "name": "AzureBlobInputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Input2: Azure BLOB**

Input2 は、**毎週**更新される Azure BLOB です。

	{
	  "name": "AzureBlobInputWeekly",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 7
	    }
	  }
	}

**出力: Azure BLOB**

毎日、その日のフォルダーに出力ファイルが 1 つ作成されます。出力の availability は Daily に設定されています (frequency: Day、interval: 1)。
	
	{
	  "name": "AzureBlobOutputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**アクティビティ: パイプラインの Hive アクティビティ**

Hive アクティビティは 2 つの入力を受け取り、出力スライスを毎日生成します。次のように、毎週の入力に対する最新の週の入力スライスに応じて、毎日の出力スライスを指定できます。
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"hive activity",
	    "activities": [
	      {
	        "name": "SampleHiveActivity",
	        "inputs": [
	          {
	            "name": "AzureBlobInputDaily"
	          },
	          {
	            "name": "AzureBlobInputWeekly",
	            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
	            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutputDaily"
	          }
	        ],
	        "linkedServiceName": "HDInsightLinkedService",
	        "type": "HDInsightHive",
	        "typeProperties": {
	          "scriptPath": "adftutorial\\hivequery.hql",
	          "scriptLinkedService": "StorageLinkedService",
	          "defines": {
	            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
	            "Month": "$$Text.Format('{0:%M}',WindowStart)",
	            "Day": "$$Text.Format('{0:%d}',WindowStart)"
	          }
	        },
	        "scheduler": {
	          "frequency": "Day",
	          "interval": 1
	        },			
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 2,  
	          "timeout": "01:00:00"
	        }
		   } 
	     ]
	   }
	}


## Data Factory の関数およびシステム変数   

Azure Data Factory でサポートされている関数とシステム変数の一覧については、[Data Factory の関数とシステム変数](data-factory-functions-variables.md)に関する記事をご覧ください。

## データ依存関係の詳細情報

アクティビティ実行でデータセット スライスを生成するために、Data Factory では、次の**依存関係モデル**を使用して、アクティビティで使用されるデータセットとアクティビティで生成されるデータセット間の関係を特定します。

出力データセット スライスの生成に必要な入力データセットの時間範囲は、**依存関係期間**と呼ばれます。

依存関係期間内の入力データセットのデータ スライスを使用できるようになった後にのみ、アクティビティ実行でデータセット スライスが生成されます。つまり、アクティビティ実行で出力データ スライスが生成されるには、依存関係期間を構成するすべての入力スライスが **Ready** 状態である必要があります。

データセット スライス [start、end] を生成するには、データセット スライスを依存関係期間にマップする関数が必要です。この関数は、本質的には、データセット スライスの開始と終了を依存関係期間の開始と終了に変換する式です。より形式的には次のようになります。
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

この f と g は、各アクティビティの依存関係期間の開始と終了を計算するマップ関数です。

サンプルに示すように、依存関係期間は生成されるデータ スライスの期間と同じです。このような場合、Data Factory によって、依存関係期間に属する入力スライスが自動的に計算されます。

例: 集計サンプルでは、出力が毎日生成され、入力データは 1 時間ごとに利用できます。データ スライス期間は 24 時間です。Data Factory によって、この期間に関連する毎時の入力スライスが検出され、入力スライスに応じて出力スライスが作成されます。

また、サンプルに示すように、入力の 1 つは毎週、出力スライスは毎日生成されるという独自のマッピングを依存関係期間に指定することもできます。
   
## データの依存関係と検証

必要に応じて、スライス実行を使用する前に、スライス実行で生成されるデータを検証する方法を指定した検証ポリシーをデータセットに定義することができます。詳細については、[データセットの作成](data-factory-create-datasets.md)の記事を参照してください。

この例では、スライスの実行が完了すると、出力スライスの状態は、サブジェクト状態が **Validation** の **Waiting** に変わります。スライスの検証が完了すると、スライスの状態は **Ready** に変わります。
   
データ スライスが生成されても、検証に合格しなかった場合、検証に失敗したスライスに依存するダウンストリーム スライスのアクティビティ実行は処理されません。

Data Factory のデータ スライスの多様な状態については、[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)の記事を参照してください。

## 外部データ

データセットを external としてマークすることで (次の JSON スニペットを参照)、Azure Data Factory で生成されたデータセットではないことを示すことができます。この場合、データセット ポリシーに、データセットの検証と再試行ポリシーを記述するパラメーター セットを追加できます。すべてのプロパティの説明については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事をご覧ください。

Data Factory で生成されるデータセットと同様に、外部データのデータ スライスを準備してから、依存スライスを生成する必要があります。

	{
		"name": "AzureSqlInput",
		"properties": 
		{
			"type": "AzureSqlTable",
			"linkedServiceName": "AzureSqlLinkedService",
			"typeProperties": 
			{
				"tableName": "MyTable"	
			},
			"availability": 
			{
				"frequency": "Hour",
				"interval": 1     
			},
			"external": true,
			"policy": 
			{
				"externalData": 
				{
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}  
		} 
	} 


## 1 回限りのパイプライン
パイプライン定義で指定した開始時刻から終了時刻までの間に定期的に (毎時、毎日など) 実行するパイプラインを作成し、スケジュールを設定することができます。詳細については、「[スケジュール設定のアクティビティ](#scheduling-and-execution)」をご覧ください。1 回だけ実行するパイプラインを作成することもできます。これを行うには、次の JSON サンプルに示すように、パイプライン定義の **pipelineMode** プロパティを **onetime** に設定します。このプロパティの既定値は **scheduled** です。

	{
	    "name": "CopyPipeline",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset"
	                    }
	                ]
	                "name": "CopyActivity-0"
	            }
	        ]
	        "pipelineMode": "OneTime"
	    }
	}

以下の点に注意してください。
 
- パイプラインの**開始**時刻と**終了**時刻は指定しません。
- Data Factory で値が使用されない場合でも、入力データセットと出力データセットの**可用性** (頻度と間隔) を指定します。
- ダイアグラム ビューには、1 回限りのパイプラインは表示されません。この動作は仕様です。
- 1 回限りのパイプラインを更新することはできません。1 回限りのパイプラインを複製して名前を変更し、プロパティを更新してデプロイすることで別のパイプラインを作成することができます。

  




  









 
 












      

  

<!---HONumber=AcomDC_0824_2016-->