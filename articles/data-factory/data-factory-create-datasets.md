<properties 
	pageTitle="Azure Data Factory のデータセット | Microsoft Azure" 
	description="Azure Data Factory のデータセットを理解し、その作成方法について学習します。" 
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

# Azure Data Factory のデータセット
Azure Data Factory のデータセットは、パイプライン内のアクティビティの入力または出力として使用するデータへの名前付きの参照/ポインターです。データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。

データ ファクトリを作成する際に、データ ストアをデータ ファクトリにリンクする、リンクされたサービスを作成します。**リンクされたサービス**は、Azure Data Factory がデータ ストアに**接続する**ために必要な情報を定義します (Azure ストレージ アカウント、Azure SQL Database など)。また、リンクされたサービスは、データ ストアにアクセスするためのメカニズムを定義します (アドレス、プロトコル、認証スキームなど)。

Data Factory の**データセット**は、このデータ ストア内部のデータ構造を表します (たとえば、BLOB コンテナーや SQL テーブル)。このデータ構造は、パイプライン内のアクティビティの入力または出力として使用できます。データセットは作成後、パイプライン内のアクティビティで使用できます。たとえば、データセットはコピー アクティビティ/HDInsightHive アクティビティの入力/出力データセットとして使用できます。

> [AZURE.NOTE] Azure Data Factory を初めて使用する場合は、Azure Data Factory サービス一般について説明した [Azure Data Factory の概要](data-factory-introduction.md)に関する記事と、最初のデータ ファクトリを作成するためのチュートリアル「[初めての Data Factory の作成](data-factory-build-your-first-pipeline.md)」を参照してください。これらの 2 つの記事には、この記事をより深く理解するために必要な背景情報が示されています。

## データセットの定義
Azure Data Factory のデータセットは次のように定義されます。


	{
	    "name": "<name of dataset>",
	    "properties": {
	        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
			"external": <boolean flag to indicate external data. only for input datasets>,
	        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
	        "structure": [
	            {
	                "name": "<Name of the column>",
	                "type": "<Name of the type>"
	            }
	        ],
	        "typeProperties": {
	            "<type specific property>": "<value>",
				"<type specific property 2>": "<value 2>",
	        },
	        "availability": {
	            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
	            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
	        },
	       "policy": 
	        {      
	        }
	    }
	}

次の表では、上記の JSON のプロパティについて説明します。

