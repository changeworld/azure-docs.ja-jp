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
	ms.date="04/11/2016" 
	ms.author="spelluru"/>

# Data Factory を使用したスケジュール設定と実行
  
この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。この記事は、「[パイプラインの作成](data-factory-create-pipelines.md)」と「[データセットの作成](data-factory-create-datasets.md)」の記事に基づいて書かれています。また、Data Factory アプリケーション モデルの基本的な概念 (アクティビティ、パイプライン、リンクされるサービスとデータセット) について理解している前提で説明します。

## スケジュール設定のアクティビティ

アクティビティ JSON の **scheduler** セクションでは、アクティビティの定期的なスケジュールを指定できます。たとえば、次のように毎時間実行されるアクティビティをスケジュールできます。

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![スケジューラの例](./media/data-factory-scheduling-and-execution/scheduler-example.png)

上記のように、アクティビティのスケジュールの指定は一連のタンブリング ウィンドウを作成します。タンブリング ウィンドウとは、一連の固定サイズで重複しない連続する時間間隔です。アクティビティに対するこれらの論理的なタンブリング ウィンドウは、**アクティビティ ウィンドウ**と呼ばれます。
 
現在実行中のアクティビティ ウィンドウでは、アクティビティ JSON の **WindowStart** および **WindowEnd** システム変数を使用して、アクティビティ ウィンドウに関連付けられた時間間隔にアクセスできます。アクティビティ JSON と、アクティビティに関連付けられたスクリプトで、時系列データを表す入力、出力データセットからデータを選択するなど、さまざまな用途にこれらの変数を使用できます。

