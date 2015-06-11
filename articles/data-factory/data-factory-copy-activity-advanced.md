<properties 
	pageTitle="Azure Data Factory でコピー アクティビティを使用する高度なシナリオ" 
	description="Azure Data Factory でコピー アクティビティを使用する高度なシナリオについて説明します。" 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Azure Data Factory でコピー アクティビティを使用する高度なシナリオ 
## 概要
パイプラインで**コピー アクティビティ**を使用して、ソースからシンク (ターゲット) にデータを一括でコピーできます。このトピックでは、コピー アクティビティがサポートする高度なシナリオについて説明します。コピー アクティビティと、コピー アクティビティがサポートする主要なシナリオの詳細については、「[Azure Data Factory を使用してデータをコピーする][adf-copyactivity]」を参照してください。


## 構造体定義を使用した列のフィルター処理
テーブルの種類によっては、テーブル定義の **Structure** 定義の列を、基になるデータ ソースの列よりも少なく指定することで、ソースの列のサブセットを指定できます。次の表は、さまざまな種類のテーブルの列フィルタリング ロジックをまとめたものです。

<table>

	<tr>
		<th align="left">テーブルの種類</th>
		<th align="left">列フィルタリング ロジック</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>テーブル JSON 内の <b>Structure</b> 定義は、BLOB の構造と一致する必要があります。列のサブセットを選択するには、次のセクション「変換ルール - 列マッピング」で説明する列マッピング機能を使用します。</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation と OnPremisesSqlServerTableLocation</td>
		<td align="left">
			コピー アクティビティ定義の中でプロパティ <b>SqlReaderQuery</b> が指定されている場合、テーブルの <b>Structure</b> 定義は、クエリで選択された列と一致している必要があります。<br/><br/>
			プロパティ <b>SqlReaderQuery</b> が指定されていない場合は、コピー アクティビティによって、テーブル定義の <b>Structure</b> 定義で指定された列に基づいて自動的に SELECT クエリが作成されます。
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			テーブル定義の <b>Structure</b> セクションには、基になる Azure テーブルの列の完全なセットまたはサブセットを含めることができます。
		</td>
	<tr>

</table>

## 変換ルール - 列マッピング
列マッピングを使用して、ソース テーブルの列をシンク テーブルの列にマップする方法を指定できます。列マッピングは、次のシナリオをサポートしています。

- ソース テーブルの "structure" に指定されたすべての列をターゲット テーブルの "structure" にマップする。
- ソース テーブルの "structure" に指定された列のサブセットがのターゲット テーブルの "structure" にマップされる。

次のシナリオはサポートされておらず、例外がスローされます。

- ターゲットの列数を減らす、または増やす。
- 重複したマッピング。
- SQL クエリの結果に列名が含まれない。

特に、2 つの Azure BLOB の間でデータをコピーするとき、次の 3 つのシナリオに合致する場合を除くほとんどの場合、コピー アクティビティはこれをバイナリ データの直接コピーとして扱います。


1. 入力テーブルと出力テーブルの形式が異なる場合、コピー アクティビティによって形式変換が行われます。
2. 入力テーブルが複数のファイルを格納できるフォルダーとして指定され、出力テーブルがファイルとして指定された場合、コピー アクティビティによってソース フォルダー内の各ファイルが 1 つのシンク ファイルにマージされます。
3. "columnMapping" が指定された場合、コピー アクティビティによって対応するデータ変換が行われます。


### 例 1 – SQL Server から Azure BLOB への列マッピング
この例では、**入力テーブル**が次のように定義されます。入力テーブルには構造体が 1 つあり、SQL Server データベース内の SQL テーブルをポイントしています。
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
				"location":
				{
					"type": "OnPremisesSqlServerTableLocation",
					"tableName": "MyTable",
					"linkedServiceName": "MyOnPremisesSQLDB"
				},
				"availability":	
				{
    				"frequency": "Hour",
    				"interval": 1
				}
     		}
		}

この例では、**出力テーブル**が次のように定義されます。出力テーブルには構造体が 1 つあり、Azure BLOB ストレージ内の BLOB をポイントしています。
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
    	    	"format":
    	    	{
    	        	"type": "TextFormat",
		            "columnDelimiter": ",",
    		        "rowDelimiter": ";",
    		        "EscapeChar": "$",
    		        "NullValue": "NaN"
    		    }
			},
			"availability":
			{
    			"frequency": "Hour",
    			"interval": 1
			}
		}
	}	

