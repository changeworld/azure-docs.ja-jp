<properties 
	pageTitle="Azure Data Factory でのデータセットの作成 | Microsoft Azure" 
	description="Azure Data Factory でデータセットを作成する方法について説明し、offset、anchorDateTime などのプロパティを使用する例を紹介します。"
    keywords="データセットの作成, データセットの例, offset の例"
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
	ms.date="09/13/2016" 
	ms.author="spelluru"/>

# Azure Data Factory のデータセット
この記事では、Azure Data Factory のデータセットについて説明し、offset、anchorDateTime、offset/style などを使用したデータベースの例を紹介します。

データセットを作成するときは、処理するデータへのポインターを作成します。データは、アクティビティ内で処理 (入力/出力) されます。アクティビティは、パイプラインに含まれています。入力データセットはパイプラインのアクティビティの入力を表し、出力データセットはアクティビティの出力を表します。

データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。作成したデータセットは、パイプライン内のアクティビティで使用できます。たとえば、データセットはコピー アクティビティまたは HDInsightHive アクティビティの入力/出力データセットとして使用できます。Azure Portal では、すべてのパイプラインとデータの入出力の視覚的なレイアウトが表示されます。すべてのソースにわたって、パイプラインのすべてのリレーションシップと依存関係がひとめでわかるため、データの入力元や出力先を常に把握できます。

Azure Data Factory では、パイプライン内でコピー アクティビティを使用することで、データセットからデータを取得できます。

