<properties 
	pageTitle="Azure Data Factory のパイプラインとアクティビティ | Microsoft Azure" 
	description="Azure Data Factory のパイプラインについて、およびパイプラインを作成してデータを移動および変換して洞察を得るために使用できる情報を生成する方法について説明します" 
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
	ms.date="04/08/2016" 
	ms.author="spelluru"/>

# Azure Data Factory のパイプラインとアクティビティ
この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法を説明します。

> [AZURE.NOTE] この記事では、既に [Azure Data Factory の概要](data-factory-introduction.md)および[データセットの作成](data-factory-create-datasets.md)に関する記事を読んであるものとします。データ ファクトリを実際に作成したことがない場合は、「[最初の Data Factory の作成](data-factory-build-your-first-pipeline.md)」チュートリアルを行うと、この記事をより深く理解することができます。

## パイプラインとは
**パイプラインは、アクティビティの論理的なグループです**。パイプラインは、タスクを実行するユニットにアクティビティをグループ化するために使用されます。パイプラインをより深く理解するには、最初にアクティビティを理解する必要があります。

### アクティビティとは
アクティビティは、データに対して実行するアクションを定義します。各アクティビティは、入力として 0 個以上の[データセット](data-factory-create-datasets.md)を受け取り、出力として 1 つまたは複数のデータセットを生成します。**アクティビティは、Azure Data Factory でのオーケストレーションの単位です。**

たとえば、コピー アクティビティを使用して、データセット間のデータのコピーを調整できます。同様に、Azure HDInsight クラスターに対して Hive クエリを実行する HDInsight Hive アクティビティを使用して、データを変換および分析できます。Azure Data Factory には、[データ変換、分析](data-factory-data-transformation-activities.md)、[データ移動のための広範なアクティビティ](data-factory-data-movement-activities.md)があります。また、カスタム .NET アクティビティを作成して独自のコードを実行することもできます。

次の 2 つのデータ セットを検討してください。

**Azure SQL データセット**

テーブル "MyTable" には列 "timestampcolumn" が含まれ、データがデータベースに挿入されたときの日付と時刻を示します。

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure BLOB データセット**

データは、時間の粒度で特定の日時を反映している BLOB のパスの新しい BLOB に 1 時間ごとにコピーされます。

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


