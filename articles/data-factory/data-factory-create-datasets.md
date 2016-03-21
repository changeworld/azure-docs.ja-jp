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
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Azure Data Factory のデータセット

## 説明
データセットはデータを論理的に説明するものです。記述されるデータは、単純なバイト、CSV ファイルのような半構造化データから、リレーショナル テーブルやモデルまでさまざまです。データにアクセスするためのメカニズム (アドレス、プロトコル、認証方式) はリンクされたサービスで定義され、データセット定義で参照されます。

## 構文

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

**構文の詳細**

| プロパティ | 説明 | 必須 | 既定値 |
| -------- | ----------- | -------- | ------- |
| name | データセットの名前 | あり | 該当なし |
| structure | <br/>データセットのスキーマ<br/>詳細については、「[データセット構造](#Structure)」セクションを参照してください。 | いいえ、できません。 | 該当なし |
| type | データセットの型 | あり | 該当なし |
| typeProperties | 選択した種類に対応するプロパティ サポートされる型とそのプロパティについては、「[データセットの型](#Type)」セクションを参照してください。 | あり | 該当なし |
| 外部 | データセットを Data Factory パイプラインにより明示的に生成するかどうかを指定するブール型のフラグ | いいえ | false | 
| availability | データセット生成の処理時間枠またはスライシング モデルを定義します。<br/><br/>詳細については、「[データセットの可用性](#Availability)」トピックを参照してください。<br/><br/>データセット スライシング モデルの詳細については、[スケジュールと実行](data-factory-scheduling-and-execution.md)に関する記事を参照してください。 | あり | 該当なし
| policy | データセット スライスで満たさなければならない基準または条件を定義します。<br/><br/>詳細については、「[データセット ポリシー](#Policy)」トピックを参照してください。 | いいえ | 該当なし |

### 例

以下は **Azure SQL データベース**に「**MyTable**」という名前のテーブルを表示するデータセットの例です。Azure SQL データベースの接続文字列はこのデータセットで参照される **AzureSqlLinkedService** で定義されます。このデータセットは毎日スライスされます。

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

## <a name="Structure"></a>データセット構造

**構造**セクションでは、データセットのスキーマをアサートします。列の集合と列の種類が含まれます。各列には次のプロパティが含まれます。

プロパティ | 説明 | 必須 | 既定値  
-------- | ----------- | -------- | -------
name | 列の名前です。 | いいえ | 該当なし 
type | 列のデータ型です。 | いいえ | 該当なし 

### 例

次の例では、データセットに slicetimestamp、projectname、pageviews の 3 つの列があります。

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a> データセットの型

サポートされるデータ ソースとデータセットの型が並んでいます。データセットの型と構成の詳細については、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事の中のコネクタに関するトピックを参照してください。

## <a name="Availability"></a> データセットの可用性

データセットの「可用性」セクションにより、データセット生成の処理時間枠またはスライシング モデルが定義されます。データセットのスライシングおよび依存性モデルの詳細については、「[Data Factory を使用したスケジュール設定と実行](data-factory-scheduling-and-execution.md)」をご覧ください。

| プロパティ | 説明 | 必須 | 既定値 |
| -------- | ----------- | -------- | ------- |
| frequency | データセット スライス生成の時間単位を指定します。<br/><br/>**サポートされている頻度**: 分、時間、日、週、月 | あり | 該当なし |
| interval | 頻度の乗数を指定します。<br/><br/>"頻度 x 間隔" により、スライスが生成される頻度が決定されます。<br/><br/>データセットを時間単位でスライスする必要がある場合は、**[頻度]** を **[時間]** に、**[間隔]** を **[1]** に設定します。<br/><br/>**注:** 頻度に分を指定する場合、間隔を 15 以上に設定することをお勧めします。 | あり | 該当なし |
| style | スライスを間隔の始め/終わりに生成するかどうかを指定します。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>頻度を月に設定し、スタイルを EndOfInterval に設定すると、スライスは月の最終日に生成されます。スタイルを StartOfInterval に設定すると、スライスは月の最初の日に生成されます。<br/><br/>頻度を日に設定し、スタイルを EndOfInterval に設定すると、スライスは 1 日の最後の 1 時間に生成されます。<br/><br/>頻度を時間に設定し、スタイルを EndOfInterval に設定すると、スライスは時間の終わりに生成されます。たとえば、午後 1 時 ～ 午後 2 時のスライスの場合、午後 2 時直前にスライスが生成されます。 | いいえ | EndOfInterval |
| anchorDateTime | データセット スライスの境界を計算するためにスケジューラによって使用される時間の絶対位置を定義します。<br/><br/>**注:** AnchorDateTime に頻度より細かい日付部分が含まれている場合、その部分は無視されます。たとえば、**間隔**が**時間単位** (頻度: 時間、間隔: 1) で、**AnchorDateTime** に**分と秒**が含まれる場合、AnchorDateTime の**分と秒**部分は無視されます。 | いいえ | 01/01/0001 |
| offset | すべてのデータセット スライスの開始と終了がシフトされる時間帯です。<br/><br/>**注:** anchorDateTime とoffset の両方が指定されている場合、結果的にシフトが結合されます。 | いいえ | 該当なし |

### anchorDateTime 例

**例:** 2007-04-19T08:00:00 に開始する 23 時間のデータセット スライス

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}


### offset 例

既定の真夜中の代わりに朝の 6 時に開始する毎日のスライス。

	"availability":
	{
		"frequency": "Day",
		"interval": "1",
		"offset": "06:00:00"
	}

**頻度**が**月**に設定され、**間隔**が **1** (月 1 回) に設定されている場合、各月 9 日午前 6 時にスライスを生成するには、オフセットを "09.06:00:00" に設定します。これは UTC 時間であることに注意してください。

12 か月スケジュールの場合 (頻度 = 月、間隔 = 12)、「オフセット: 60.00:00:00」は毎年の 3 月 1 日または 2 日 (スタイルが StartOfInterval の場合、年の初めから 60 日後であり、うるう年かどうかにより 1 日または 2 日になる) を意味します。


## <a name="Policy"></a>データセット ポリシー

データセットのポリシー セクションでは、データセット スライスで満たさなければならない基準または条件を定義します。

### 検証ポリシー

| ポリシー名 | 説明 | 適用先 | 必須 | 既定値 |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Azure BLOB のデータが最小サイズ要件 (メガバイト単位) を満たすことを検証します。 | Azure BLOB | いいえ | 該当なし |
|minimumRows | Azure SQL データベースまたは Azure テーブルのデータが最小行数が含まれていることを検証します。 | <ul><li>Azure SQL Database</li><li>Azure テーブル</li></ul> | いいえ | 該当なし

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

### ExternalData ポリシー

外部データセットは Data Factory のパイプライン実行で生成されないデータセットです。データセットが「外部」としてマークされている場合、データセット スライス可用性の動作を変更するように ExternalData ポリシーを定義できます。

| 名前 | 説明 | 必須 | 既定値 |
| ---- | ----------- | -------- | -------------- |
| dataDelay | 特定のスライスの外部データの可用性チェックを遅らせる時間。たとえば、データを時間単位で利用する場合、外部データが実際に利用できることと、該当するスライスが "準備完了" であることの確認を dataDelay で遅らせることができます。<br/><br/>現在時刻にのみ適用されます。たとえば、現在午後 1 時で、この値が 10 分の場合、検証は午後 1 時 10 分に開始されます。<br/><br/>この設定は過去のスライスには影響しません。スライスの終了時刻と dataDelay を足したものが "現在" より前になるスライスは遅延なく処理されます。<br/><br/>23 時間 59 分を超える時間は、day.hours:minutes:seconds 形式で指定する必要があります。たとえば、24 時間を指定するには、24:00:00 ではなく、1.00:00:00 を使用します。24:00:00 を使用した場合は、24 日間 (24.00:00:00) として処理されます。1 日と 4 時間の場合は 1:04:00:00 と指定します。 | いいえ | 0 |
| retryInterval | エラーと次の再試行の間の待機時間です。現在の時間に適用されます。前の試行に失敗した場合、最後の試行からこの時間が経過するまで待機します。<br/><br/>現在時刻が午後 1 時の場合に最初の試行を開始したとします。最初の検証チェックを完了するための時間が 1 分のとき、操作に失敗した場合、次の再試行は「1:00pm + 1 分 (チェック時間) + 1 分 (再試行間隔) = 1:02pm」になります。<br/><br/>過去のスライスの場合、遅延はありません。再試行はすぐに実行されます。 | いいえ | 00:01:00 (1 分) | 
| retryTimeout | 各再試行のタイムアウト。<br/><br/>これが 10 分に設定されている場合、検証を 10 分以内に完了する必要があります。検証に 10 分以上の時間がかかった場合、再試行はタイムアウトします。<br/><br/>検証のすべての試行がタイムアウトした場合、スライスに TimedOut のマークが付きます。 | いいえ | 00:10:00 (10 分) |
| maximumRetry | 外部データの可用性の確認回数です。許容される最大値は 10 です。 | いいえ | 3 | 

#### その他の例

毎月の特定の日時 (たとえば、毎月 3 日の午前 8 時) にパイプラインを実行する必要がある場合は、**offset** タグを使用して、実行する日時を設定できます。

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

<!---HONumber=AcomDC_0309_2016-->