> [AZURE.NOTE] Azure Data Factory の使用経験がない場合、Azure Data Factory サービスの概要については、「[Azure Data Factory サービスの概要](data-factory-introduction.md)」を参照してください。最初のデータ ファクトリを作成するためのチュートリアルについては、「[初めての Data Factory の作成](data-factory-build-your-first-pipeline.md)」を参照してください。これらの 2 つの記事には、この記事をより深く理解するために必要な背景情報が示されています。

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
| typeProperties | 選択された型に対応するプロパティ。サポートされている型とそのプロパティの詳細については、「[データセットの型](#Type)」セクションを参照してください。 | はい | 該当なし |
| 外部 | データセットをデータ ファクトリ パイプラインによって明示的に生成するかどうかを指定するブール型のフラグ。 | なし | false | 
| availability | データセット生成の処理時間枠またはスライシング モデルを定義します。<br/><br/>詳細については、「[データセットの可用性](#Availability)」セクションを参照してください。<br/><br/>データセットのスライシング モデルの詳細については、[スケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事を参照してください。 | はい | 該当なし
| policy | データセット スライスで満たさなければならない基準または条件を定義します。<br/><br/>詳細については、「[データセット ポリシー](#Policy)」セクションを参照してください。 | なし | 該当なし |

## データセットの例
以下の例では、データセットは **Azure SQL データベース**にある **MyTable** という名前のテーブルを示します。

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


ご覧のように、リンクされたサービスによって、Azure SQL Database に接続する方法が定義されます。データセットは、どのテーブルがパイプライン内のアクティビティの入力/出力として使用されるかを定義します。[パイプライン](data-factory-create-pipelines.md) JSON 内の activity セクションは、データセットが入力データセットとして使用されるか、出力データセットとして使用されるかを指定します。


> [AZURE.IMPORTANT] データセットは Azure Data Factory で作成されている場合を除き、**external** とマークされます。この設定は通常、パイプライン内の最初のアクティビティの入力に適用されます。

## <a name="Type"></a> データセットの型
サポートされるデータ ソースとデータセットの型が並んでいます。データセットの型と構成については、[データ移動アクティビティ](data-factory-data-movement-activities.md#supported-data-stores)に関する記事で参照されているトピックを参照してください。たとえば、Azure SQL データベースのデータを使用している場合は、サポートされるデータ ストアの一覧にある Azure SQL Database をクリックして、詳細を確認してください。

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

次の availability セクションでは、出力データセットが 1 時間ごとに生成されるか、入力データセットが 1 時間ごとに使用可能となるように指定されます。

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 1	
	}

次の表では、availability セクションで使用できるプロパティについて説明します。

| プロパティ | 説明 | 必須 | 既定値 |
| -------- | ----------- | -------- | ------- |
| frequency | データセット スライス生成の時間単位を指定します。<br/><br/>**サポートされている frequency**: Minute、Hour、Day、Week、Month。 | はい | 該当なし |
| interval | frequency の乗数を指定します。<br/><br/>"frequency x interval" により、スライスが生成される頻度が決まります。<br/><br/>データセットを時間単位でスライスする必要がある場合は、**frequency** を **Hour** に、**interval** を **1** に設定します。<br/><br/>**注:** frequency に Minute を指定する場合は、interval を 15 以上に設定することをお勧めします。 | はい | 該当なし |
| style | スライスを間隔の始めまたは終わりに生成するかどうかを指定します。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>frequency を Month に設定し、style を EndOfInterval に設定すると、スライスは月の最終日に生成されます。style を StartOfInterval に設定すると、スライスは月の最初の日に生成されます。<br/><br/>frequency を Day に設定し、style を EndOfInterval に設定すると、スライスは 1 日の最後の 1 時間に生成されます。<br/><br/>frequency を Hour に設定し、style を EndOfInterval に設定すると、スライスは時間の終わりに生成されます。たとえば、午後 1 時 ～ 午後 2 時のスライスの場合、午後 2 時にスライスが生成されます。 | なし | EndOfInterval |
| anchorDateTime | データセット スライスの境界を計算するためにスケジューラによって使用される時間の絶対位置を定義します。<br/><br/>**注:** AnchorDateTime に頻度より細かい日付部分が含まれている場合、その部分は無視されます。<br/><br/>たとえば、**間隔**が**時間単位** (frequency が Hour で interval が 1) で、**AnchorDateTime** に**分と秒**が含まれる場合、AnchorDateTime の**分と秒**部分は無視されます。 | なし | 01/01/0001 |
| offset | すべてのデータセット スライスの開始と終了がシフトされる時間帯です。<br/><br/>**注:** anchorDateTime と offset の両方が指定されている場合、結果的にシフトが結合されます。 | なし | 該当なし |

### offset 例

既定の設定である真夜中ではなく、毎日午前 6 時にスライスを開始する例です。

	"availability":
	{
		"frequency": "Day",
		"interval": 1,
		"offset": "06:00:00"
	}

**frequency** が **Day** に、**interval** が **1** (1 日に 1 回) に設定され、既定の午前 12 時ではなく、午前 6 時にスライスが生成されるように設定されています。この時刻は UTC 時間であることに注意してください。

## anchorDateTime の例

**例:** 2007-04-19T08:00:00 に開始する 23 時間のデータセット スライス

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 23,	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}

## offset/style の例

毎月、特定の日時 (たとえば、毎月 3 日の午前 8 時) にデータセットが必要な場合は、**offset** タグを使用して、実行する日時を設定します。

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

| ポリシー名 | Description | 適用先 | 必須 | 既定値 |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | **Azure BLOB** のデータが最小サイズ要件 (MB 単位) を満たすことを検証します。 | Azure BLOB | なし | 該当なし |
|minimumRows | **Azure SQL Database** または **Azure テーブル**のデータに最小行数が含まれていることを検証します。 | <ul><li>Azure SQL Database</li><li>Azure テーブル</li></ul> | なし | 該当なし

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

データセットは Azure Data Factory で作成されている場合を除き、**external** とマークされます。この設定は通常、パイプライン内の最初のアクティビティの入力に適用されます (アクティビティまたはパイプラインの連鎖が使用されている場合を除く)。

| 名前 | Description | 必須 | 既定値 |
| ---- | ----------- | -------- | -------------- |
| dataDelay | 特定のスライスの外部データの可用性チェックを遅らせる時間。データが 1 時間ごとに使用できると想定されていて、外部データを確認するチェックが利用可能であり、対応するスライスが準備完了の場合、dataDelay を使用して可用性チェックを延期できます。<br/><br/>これは、現在の時刻にのみ適用されます。たとえば、現在時刻が午後 1時 00分 PM でこの値が 10 分の場合、検証は午後 1 時 10 分に開始されます。<br/><br/>この設定は、過去のスライス ([スライス終了時間] + dataDelay < 現在時刻) には影響しません。過去のスライスは遅延なく処理されます。<br/><br/>23:59 よりも大きい時間の場合、day.hours:minutes:seconds 形式で指定する必要があります。たとえば、24 時間を指定するには、24:00:00 ではなく、1.00:00:00 を使用します。24:00:00 を使用した場合は、24 日間 (24.00:00:00) として処理されます。1 日と 4 時間の場合は 1:04:00:00 と指定します。 | なし | 0 |
| retryInterval | エラーと次の再試行の間の待機時間です。現在の時間に適用されます。前の試行に失敗した場合、最後の試行からこの時間が経過するまで待機します。<br/><br/>現在時刻が午後 1 時の場合に最初の試行を開始したとします。最初の検証チェックを完了するための時間が 1 分のとき、操作に失敗した場合、次の再試行は「1:00pm + 1 分 (チェック時間) + 1 分 (再試行間隔) = 1:02pm」になります。<br/><br/>過去のスライスの場合、遅延はありません。再試行は直ちに行われます。 | なし | 00:01:00 (1 分) | 
| retryTimeout | 各再試行のタイムアウト。<br/><br/>このプロパティが 10 分に設定されている場合、検証を 10 分以内に完了する必要があります。検証に 10 分以上の時間がかかった場合、再試行はタイムアウトします。<br/><br/>検証のすべての試行がタイムアウトした場合、スライスに TimedOut のマークが付きます。 | なし | 00:10:00 (10 分) |
| maximumRetry | 外部データの可用性の確認回数です。許容される最大値は 10 です。 | なし | 3 | 

## 範囲指定されたデータセット
**datasets** プロパティを使用すると、対象となるパイプラインを指定したデータセットを作成できます。これらのデータセットは、そのパイプライン内のアクティビティでのみ使用できます。他のパイプラインのアクティビティでは使用できません。次の例では、パイプラインと、そのパイプライン内で使用する 2 つのデータセット (InputDataset-rdc と OutputDataset-rdc) が定義されています。

> [AZURE.IMPORTANT] 範囲指定されたデータセットは、1 回限りのパイプライン (**pipelineMode** が **OneTime** に設定されたパイプライン) でのみサポートされます。詳細については、「[1 回限りのパイプライン](data-factory-scheduling-and-execution.md#onetime-pipeline)」を参照してください。

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

<!---HONumber=AcomDC_0921_2016-->