| プロパティ | 説明 | 必須 | 既定値 |
| -------- | ----------- | -------- | ------- |
| name | データセットの名前。名前付け規則については、「[Azure Data Factory - 名前付け規則](data-factory-naming-rules.md)」を参照してください。 | はい | 該当なし |
| type | データセットの型。Azure Data Factory でサポートされている型のいずれかを指定します (たとえば、AzureBlob、AzureSqlTable)。<br/><br/>詳細については、「[データセットの型](#Type)」を参照してください。 | はい | 該当なし |
| structure | データセットのスキーマ。<br/><br/>詳細については、「[データセット構造](#Structure)」セクションを参照してください。 | いいえ、できません。 | 該当なし |
| typeProperties | 選択された型に対応するプロパティ。サポートされている型とそのプロパティについては、「[データセットの型](#Type)」セクションを参照してください。 | あり | 該当なし |
| 外部 | データセットをデータ ファクトリ パイプラインによって明示的に生成するかどうかを指定するブール型のフラグ。 | いいえ | false | 
| availability | データセット生成の処理時間枠またはスライシング モデルを定義します。<br/><br/>詳細については、「[データセットの可用性](#Availability)」トピックを参照してください。<br/><br/>データセット スライシング モデルの詳細については、[スケジュールと実行](data-factory-scheduling-and-execution.md)に関する記事を参照してください。 | あり | 該当なし
| policy | データセット スライスで満たさなければならない基準または条件を定義します。<br/><br/>詳細については、「[データセット ポリシー](#Policy)」トピックを参照してください。 | いいえ | 該当なし |

### 例

以下は **Azure SQL データベース**に **MyTable** という名前のテーブルを表示するデータセットの例です。

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

以下の点に注意してください。

- type が AzureSqlTable に設定されています。
- (AzureSqlTable 型に固有の) 型プロパティ tableName が MyTable に設定されています。
- linkedServiceName は、型が AzureSqlDatabase であるリンクされたサービスを参照します。次のリンクされたサービスの定義を参照してください。 
- availability の frequency が Day に、interval が 1 に設定されています。これはスライスが毎日生成されることを意味します。  

AzureSqlLinkedService は次のように定義されます。

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}

上記の JSON では、

- type が AzureSqlDatabase に設定されています。
- 型プロパティ connectionString は、Azure SQL データベースに接続される情報を指定します。  


ご覧のとおり、リンクされたサービスは Azure SQL データベースに接続する方法を定義します。また、データセットは、データ ファクトリの入力/出力として使用されるテーブルを定義します。[パイプライン](data-factory-create-pipelines.md) JSON 内の activity セクションは、データセットが入力データセットとして使用されるか、出力データセットとして使用されるかを指定します。


> [AZURE.IMPORTANT] データセットは Azure Data Factory で作成されている場合を除き、**external** とマークされます。これは通常、パイプライン内の最初のアクティビティの入力に適用されます。

## <a name="Type"></a> データセットの型
サポートされるデータ ソースとデータセットの型が並んでいます。データセットの型と構成については、「[データ移動アクティビティ](data-factory-data-movement-activities.md#supported-data-stores)」という記事のトピックを参照してください。たとえば、Azure SQL データベースのデータを使用している場合は、サポートされるデータ ストアの一覧にある Azure SQL Database をクリックします。これで、Azure SQL Database をソース データ ストアまたはシンク データ ストアとして使用する方法の詳細を確認できます。

## <a name="Structure"></a>データセット構造
**structure** セクションでは、データセットのスキーマを定義します。このセクションには列の名前とデータ型のコレクションが含まれています。次の例では、データセットに slicetimestamp、projectname、pageviews の 3 つの列があります。各列の型は、String、String、Decimal です。

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Availability"></a> データセットの可用性
データセットの **availability** セクションでは、データセットの処理時間枠 (時間単位、日単位、週単位など) またはスライシング モデルを定義します。データセットのスライシングおよび依存性モデルの詳細については、「[Data Factory を使用したスケジュール設定と実行](data-factory-scheduling-and-execution.md)」をご覧ください。

次の availability セクションでは、出力データセットの場合は 1 時間ごとにデータセットが生成され、入力データセットの場合は 1 時間ごとにデータセットが使用可能であるように指定されています。

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 1	
	}

次の表では、availability セクションで使用できるプロパティについて説明します。

| プロパティ | 説明 | 必須 | 既定値 |
| -------- | ----------- | -------- | ------- |
| frequency | データセット スライス生成の時間単位を指定します。<br/><br/>**サポートされている frequency**: Minute、Hour、Day、Week、Month。 | あり | 該当なし |
| interval | frequency の乗数を指定します。<br/><br/>"frequency x interval" により、スライスが生成される頻度が決まります。<br/><br/>データセットを時間単位でスライスする必要がある場合は、**frequency** を **Hour** に、**interval** を **1** に設定します。<br/><br/>**注:** frequency に Minute を指定する場合は、interval を 15 以上に設定することをお勧めします。 | あり | 該当なし |
| style | スライスを間隔の始め/終わりに生成するかどうかを指定します。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>ｆrequency を Month に設定し、style を EndOfInterval に設定すると、スライスは月の最終日に生成されます。style を StartOfInterval に設定すると、スライスは月の最初の日に生成されます。<br/><br/>ｆrequency を Day に設定し、style を EndOfInterval に設定すると、スライスは 1 日の最後の 1 時間に生成されます。<br/><br/>frequency を Hour に設定し、style を EndOfInterval に設定すると、スライスは時間の終わりに生成されます。たとえば、午後 1 時 ～ 午後 2 時のスライスの場合、午後 2 時にスライスが生成されます。 | いいえ | EndOfInterval |
| anchorDateTime | データセット スライスの境界を計算するためにスケジューラによって使用される時間の絶対位置を定義します。<br/><br/>**注:** AnchorDateTime に頻度より細かい日付部分が含まれている場合、その部分は無視されます。<br/><br/>たとえば、**間隔**が**時間単位** (frequency が Hour で interval が 1) で、**AnchorDateTime** に**分と秒**が含まれる場合、AnchorDateTime の**分と秒**部分は無視されます。 | いいえ | 01/01/0001 |
| offset | すべてのデータセット スライスの開始と終了がシフトされる時間帯です。<br/><br/>**注:** anchorDateTime と offset の両方が指定されている場合、結果的にシフトが結合されます。 | いいえ | 該当なし |

### offset 例

既定の設定である真夜中ではなく、毎日午前 6 時にスライスを開始する例です。

	"availability":
	{
		"frequency": "Day",
		"interval": 1,
		"offset": "06:00:00"
	}

**frequency** が **Month** に設定され、**interval** が **1** (月 1 回) に設定されている場合、毎月 9 日の午前 6 時にスライスを生成するには、offset を "09.06:00:00" に設定します。これは UTC 時間であることに注意してください。

12 か月スケジュールの場合 (頻度 = 月、間隔 = 12)、「オフセット: 60.00:00:00」は毎年の 3 月 1 日または 2 日 (スタイルが StartOfInterval の場合、年の初めから 60 日後であり、うるう年かどうかにより 1 日または 2 日になる) を意味します。

### anchorDateTime の例

**例:** 2007-04-19T08:00:00 に開始する 23 時間のデータセット スライス

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 23,	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}

### offset/style の例

毎月、特定の日時 (たとえば、毎月 3 日の午前 8 時) にデータセットが必要な場合は、**offset** タグを使用して、実行する日時を設定できます。

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}


## <a name="Policy"></a>データセット ポリシー

データセット定義の **policy** セクションでは、データセット スライスで満たさなければならない基準または条件を定義します。

### 検証ポリシー

| ポリシー名 | 説明 | 適用先 | 必須 | 既定値 |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | **Azure BLOB** のデータが最小サイズ要件 (MB 単位) を満たすことを検証します。 | Azure BLOB | いいえ | 該当なし |
|minimumRows | **Azure SQL データベース**または **Azure テーブル**のデータに最小行数が含まれていることを検証します。 | <ul><li>Azure SQL Database</li><li>Azure テーブル</li></ul> | いいえ | 該当なし

#### 例

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### 外部データセット

外部データセットはデータ ファクトリのパイプライン実行で生成されないデータセットです。データセットが **external** としてマークされている場合は、**ExternalData** ポリシーを定義することで、データセット スライス可用性の動作を変更できます。

データセットは Azure Data Factory で作成されている場合を除き、**external** とマークされます。これは通常、パイプライン内の最初のアクティビティの入力に適用されます (アクティビティまたはパイプラインの連鎖が利用されていない場合を除く)。

| 名前 | 説明 | 必須 | 既定値 |
| ---- | ----------- | -------- | -------------- |
| dataDelay | 特定のスライスの外部データの可用性チェックを遅らせる時間。たとえば、データを時間単位で利用する場合、外部データが実際に利用できることと、該当するスライスが "準備完了" であることの確認を dataDelay で遅らせることができます。<br/><br/>現在時刻にのみ適用されます。たとえば、現在午後 1 時で、この値が 10 分の場合、検証は午後 1 時 10 分に開始されます。<br/><br/>この設定は過去のスライスには影響しません。スライスの終了時刻と dataDelay を足したものが "現在" より前になるスライスは遅延なく処理されます。<br/><br/>23 時間 59 分を超える時間は、day.hours:minutes:seconds 形式で指定する必要があります。たとえば、24 時間を指定するには、24:00:00 ではなく、1.00:00:00 を使用します。24:00:00 を使用した場合は、24 日間 (24.00:00:00) として処理されます。1 日と 4 時間の場合は 1:04:00:00 と指定します。 | いいえ | 0 |
| retryInterval | エラーと次の再試行の間の待機時間です。現在の時間に適用されます。前の試行に失敗した場合、最後の試行からこの時間が経過するまで待機します。<br/><br/>現在時刻が午後 1 時の場合に最初の試行を開始したとします。最初の検証チェックを完了するための時間が 1 分のとき、操作に失敗した場合、次の再試行は「1:00pm + 1 分 (チェック時間) + 1 分 (再試行間隔) = 1:02pm」になります。<br/><br/>過去のスライスの場合、遅延はありません。再試行はすぐに実行されます。 | いいえ | 00:01:00 (1 分) | 
| retryTimeout | 各再試行のタイムアウト。<br/><br/>これが 10 分に設定されている場合、検証を 10 分以内に完了する必要があります。検証に 10 分以上の時間がかかった場合、再試行はタイムアウトします。<br/><br/>検証のすべての試行がタイムアウトした場合、スライスに TimedOut のマークが付きます。 | いいえ | 00:10:00 (10 分) |
| maximumRetry | 外部データの可用性の確認回数です。許容される最大値は 10 です。 | いいえ | 3 | 

## 範囲指定されたデータセット
**datasets** プロパティを使用することで、対象となるパイプラインを指定したデータセットを作成できます。これらのデータセットは、そのパイプライン内のアクティビティでのみ使用できます。他のパイプラインのアクティビティでは使用できません。次の例では、パイプラインと、そのパイプライン内で使用する 2 つのデータセット (InputDataset-rdc と OutputDataset-rdc) が定義されています。

	{
	    "name": "CopyPipeline-rdc",
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
	                        "name": "InputDataset-rdc"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset-rdc"
	                    }
	                ],
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1,
	                    "style": "StartOfInterval"
	                },
	                "name": "CopyActivity-0"
	            }
	        ],
	        "start": "2016-02-28T00:00:00Z",
	        "end": "2016-02-28T00:00:00Z",
	        "isPaused": false,
	        "pipelineMode": "OneTime",
	        "expirationTime": "15.00:00:00",
	        "datasets": [
	            {
	                "name": "InputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "InputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/input",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": true,
	                    "policy": {}
	                }
	            },
	            {
	                "name": "OutputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "OutputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/output",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": false,
	                    "policy": {}
	                }
	            }
	        ]
	    }
	}

<!---HONumber=AcomDC_0427_2016-->