**出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されるファイルには Data.<Guid>.txt という形式で名前が付けられます (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

**folderPath** と **fileName** を **SliceStart** 時刻に基づいて動的に設定するには、**partitionedBy** プロパティを使用します。次の例では、**folderPath** に SliceStart (処理されるスライスの開始時刻) の Year、Month、Day を使用し、fileName に SliceStart の Hour を使用します。たとえば、スライスが 2014-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2014/10/20 に設定され、fileName は 08.csv に設定されます。

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

#### 例 - 列マッピング
この例では、パイプライン内のアクティビティが次のように定義されます。ソースの列は、**Translator** プロパティを使用して、シンク (**columnMappings**) の列にマップされます。

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![列マッピング][image-data-factory-column-mapping-1]

### 例 2 – SQL クエリを使用した SQL Server から Azure BLOB への列マッピング
この例では、(前の例のテーブルとの比較として) SQL クエリを使用して、内部設置型 SQL Server からデータを抽出し、クエリ結果の列をソース アーティファクトにマップした後、ターゲット アーティファクトにマップします。この例では、クエリは 5 つの列を返します。

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = '{0:yyyyMMdd-HH}'', SliceStart)"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![列マッピング 2][image-data-factory-column-mapping-2]

## コピー アクティビティによるデータ型の処理

テーブル定義の Structure セクションで指定するデータ型は、**BlobSource** でのみ受け入れられます。次の表は、その他の種類のソースおよびシンクでのデータ型の処理方法を示しています。

<table>	
	<tr>
		<th align="left">ソース/シンク</th>
		<th align="left">データ型の処理ロジック</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型は無視されます。基になる SQL データベースで定義されたデータ型は、コピー アクティビティのデータ抽出に使用されます。</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型は無視されます。基になるソースとターゲットのデータ型を比較し、型の不一致がある場合は、暗黙的な型の変換が行われます。</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><b>BlobSource</b> から <b>BlobSink</b> に転送する場合、型の変換は行われません。テーブル定義の <b>Structure</b> セクションで定義されたデータ型は無視されます。<b>BlobSink</b> 以外のターゲットの場合は、テーブル定義の <b>Structure</b> セクションで定義されたデータ型が受け入れられます。<br/><br/>
		テーブル定義で <b>Structure</b> が指定されていない場合、型の処理は、次に示す <b>BlobSource</b> テーブルの <b>format</b> プロパティに基づいて実行されます。
		<ul>
			<li> <b>TextFormat:</b> すべての列の種類は文字列として扱われ、すべての列名は "Prop_&lt;0 ～ n>" として設定されます。</li> 
			<li><b>AvroFormat:</b> Avro ファイルに用意された列の種類と名前を使用します。</li> 
			<li><b>JsonFormat:</b> すべての列の種類は文字列として扱われ、JSON ファイルに用意された列名を使用します。</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型は無視されます。基になる入力データ ストアで定義されたデータ型が使用されます。列は、Avro シリアル化の null 許容型として指定されます。</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型は無視されます。基になる Azure テーブルで定義されたデータ型が使用されます。</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型は無視されます。基になる入力データ ストアで定義されたデータ型が使用されます。</td>
	</tr>

</table>

**注:** Azure テーブルでは、限られたデータ型のセットのみをサポートしています。詳細については、「[テーブル サービス データ モデルについて][azure-table-data-type]」を参照してください。

## SQL シンクのストアド プロシージャの呼び出し
データの SQL Server または Azure SQL Database へのコピー時に、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターと共に呼び出すことができます。
### 例
1. 次のように出力テーブルの JSON を定義します (Azure SQL Database テーブルを例として取り上げます)。

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. 次のように、コピー アクティビティ JSON の **SqlSink** セクションを定義します。データの挿入中にストアド プロシージャを呼び出すには、**SqlWriterStoredProcedureName** と **SqlWriterTableType** の両方のプロパティが必要です。

		"sink":
	    {
			"type": "SqlSink",
	        "SqlWriterTableType": "MarketingType",
		    "SqlWriterStoredProcedureName": "spOverwriteMarketing",	
			"storedProcedureParameters":
					{
                    	"stringData": 
						{
                        	"value": "str1"		
						}
                    }
	    }

3. データベース内で、**SqlWriterStoredProcedureName** と同じ名前のストアド プロシージャを定義します。これによって指定したソースの入力データが処理され、出力テーブルに挿入されます。ストアド プロシージャのパラメーター名は、テーブル JSON ファイルで定義された **tableName** と同じにする必要があります。

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. データベースで、**SqlWriterTableType** と同じ名前のテーブル型を定義します。テーブル型のスキーマは、入力データから返されるスキーマと同じにする必要があります。

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

ストアド プロシージャ機能は[テーブル値パラメーター][table-valued-parameters]を利用しています。

## テキスト ファイルのエンコードを指定する
UTF-8 エンコードが一般的ではあるものの、Azure BLOB のテキスト ファイルは、通常、歴史的経緯から他のエンコードを採用しています。**encodingName** プロパティを使用すると、TextFormat 型のテーブルのコード ページ名でエンコードを指定できます。有効なエンコード名の一覧については、Encoding.EncodingName プロパティを参照してください。例: windows-1250 または shift_jis。既定値は UTF-8 です。有効なエンコード名については、「[Encoding クラス](https://msdn.microsoft.com/library/system.text.encoding(v=vs.110).aspx)」を参照してください。

## 関連項目

- [コピー アクティビティの使用例][copy-activity-examples]
- [Azure Data Factory を使用してデータをコピーする][adf-copyactivity]
- [コピー アクティビティ - JSON スクリプト リファレンス](https://msdn.microsoft.com/library/dn835035.aspx)
- [ビデオ: Azure Data Factory コピーアクティビティの概要][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/en-us/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample2.png

<!---HONumber=GIT-SubDir--> 