**scheduler** プロパティは、データセットの **availability** プロパティと同じサブプロパティをサポートしています。特定の時間オフセットでのスケジュール設定、アクティビティ ウィンドウ間隔の開始時または終了時に処理を合わせるモード設定など、スケジューラに使用できるさまざまなプロパティの詳細については、「[Dataset availability (データセットの可用性)](data-factory-create-datasets.md#Availability)」の記事を参照してください。

## 時系列のデータセットとデータ スライス

時系列データは、連続するシーケンスのデータ ポイントです。通常、一定の間隔で行われる連続的な測定値で構成されます。時系列データの一般的な例として、センサー データ、アプリケーション テレメトリ データなどがあります。

Azure Data Factory を使用すると、アクティビティ実行で時系列データを一括処理できます。通常、入力データを受信してから、出力データを生成する必要がある定期的なパターンがあります。このパターンは、次のようにデータセットの **availability** セクションを指定してモデル化します。

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

アクティビティ実行で使用および生成されるデータの各ユニットは、データ **スライス**と呼ばれます。次の図は、可用性セットが毎時の頻度に設定された入力時系列データセットと出力時系列データセットを含むアクティビティの例です。

![可用性スケジューラ](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

上の図には、入力と出力のデータセットについて、毎時のデータ スライスがあります。図では、処理の準備ができた 3 つの入力スライスがあり、10-11AM 出力スライスを生成する 10-11AM アクティビティ実行が実行中です。

現在生成中のスライスに関連付けられた時間間隔にアクセスするには、データセット JSON で変数 **SliceStart** と **SliceEnd** を使用します。

availability セクションで使用できるさまざまなプロパティの詳細については、「[データセットの作成](data-factory-create-datasets.md)」の記事を参照してください。

## サンプル - Azure SQL のアクティビティ移動データを Azure BLOB にコピーする

「[パイプラインの作成](data-factory-create-pipelines.md)」の記事に記載されている、Azure SQL テーブルのデータを Azure BLOB に毎時コピーするコピー アクティビティのサンプルを変更して、データを一括処理してみましょう。

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


上の例で、アクティビティ スケジュールとデータセットの availability セクションは毎時の頻度に設定されています。このサンプルは、**WindowStart** 変数と **WindowEnd** 変数を利用して、特定のアクティビティ実行に関連するデータを取得して、毎時のフォルダーが作成されるようにパラメーター化された適切な動的 **folderPath** を使用して BLOB に送信する方法を示しています。

午前 8 時から 11 時に 3 つのスライスが実行されると、サンプル Azure テーブルと BLOB のようになります。

Azure SQL 内のデータは、次のようにあるとします。

![サンプル入力](./media/data-factory-scheduling-and-execution/sample-input-data.png)

上のパラメーターをデプロイすると、次のように Azure BLOB が構成されます。

1.	データがあるファイル mypath/2015/1/1/8/Data.<Guid>.txt 

		10002345,334,2,2015-01-01 08:24:00.3130000
		10002345,347,15,2015-01-01 08:24:00.6570000
		10991568,2,7,2015-01-01 08:56:34.5300000

	**注:** <Guid>は、実際の GUID に置き換えられます。ファイル名の例: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	データがあるファイル mypath/2015/1/1/9/Data.<Guid>.txt:

		10002345,334,1,2015-01-01 09:13:00.3900000
		24379245,569,23,2015-01-01 09:25:00.3130000
		16777799,21,115,2015-01-01 09:47:34.3130000
3.	データがないファイル mypath/2015/1/1/10/Data.<Guid>.txt:


## データ スライス、パイプラインと同時スライス実行のためのアクティブな期間

「[パイプラインの作成](data-factory-create-pipelines.md)」の記事では、パイプラインの **start** および **end** プロパティを設定して指定したパイプラインのアクティブな期間という概念について説明しました。
 
パイプラインのアクティブな期間の開始日は過去に設定できます。Data Factory では、過去のすべてのデータ スライスが自動的に計算され (バック フィル)、処理が開始されます。

バック フィルされたデータ スライスでは、並行実行するように構成できます。並行実行するには、「[パイプラインの作成](data-factory-create-pipelines.md)」の記事で説明したように、アクティビティ JSON の **policy** セクションで concurrency プロパティを設定します。

## 再実行が失敗したデータ スライスと自動データ依存関係追跡

わかりやすい画面でスライスの実行を監視できます。詳細については、「[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」の記事を参照してください。

2 つのアクティビティがある次の例を使って説明します。Activity1 では、出力としてスライスを含む時系列データセットが生成されます。この出力は、Activity2 で入力として使用され、最終的な出力の時系列データセットが生成されます。

![失敗したスライス](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

上の図は、3 つの最近のスライスのうち、**Dataset2** の 9-10 AM のスライスを生成するときにエラーが発生したことを示しています。Data Factory では、時系列データセットの依存関係が自動的に追跡され、結果として、9-10 AM ダウンストリーム スライスのアクティビティ実行の開始が保留されます。


Data Factory の監視および管理ツールを使用すると、失敗したスライスの診断ログを詳細に調べ、問題の根本原因を簡単に見つけて修正できます。問題を解決した後は、アクティビティ実行を簡単に開始して失敗したスライスを生成することもできます。再実行を開始する方法と、データ スライスの状態の遷移については、[監視と管理](data-factory-monitor-manage-pipelines.md)に関する記事を参照してください。

再実行が開始され、dataset2 の 9-10AM スライスの準備ができると、下図のように、最終データセットに対して 9-10AM 依存のスライスの実行が開始されます。

![失敗したスライスの再実行](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

依存関係の指定と、複雑なチェーンのアクティビティとデータセットの依存関係の追跡の詳細については、以下のセクションを参照してください。

## 連鎖するアクティビティ
2 つのアクティビティを連鎖させるには、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。このとき、同じパイプラインのアクティビティと、別のパイプラインのアクティビティを指定できます。2 つ目のアクティビティは、1 つ目のアクティビティが正常に完了した後にのみ実行されます。

たとえば、次の場合を考えてみましょう。
 
1.	パイプライン P1 には、外部入力データセット D1 を必要とし、**出力**データセット **D2** を生成するアクティビティ A1 があります。
2.	パイプライン P2 には、データセット **D2** からの**入力**を必要とし、出力データセット D3 を生成するアクティビティ A2 があります。
 
このシナリオでは、外部データが使用可能なときにアクティビティ A1 が実行され、スケジュールされた使用可能頻度に達します。D2 のスケジュールされたスライスが使用可能になると、アクティビティ A2 が実行され、スケジュールされた使用可能頻度に達します。データセット D2 のスライスのいずれかでエラーが発生した場合、スライスが使用可能になるまで、そのスライスに対して A2 は実行されません。

ダイアグラム ビューは、次のようになります。

![2 つのパイプラインでのアクティビティの連鎖](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

同じパイプラインに両方のアクティビティがあるダイアグラム ビューは、次のようになります。

![同じパイプラインでのアクティビティの連鎖](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### 順序指定されたコピー
複数のコピー操作を、順番にまたは順序を指定して 1 つずつ実行できます。パイプラインには、CopyActivity1 と CopyActivity 2 という 2 つのコピー アクティビティがあり、それぞれ入力データと出力データセットは次のようになります。

CopyActivity1: 入力: Dataset1 出力: Dataset2

CopyActivity2: 入力: Dataset2 出力: Dataset4

CopyActivity2 は、CopyActivity1 が正常に実行していて、Dataset2 を使用できた場合にのみ実行します。

上の例で、CopyActivity2 には異なる入力 (たとえば Dataset3) を使用できますが、Dataset2 も CopyActivity2 への入力として指定する必要があるため、このアクティビティは CopyActivity1 が完了するまで実行されません。次に例を示します。

CopyActivity1: 入力: Dataset1 出力: Dataset2

CopyActivity2: 入力: Dataset3、Dataset2 出力: Dataset4

複数の入力を指定すると、データのコピーに使用されるのは最初の入力データセットのみで、他のデータセットは依存関係として使用されます。CopyActivity2 は、次の条件が満たされた場合にのみ実行を開始します。

- CopyActivity1 が正常に完了していて、Dataset2 を使用できる。このデータセットは、データを Dataset4 にコピーする際に使用されません。これは、CopyActivity2 のスケジュールの依存関係としてのみ機能します。   
- Dataset3 を使用できる。このデータセットは、コピー先にコピーされるデータを表します。  



## 頻度が異なるデータセットのモデル化

前述のサンプルでは、入力および出力データセットとアクティビティ スケジュール ウィンドウの頻度は同じでした。シナリオによっては、1 つまたは複数の入力の頻度とは異なる頻度で出力を生成できる必要があります。Data Factory は、このようなシナリオのモデル化をサポートしています。

### サンプル 1: 毎時取得できる入力データの出力レポートを 1 日に 1 回生成する

Azure BLOB で、センサーから毎時取得できる入力測定データがあり、Data Factory の [Hive アクティビティ](data-factory-hive-activity.md)を使用して、1 日の平均、最高値、最小値などの統計情報を含む集計レポートを 1 日に 1 回生成するシナリオがあるとします。

Data Factory を使用してこの処理をモデル化する方法を次に示します。

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

毎日、その日のフォルダーに 1 つの出力ファイルが生成されます。出力の availability は Daily に設定されています (frequency: Day、interval: 1)。


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

Hive スクリプトは、次のように **WindowStart** 変数を使用してパラメーターとして適切な日時情報を受け取ります。Hive スクリプトは、この変数を使用して、その日の適切なフォルダーからデータを読み込み、集計を実行して出力を生成します。

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
		                    "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

データ依存関係の観点から見ると、次のようになります。

![データの依存関係](./media/data-factory-scheduling-and-execution/data-dependency.png)

毎日の出力スライスは、入力データセットの 24 時間のスライスに依存しています。Data Factory では、生成する出力スライスと同じ期間に属する入力データ スライスを特定して、これらの依存関係を自動的に計算します。24 個の入力スライスのいずれかを使用できない場合 (たとえば、アップストリームのそのスライスを生成するアクティビティで偶然処理が発生した場合など)、Data Factory では入力スライスの準備が完了するまで待機してから、毎日のアクティビティ実行を開始します。


### サンプル 2: 式と Data Factory の関数を使用して依存関係を指定する

別のシナリオについて考えてみましょう。2 つのデータセットを処理する Hive アクティビティがあるとします。1 つは毎日新しいデータを取得しますが、もう 1 つは毎週新しいデータを取得します。この例では 2 つの入力を結合し、出力を毎日生成します。
 
出力データ スライスの期間に合わせて入力データ スライスを含めることで、処理対象の適切な入力スライスを Data Factory で自動的に特定する、というこれまでの簡単な方法は、この例では利用できません。

個々のアクティビティ実行で、毎週の入力データセットについて最新の週のデータ スライスが Data Factory に使用されるように指定する方法が必要です。この処理には、次のように Azure Data Factory の関数を使用できます。

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

毎日、その日のフォルダーに 1 つの出力ファイルが生成されます。出力の availability は Daily に設定されています (frequency: Day、interval: 1)。
	
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

Hive アクティビティは 2 つの入力を使用して、出力スライスを毎日生成します。次のように、毎週の入力に対する最新の週の入力スライスに応じて、毎日の出力スライスを指定できます。
	
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
	            "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Azure Data Factory でサポートされている関数およびシステム変数の一覧については、[Data Factory の関数およびシステム変数](data-factory-functions-variables.md)に関する記事を参照してください。

## データ依存関係の詳細情報

アクティビティ実行でデータセット スライスを生成するために、Data Factory では、次の**依存関係モデル**を使用して、アクティビティに使用されるデータセットとアクティビティで生成されるデータセット間の関係を特定します。

出力データセット スライスの生成に必要な入力データセットの時間範囲は、**依存関係期間**と呼ばれます。

依存関係期間内の入力データセットのデータセット スライスを使用できるようになった後にのみ、アクティビティ実行でデータセット スライスが生成されます。つまり、アクティビティ実行で出力データ スライスが生成されるには、依存関係期間を構成するすべての入力スライスが **Ready** 状態である必要があります。

データセット スライス [start、end] を生成するには、データセット スライスを依存関係期間にマップする関数が必要です。この関数は、本質的には、データセット スライスの開始と終了を依存関係期間の開始と終了に変換する式です。より形式的には次のようになります。
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

この f と g は、各アクティビティの依存関係期間の開始と終了を計算するマップ関数です。

上のサンプルに示すように、ほとんどの場合、依存関係期間は、生成するデータ スライスの期間と同じです。このような場合、Data Factory によって、依存関係期間に属する入力スライスが自動的に計算されます。

例: 上の集計例では、出力が毎日生成され、入力データは毎時取得できます。データ スライス期間は 24 時間です。Data Factory によって、この期間に関連する毎時の入力スライスが検出され、入力スライスに応じて出力スライスが作成されます。

また、上記のサンプルのように、入力の 1 つは毎週、出力スライスは毎日生成されるという独自のマップを依存関係期間に指定することもできます。
   
## データの依存関係と検証

必要に応じて、スライス実行を使用する前に、スライス実行で生成されるデータを検証する方法を指定した検証ポリシーをデータセットに定義することができます。詳細については、[データセットの作成](data-factory-create-datasets.md)の記事を参照してください。

この例では、スライスの実行が完了すると、出力スライスの状態は、サブジェクト状態が **Validation** の **Waiting** に変わります。スライスの検証が完了すると、スライスの状態は **Ready** に変わります。
   
データ スライスが生成され、検証に合格しなかった場合、検証に失敗したスライスに依存するダウンストリーム スライスのアクティビティ実行は処理されません。

Data Factory のデータ スライスの多様な状態については、[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)の記事を参照してください。

## 外部データ

データセットを external とマークすると (次の JSON を参照してください)、Azure Data Factory で生成されていないことを示します。この場合、データセット ポリシーに、データセットの検証と再試行ポリシーを記述するパラメーター セットを追加できます。すべてのプロパティの説明については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。

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
パイプライン定義で指定した開始時刻から終了時刻までの間に定期的に (毎時、毎日など) 実行するパイプラインを作成してスケジュール設定することができます。詳細については、「[スケジュール設定のアクティビティ](#scheduling-and-execution)」を参照してください。1 回だけ実行するパイプラインを作成することもできます。これを行うには、以下の JSON サンプルに示すように、パイプライン定義の **pipelineMode** プロパティを **onetime** に設定します。このプロパティの既定値は **scheduled** です。

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
 
- パイプラインの**開始**時刻と**終了**時刻を指定する必要はありません。 
- Data Factory で値が使用されない場合でも、現時点では入力データセットと出力データセット (頻度と間隔) の可用性を指定する必要があります。  
- ダイアグラム ビューには、1 回限りのパイプラインは表示されません。これは設計によるものです。 
- 1 回限りのパイプラインを更新することはできません。1 回限りのパイプラインを複製して名前を変更し、プロパティを更新してデプロイすることで別のパイプラインを作成することができます。 

  




  









 
 












      

  

<!---HONumber=AcomDC_0427_2016-->