下のパイプラインのコピー アクティビティは、Azure SQL から Azure Blob Storage にデータをコピーします。1 時間ごとに入力データセットとして Azure SQL のテーブルを受け取り、"AzureBlobOutput" データセットによって表される Azure BLOB ストレージにデータを書き込みます。出力データセットも 1 時間ごとです。時間単位でデータをコピーする方法については、「[スケジュールと実行](#scheduling-and-execution)」セクションを参照してください。このパイプラインの有効期間は、"2015-01-01T08:00:00" から "2015-01-01T11:00:00" までの 3 時間です。

**パイプライン:**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
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
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

アクティビティがどういうものかわかったので、パイプラインに戻ります。
 
パイプラインは、アクティビティの論理的なグループです。パイプラインは、タスクを実行するユニットにアクティビティをグループ化するために使用されます。**パイプラインは、アクティビティのデプロイと管理の単位でもあります。** たとえば、論理的に関連性のあるアクティビティを 1 つのパイプラインにして、まとめてアクティブ状態や一時停止状態にできます。

アクティビティ間の依存関係を定義することで、パイプラインのあるアクティビティからの出力データセットを、同じまたは別のパイプラインの別のアクティビティの入力データセットにできます。詳細については、「[スケジュールと実行](#chaining-activities)」を参照してください。

Azure Data Factory でパイプラインを作成する一般的な手順は次のとおりです。

1.	Data Factory を作成します (作成されていない場合)。
2.	データ ストアまたはコンピューティングごとにリンクされたサービスを作成します。
3.	入力データセットと出力データセットを作成します。
4.	上で定義したデータセットに使用するアクティビティでパイプラインを作成します。

![Data Factory のエンティティ](./media/data-factory-create-pipelines/entities.png)

パイプラインを定義する方法について詳しく説明します。

## パイプラインのしくみ  

パイプラインの一般的な構造は次のようになります。

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

**activities** セクションでは、1 つまたは複数のアクティビティを定義できます。各アクティビティには、次のような最上位構造があります。

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

次の表では、アクティビティとパイプラインの JSON 定義で使用されるプロパティを説明します。

タグ | 説明 | 必須
--- | ----------- | --------
name | アクティビティまたはパイプラインの名前です。アクティビティまたはパイプラインが実行するように構成されているアクションを表す名前を指定します<br/><ul><li>最大文字数: 260</li><li>文字、数字、またはアンダースコア (\_) で始める必要があります</li><li>次の文字は使用できません: “.”、“+”、“?”、“/”、“<”、”>”、”*”、”%”、”&”、”:”、”\\”</li></ul> | ○ 
description | アクティビティまたはパイプラインの用途を説明するテキスト | ○ 
type | アクティビティの種類を指定します。アクティビティの種類については、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」および「[データ変換アクティビティ](data-factory-data-transformation-activities.md)」を参照してください。 | ○ 
inputs | アクティビティによって使用される入力テーブル<br/><br/>// 1 つの入力テーブル<br/>"inputs": [ { "name": "inputtable1" } ],<br/><br/>// 2 つの入力テーブル<br/>"inputs": [ { "name": "inputtable1" }, { "name": "inputtable2" } ], | ○ 
outputs | アクティビティによって使用される出力テーブル。// 1 つの出力テーブル<br/>"outputs": [ { "name": "outputtable1" } ],<br/><br/>// 2 つの出力テーブル<br/>"outputs": [ { "name": "outputtable1" }, { "name": "outputtable2" } ], | ○ 
linkedServiceName | アクティビティで使用されるリンクされたサービスの名前。<br/><br/>アクティビティでは、必要なコンピューティング環境にリンクするリンクされたサービスの指定が必要な場合があります。 | ○: HDInsight アクティビティおよび Azure Machine Learning バッチ スコアリング アクティビティの場合<br/><br/>×: 他のすべての場合 
typeProperties | typeProperties セクションのプロパティは、アクティビティの種類に依存します。詳細については、各個別アクティビティの記事を参照してください | × 
policy | アクティビティの実行時の動作に影響を与えるポリシー。指定されていない場合は、既定のポリシーが使用されます。詳細については後で説明します | × 
start | パイプラインの開始日時。[ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)にする必要があります。(例: 2014-10-14T16:32:41Z)。<br/><br/>東部標準時などの現地時間を指定できます (例: 東部標準時の午前 6 時の場合、"2016-02-27T06:00:00**-05:00**")。<br/><br/>start プロパティと end プロパティで、パイプラインの有効期間を指定します。出力スライスは、この有効期間にのみ生成されます。 | ×<br/><br/>end プロパティの値を指定する場合、start プロパティの値も指定する必要があります。<br/><br/>パイプラインを作成するには、開始時間と終了時間の両方が空でもかまいませんが、パイプラインを実行できる有効期間を設定するには、両方の時間を指定する必要があります。パイプラインの作成時に開始時間と終了時間を指定しない場合、後で Set-AzureRmDataFactoryPipelineActivePeriod コマンドレットを使用して設定できます。 
end | パイプラインの終了日時。ISO 形式で指定する必要があります。(例: 2014-10-14T17:32:41Z) <br/><br/>東部標準時などの現地時間を指定できます (例: 東部標準時の午前 6 時の場合、"2016-02-27T06:00:00**-05:00**")。<br/><br/>無期限でパイプラインを実行するには、end プロパティの値として 9999-09-09 を指定します。| × <br/><br/>start プロパティの値を指定する場合、end プロパティの値も指定する必要があります。<br/><br/>**start** プロパティの注意事項を参照してください。 
isPaused | true に設定すると、パイプラインは実行されません。既定値 = false。このプロパティを使用して有効または無効にできます。 | × 
scheduler | アクティビティのスケジュールを定義します。サブプロパティは、[データセットの availability プロパティ](data-factory-create-datasets.md#Availability)にあるサブプロパティと同じです。| × | 
| pipelineMode | パイプラインの実行のスケジューリングを行うためのメソッドです。使用可能な値: "Scheduled" (既定)、"Onetime"。<br/><br/>"Scheduled" は、パイプラインがアクティブな期間 (開始時刻と終了時刻) に応じて、指定された間隔で実行されることを意味します。"Onetime" はパイプラインが 1回だけ実行されることを意味します。現時点では、作成された Onetime パイプラインを変更または更新することはできません。Onetime パイプラインの設定について詳しくは、「[Onetime パイプライン](data-factory-scheduling-and-execution.md#onetime-pipeline)」を参照してください。 | × | 
|expirationTime |パイプラインの作成後に、パイプラインが有効であり、プロビジョニングされた状態である必要がある時間。パイプラインは、アクティブ、エラー、または保留中の実行がない限り、有効期限に達すると自動的に削除されます。 | × | 
| datasets | パイプラインで定義されたアクティビティで使用されるデータセットの一覧。これは、このパイプラインに固有の、Data Factory 内で定義されていないデータセットを定義するために使用できます。このパイプライン内で定義されているデータセットは、このパイプラインでのみ使用でき、共有することはできません。詳細については、「[範囲指定されたデータセット](data-factory-create-datasets.md#scoped-datasets)」を参照してください。 | × | 
 

### アクティビティの種類
Azure Data Factory には、[データ移動](data-factory-data-movement-activities.md)および[データ変換](data-factory-data-transformation-activities.md)のための広範なアクティビティがあります。

### ポリシー
ポリシーはアクティビティの実行時の動作に影響します。具体的には、テーブルのスライスがいつ処理されるかです。次の表で詳細に説明します。

プロパティ | 使用できる値 | 既定値 | 説明
-------- | ----------- | -------------- | ---------------
同時実行 | 整数<br/><br/>最大値: 10 | 1 | アクティビティの同時実行の数。<br/><br/>各種のスライスで実行できる並列アクティビティ実行の数を決定します。たとえば、アクティビティが大量のデータを処理する必要がある場合、同時実行の数を大きくするとデータ処理が速くなります。 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | 処理されるデータ スライスの順序を決定します。<br/><br/>たとえば、2 個のスライス (午後 4 時と午後 5 時の実行) があり、どちらも実行が保留されているとします。executionPriorityOrder を NewestFirst に設定すると、午後 5 時のスライスが最初に処理されます。同様に、executionPriorityORder を OldestFIrst に設定すると、午後 4 時のスライスが処理されます。 
retry | 整数<br/><br/>最大値は 10 | 3 | スライスのデータ処理が失敗としてマークされるまでの再試行回数。データ スライスのアクティビティの実行は、指定された再試行回数まで再試行されます。再試行は、障害発生後にできるだけ早く行われます。
timeout | TimeSpan | 00:00:00 | アクティビティのタイムアウト。例: 00:10:00 (タイムアウトが 10 分であることを意味します)<br/><br/>値が指定されていない場合、または値が 0 の場合は、タイムアウトは無期限です。<br/><br/>スライスのデータ処理時間がタイムアウト値を超えた場合、処理はキャンセルされて、システムは処理の再試行を試みます。再試行の回数は、retry プロパティで指定します。タイムアウトが発生すると、ステータスは TimedOut になります。
delay | TimeSpan | 00:00:00 | スライスのデータ処理を開始する前の遅延時間を指定します。<br/><br/>データ スライスのアクティビティの実行は、予想実行時刻を Delay だけ過ぎてから開始します。<br/><br/>例: 00:10:00 (10 分の遅延を意味します)
longRetry | 整数<br/><br/>最大値: 10 | 1 | スライスの実行が失敗になるまでの、長い再試行の回数。<br/><br/>longRetry の試行は longRetryInterval の間隔で行われます。再試行間隔の時間を指定する必要がある場合は、longRetry を使用します。Retry と longRetry の両方を指定すると、各 longRetry に Retry が含まれ、最大再試行回数は Retry * longRetry になります。<br/><br/>たとえば、次のようなアクティビティ ポリシーがあります。<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>実行するスライスは 1 つだけ (ステータスは Waiting)、アクティビティ実行は毎回失敗するとします。最初に 3 つの連続する試行があります。試行するたびには、スライスの状態は Retry になります。最初の 3 つの試行が終わると、スライスの状態は LongRetry 。<br/><br/>1 時間 (つまり longRetryInteval の値) の後、再度 3 回連続して試行されます。その後、スライスの状態は Failed になり、それ以上再試行は行われません。したがって、全部で 6 回試行されます。<br/><br/>注: いずれかの実行が成功すると、スライスの状態は Ready になり、それ以上再試行は行われません。<br/><br/>longRetry は、依存するデータがいつ到着するかわからない場合、またはデータ処理が行われる環境全体が当てにならない場合などに使用します。このような場合、連続して再試行しても意味がなく、時間をおくと成功することがあります。<br/><br/>注意: longRetry または longRetryInterval に大きい値を設定しないでください。一般に、高い値は、気付かれていないシステムの問題が他にあることを意味します。 
longRetryInterval | TimeSpan | 00:00:00 | 長い再試行の間の遅延 

## 連鎖するアクティビティ
パイプラインに複数のアクティビティがあり、相互に依存していない (アクティビティの出力が別のアクティビティの入力ではない) 場合は、アクティビティの入力データ スライスの準備ができれば、アクティビティが並列実行する可能性があります。

2 つのアクティビティを連鎖させるには、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。このとき、同じパイプラインのアクティビティと、別のパイプラインのアクティビティを指定できます。2 つ目のアクティビティは、1 つ目のアクティビティが正常に完了した後にのみ実行されます。

たとえば、次の場合を考えてみましょう。
 
1.	パイプライン P1 に、外部入力データセット D1 を必要とし、**出力**データセット **D2** を生成するアクティビティ A1 があります。
2.	パイプライン P2 に、データセット **D2** からの**入力**を必要とし、出力データセット D3 を生成するアクティビティ A2 があります。
 
このシナリオでは、外部データが使用可能なときにアクティビティ A1 が実行され、スケジュールされた使用可能頻度に達します。D2 のスケジュールされたスライスが使用可能になると、アクティビティ A2 が実行され、スケジュールされた使用可能頻度に達します。データセット D2 のスライスのいずれかでエラーが発生した場合、スライスが使用可能になるまで、そのスライスに対して A2 は実行されません。

ダイアグラム ビューは、次のようになります。

![2 つのパイプラインでアクティビティを連鎖させる](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

同じパイプラインに両方のアクティビティがあるダイアグラム ビューは、次のようになります。

![同じパイプラインでアクティビティを連鎖させる](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

## スケジュールと実行
パイプラインとアクティビティについては理解できました。また、Azure Data Factory でのアクティビティの定義と概要も説明しました。次に、実行方法を説明します。

パイプラインは、開始時刻と終了時刻の間のみアクティブです。開始時刻より前または終了時刻より後には実行されません。パイプラインは、一時停止している場合、その開始時刻と終了時刻に関係なく実行されません。パイプラインを実行するには、一時停止しないでください。実際には、実行されるのはパイプラインではありません。実行されるのはパイプライン内のアクティビティです。ただし、アクティビティはパイプラインの全体的なコンテキストで実行されます。

Azure Data Factory でのスケジュールと実行の方法については、「[スケジュールと実行](data-factory-scheduling-and-execution.md)」を参照してください。

### スライスの並列処理
実行時におけるアクティビティの複数のインスタンスで複数のスライスが並列処理されるように、アクティビティの JSON 定義での**同時実行**の値を 1 より大きい値に設定します。これは、過去からのバックフィルされたスライスを処理する場合に非常に役立ちます。


## パイプラインの作成と管理
Azure Data Factory は、(1 つ以上のアクティビティを含む) パイプラインを作成およびデプロイするさまざまなメカニズムを提供します。

### Azure ポータルの使用

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. パイプラインを作成する Azure Data Factory のインスタンスに移動します。
3. **[概要]** レンズの **[作成とデプロイ]** タイルをクリックします。 
 
	![[作成とデプロイ] タイル](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. コマンド バーの **[新しいパイプライン]** をクリックします。

	![[新しいパイプライン] ボタン](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. パイプライン JSON テンプレートを含むエディター ウィンドウが表示されます。

	![パイプライン エディター](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. パイプラインの作成が完了したら、コマンド バーの **[デプロイ]** をクリックして、パイプラインをデプロイします。

	**注:** Azure Data Factory サービスはデプロイ中にいくつかの検証チェックを実行し、一般的な問題を修正します。エラーがある場合、対応する情報が表示されます。修正処理を実行し、作成したパイプラインを再デプロイします。エディターを使用してパイプラインを更新および削除できます。

パイプラインを備えた Data Factory を作成するためのエンド ツー エンド チュートリアルについては、「[Data Factory Editor を使用した初めての Azure Data Factory パイプラインの作成](data-factory-build-your-first-pipeline-using-editor.md)」を参照してください。

### Visual Studio プラグインの使用
Visual Studio を使用して、パイプラインを作成して Azure Data Factory にデプロイできます。情報が必要な場合、パイプラインを備えた Data Factory を作成するためのエンド ツー エンド チュートリアルについては、「[Visual Studio を使用した初めての Azure Data Factory パイプラインの作成](data-factory-build-your-first-pipeline-using-vs.md)」を参照してください。


### Azure PowerShell の使用
Azure PowerShell を使用して、Azure Data Factory にパイプラインを作成できます。たとえば、c:\\DPWikisample.json のファイルでパイプライン JSON を定義しておきます。次の例のように Azure Data Factory インスタンスにそれをアップロードできます。

	New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

パイプラインを備えた Data Factory を作成するためのエンド ツー エンド チュートリアルについては、「[Azure PowerShell を使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-powershell.md)」を参照してください。

### .NET SDK の使用
.NET SDK を使用してパイプラインを作成およびデプロイすることもできます。このメカニズムを利用すると、プログラムでパイプラインを作成できます。詳細については、「[Data Factory をプログラムで作成、管理、監視する](data-factory-create-data-factories-programmatically.md)」を参照してください。


### ARM (Azure Resource Manager) テンプレートの使用
Azure Resource Manager (ARM) テンプレートを使用してパイプラインを作成およびデプロイすることができます。詳細については、「[Azure Resource Manager を使用した初めての Azure Data Factory パイプラインの作成](data-factory-build-your-first-pipeline-using-arm.md)」を参照してください。

### REST API の使用
REST API を使用してパイプラインを作成およびデプロイすることもできます。このメカニズムを利用すると、プログラムでパイプラインを作成できます。詳細については、「[パイプラインを作成または更新する](https://msdn.microsoft.com/library/azure/dn906741.aspx)」を参照してください。


## 管理と監視  
パイプラインをデプロイした後は、パイプライン、スライス、実行を管理および監視できます。詳細については、「[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」を参照してください。

## 次のステップ

- [Azure Data Factory でのスケジュールと実行](data-factory-scheduling-and-execution.md)について理解してください。  
- Azure Data Factory での[データの移動](data-factory-data-movement-activities.md)および[データ変換機能](data-factory-data-transformation-activities.md)について読んでください。
- [Azure Data Factory での管理と監視](data-factory-monitor-manage-pipelines.md)について理解してください。
- [初めてのパイプラインをビルドしてデプロイしてください](data-factory-build-your-first-pipeline.md)。 


 

   













 
 


 

 

<!-----HONumber=AcomDC_0427_2016-->