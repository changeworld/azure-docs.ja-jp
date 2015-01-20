<properties title="Copy data with Azure Data Factory" pageTitle="Azure Data Factory を使用してデータをコピーする" description="Azure データ ファクトリでコピー操作を使用して、データ ソース間でデータをコピーする方法について説明します。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Azure Data Factory を使用してデータをコピーする (コピー アクティビティ)
パイプラインで**コピー アクティビティ**を使用して、ソースからシンク (ターゲット) にデータを一括でコピーできます。コピー アクティビティは、次のシナリオで使用できます。

- **Azure に入力する**。このシナリオでは、次のサブシナリオのために内部設置型のデータ ソース (例:SQL Server) から Azure データ ストア (例:Azure BLOB、Azure テーブル、または Azure SQL データベース) にデータがコピーされます。
	- さらなる処理のために、Azure 上の 1 か所にデータを収集する。
	- 内部設置型または Azure 以外のクラウド プラットフォームから Azure にデータを移行する。
	- コスト効果の高い階層型の保存を実現するために Azure にデータをアーカイブまたはバックアップする。
- **Azure から出力する**。このシナリオでは、次のサブシナリオのために Azure (例:Azure BLOB、Azure テーブル、または Azure SQL データベース) から内部設置型のデータ マートおよびデータ ウェアハウス (例:SQL Server) にデータがコピーされます。
	- クラウドのデータ ウェアハウスをサポートしていないためにデータを内部設置型に転送する。
	- 既存の内部設置型ソリューションやレポート インフラストラクチャを活用するためにデータを内部設置型に転送する。
	- 階層型の保存を実現するために内部設置型にデータをアーカイブまたはバックアップする。
- **Azure 間のコピー**。このシナリオでは、Azure の複数のデータ ソースに分散したデータが一元的な Azure データ ストアに集約されます。次に例を示します。Azure テーブルから Azure BLOB へ、Azure BLOB から Azure テーブルへ、Azure テーブルから Azure SQL へ、Azure BLOB から Azure SQL へ。

コピー アクティビティを使用して Azure BLOB ストレージから Azure SQL データベースにデータをコピーする方法のチュートリアルは、「[Azure Data Factory を使ってみる][adfgetstarted]」を参照してください。コピー アクティビティを使用して内部設置型の Azure SQL データベースから Azure BLOB ストレージにデータをコピーする方法のチュートリアルは、「[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources]」を参照してください。


## コピー アクティビティ - コンポーネント
コピー アクティビティは、次のコンポーネントで構成されます。 

- **入力テーブル**。テーブルは、スキーマを持つ四角形のデータ セットです。入力テーブル コンポーネントは、 
- テーブルの名前、テーブルの種類、入力データが含まれたデータ ソースを参照するリンクされたサービスなどのアクティビティの入力データについて説明します。
- **出力テーブル**。出力テーブルは、テーブルの名前、テーブルの種類、出力データが含まれたデータ ソースを参照するリンクされたサービスなどのアクティビティの出力データについて説明します。
- **変換ルール**。変換ルールは、ソースから入力データを抽出する方法や、出力データをシンクに読み込む方法などを指定します。
 
コピー アクティビティには、1 つの**入力テーブル**と 1 つの**出力テーブル**があります。

## コピー アクティビティの JSON
パイプラインは、1 つ以上のアクティビティで構成されます。パイプライン内のアクティビティは、**activities[]** セクションで定義されます。パイプラインの JSON は次のとおりです。
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

**activities** セクション内のアクティビティごとに、次の最上位構造があります。**type** プロパティは、**CopyActivity** に設定されます。コピー アクティビティは、1 つの入力テーブルと 1 つの出力テーブルを使用できます。
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

次の表では、アクティビティのセクションで使用されるタグについて説明します。 

<table border="1">	
	<tr>
		<th align="left">タグ</th>
		<th align="left">説明</th>
		<th align="left">必須</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>アクティビティの名前です。</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>アクティビティの用途を説明するテキストです。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>type</td>
		<td>アクティビティの種類を指定します。 <br/><br/>ここで <b>type</b> は、 <b>CopyActivity</b>に設定されます。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>アクティビティで使用される入力テーブルです。コピー アクティビティに 1 つの入力テーブルのみを指定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>アクティビティで使用される出力テーブルです。コピー アクティビティに 1 つの出力テーブルのみを指定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>transformation のプロパティは type に依存します。コピー  <b>アクティビティ</b> は、 <b>source セクション</b> と <b>sink </b> セクションを <b>transformation</b> セクション内に指定する必要があります。詳細については、この記事の後半で説明します。 </td>
		<td>Y</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>アクティビティの実行時の動作に影響するポリシーです。指定されていない場合は既定値が使用されます。</td>
		<td>N</td>
	</tr>


</table>

JSON プロパティとスクリプトの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)][json-script-reference]」を参照してください。

## コピー アクティビティ - 例
この例では、入力テーブルと出力テーブルが定義されます。また、これらのテーブルが、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーするパイプライン内のコピー アクティビティで使用されます。

**前提条件**
サンプル JSON スクリプトでは、次の Azure Data Factory アーティファクトが参照されます。

* **ADF** という名前のリソース グループ
* **CopyFactory** という名前の Azure Data Factory
* 内部設置型の SQL Server データベースをポイントする **MyOnPremisesSQLDB** という名前のリンクされたサービス
* Azure BLOB ストレージをポイントする **MyAzureStorage** という名前のリンクされたサービス

### 入力テーブルの JSON
次の JSON スクリプトは、SQL テーブル **MyTable** を参照する入力テーブルを定義します。これは、**MyOnPremisesSQLDB** というリンクされたサービスが定義する内部設置型の SQL Server データベース内にあります。**name** は Azure Data Factory テーブルの名前で、**tableName** は SQL Server データベース内の SQL テーブルの名前です。

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
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

次の Azure PowerShell コマンド サンプルでは、**New-AzureDataFactoryTable** を使用します。このコマンドは、上記のスクリプトが含まれた JSON ファイルを使用して、**CopyFactory** という Azure Data Factory 内にテーブル (**MyOnPremTable**) を作成します。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

Data Factory コマンドレットの詳細については、[コマンドレット リファレンス][cmdlet-reference]を参照してください。 

### 出力テーブルの JSON
次の JSON スクリプトは、出力テーブル **MyDemoBlob** を定義します。これは、**MyContainer** という BLOB コンテナー内の **MySubFolder** という BLOB フォルダー内にある **MyBlob** という Azure BLOB を参照します。
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
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

次の Azure PowerShell コマンド サンプルでは、**New-AzureDataFactoryTable** を使用します。このコマンドは、上記のスクリプトが含まれた JSON ファイルを使用して、**CopyFactory** という Azure Data Factory 内にテーブル (**MyDemoBlob**) を作成します。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### パイプライン (コピー アクティビティ) の JSON
この例では、パイプライン **CopyActivityPipeline** が次のプロパティで定義されます。 

- **type** プロパティは、**CopyActivity** に設定されます。
- **MyOnPremTable** が入力として指定されます (**inputs** タグ)。
- **MyAzureBlob** が出力として指定されます (**outputs** タグ)。
- **変換**セクションには、**ソース**と**シンク**の 2 つのサブ セクションがあります。ソースの種類は **SqlSource** に設定され、シンクの種類は **BlobSink** に設定されます。**sqlReaderQuery** は、変換 (射影) がソース上で実行されるように定義します。すべてのプロパティの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)][json-script-reference]」を参照してください。

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}


 次の Azure PowerShell コマンド サンプルでは、**New-AzureDataFactoryPipeline** を使用します。このコマンドは、上記のスクリプトが含まれた JSON ファイルを使用して、**CopyFactory** という Azure Data Factory 内にパイプライン (**CopyActivityPipeline**) を作成します。
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## サポートされている入力と出力
上記の例では、transformation セクションで、ソースとして SqlSource を使用し、シンクとして BlobSink を使用しました。次の表は、コピー アクティビティでサポートされているソースとシンクの一覧です。 

<table border="1">	
	<tr>
		<th><i>シンク/ソース<i></th>
		<th>Azure BLOB</th>
		<th>Azure テーブル</th>
		<th>Azure SQL Database</th>
		<th>内部設置型 SQL Server</th>
		<th>IaaS 上の SQL Server</th>
	</tr>	

	<tr>
		<td><b>Azure BLOB</b></td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
	</tr>

	<tr>
		<td><b>Azure テーブル</b></td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>Azure SQL Database</b></td>
		<td>○</td>
		<td>○</td>
		<td>○</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>内部設置型 SQL Server</b></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>IaaS 上の SQL Server</b></td>
		<td>○</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


次の表は、コピー アクティビティが含まれたパイプラインの JSON ファイルで使用できるソースの種類とシンクの種類の一覧です。


<table border="1">	
	<tr>
		<th></th>
		<th align="left">ソースの種類</th>
		<th align="left">シンクの種類</th>
	</tr>	

	<tr>
		<td><b>Azure BLOB</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Azure テーブル</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>Azure SQL Database</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>内部設置型 SQL Server</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>IaaS 上の SQL</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

次の表は、これらのソースとシンクでサポートされているプロパティの一覧です。

<table border="1">

	<tr>
	<th align="left">ソース/シンク</th>
	<th align="left">サポートされているプロパティ</th>
	<th align="left">説明</th>
	<th align="left">使用できる値</th>
	<th align="left">必須</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>null と空の文字列を NULL 値として処理するかどうかを指定します。</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>スキップする必要のある行数を指定します。</td>
		<td>0 から最大値までの整数。</td>
		<td>N</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>カスタム クエリを使用してデータを読み取ります。</td>
		<td>Azure テーブルのクエリ文字列。<br/>サンプル:"ColumnA eq ValueA"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>「テーブルが存在しない」という例外を無視するかどうかを指定します。</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>カスタム クエリを使用してデータを読み取ります。</td>
		<td>SQL クエリ文字列。<br/><br/> 次に例を示します。"Select * from MyTable"<br/><br/> 指定しない場合、"select <構造で定義された列> from MyTable" クエリ。</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>シンクで使用できる既定のパーティション キー値です。</td>
		<td>文字列の値。</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>ユーザーが指定した列名です。列の値がパーティション キーとして使用されます。<br/><br/> 指定しない場合、AzureTableDefaultPartitionKeyValue がパーティション キーとして使用されます。</td>
		<td>列の名前。</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>行キーとして使用するように指定された列名の列の値です。<br/><br/>指定しない場合、GUID が行ごとに使用されます。</td>
		<td>列の名前。</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>Azure テーブルにデータを挿入する方法です。</td>
		<td>"merge"<br/><br/>"replace"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。</td>
		<td>1 ～ 100 の整数 (単位 = 行数)</td>
		<td>N<br/><br/>(既定 = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。</td>
		<td>(単位 = timespan)<br/><br/>サンプル:"00:20:00" (20 分)</td>
		<td>N<br/><br/>(既定値は、ストレージ クライアントの既定のタイムアウト値の 90 秒)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>ターゲット テーブルにデータを UPSERT (更新/挿入) するストアド プロシージャの名前を指定します。</td>
		<td>ストアド プロシージャの名前。</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>上記のストアド プロシージャで使用されるテーブルの種類を指定します。</td>
		<td>テーブルの種類の名前。</td>
		<td>N</td>
	</tr>
</table>

### サービスとしてのインフラストラクチャ (IaaS) 上の SQL
IaaS 上の SQL では、IaaS プロバイダーとしての Azure がサポートされます。次のネットワークおよび VPN のトポロジがサポートされます。Data Management Gateway は、1 の場合に必要がなくても、2 および 3 の場合に必要です。Data Management Gateway の詳細については、「[Enable your pipelines to access on-premises data (パイプラインが内部設置型のデータにアクセスできるようにする)][use-onpremises-datasources]」を参照してください。

1.	パブリック DNS 名と静的パブリック ポートを持つ VM: プライベート ポート マッピング
2.	SQL エンドポイントが公開されていないパブリック DNS 名を持つ VM
3.	仮想ネットワーク
	<ol type='a'>
	<li>Azure Cloud VPN with following topology at the end of the list. </li>	
	<li>VM with onpremises-to-cloud site-to-site VPN using Azure Virtual Network.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Column filtering using structure definition
Depending on the type of Table, it is possible to specify a subset of the columns from the source by specifying fewer columns in the **Structure** definition of the table definition than the ones that exist in the underlying data source. The following table provides information about column filtering logic for different types of table. 

<table>

	<tr>
		<th align="left">テーブルの種類</th>
		<th align="left">列フィルタリング ロジック</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>テーブル JSON の <b>Structure </b> 定義は、BLOB の構造と一致する必要があります。列のサブセットを選択するには、次のセクション「変換ルール - 列マッピング」で説明する列マッピング機能を使用します。 </td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation と OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>プロパティ  <b>SqlReaderQuery</b> がコピー アクティビティ定義の一部として指定されている場合、 <b>テーブルの Structure</b> 定義は、クエリで選択された列に合わせる必要があります。</p>
			<p>プロパティ <b>SqlReaderQuery</b> が指定されていない場合は、コピー アクティビティが テーブル定義の <b>Structure</b> 定義で指定された列に基づく SELECT クエリを自動的に構築します。</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			テーブル定義の <b>Structure</b> セクションには、基になる Azure テーブルの列のサブセットをすべて含めることができます。
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

#### 例 1 - SQL Server から Azure BLOB への列マッピング
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

**入力テーブル**に **fileName** (ファイル名) を指定しない場合、input フォルダー (**folderPath**) 内のすべてのファイルまたは BLOB が入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。例については、[チュートリアル][adf-tutorial]のサンプル ファイルを参照してください。

**出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されたファイルに次の形式で名前が付けられます。Data.<Guid>.txt (例::Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)

**folderPath** と **fileName** を **SliceStart** 時刻に基づいて動的に設定するには、**partitionedBy** プロパティを使用します。次の例では、**folderPath** に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。たとえば、スライスが 2014-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2014/10/20 に設定され、fileName は 08.csv に設定されます。 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

この例では、パイプライン内のアクティビティが次のように定義されます。ソースの列は、**Translator** プロパティを使用して、シンク (**columnMappings**) の列にマップされます。

##### 例 - 列マッピング

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

![Column Mapping][image-data-factory-column-mapping-1]

##### 例 2 - SQL クエリを使用した SQL Server から Azure BLOB への列マッピング
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
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
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

![Column Mapping 2][image-data-factory-column-mapping-2]

#### コピー アクティビティによるデータ型の処理

テーブル定義の Structure セクションで指定するデータ型は、**BlobSource** でのみ受け入れられます。次の表は、その他の種類のソースおよびシンクでのデータ型の処理方法を示しています。

<table>	
	<tr>
		<th align="left">ソース/シンク</th>
		<th align="left">データ型の処理ロジック</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>テーブル定義の <b>Structure</b> セクションで指定されているデータ型は無視されます。基になる SQL データベースで定義されたデータ型は、コピー アクティビティのデータ抽出に使用されます。</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>テーブル定義の <b>Structure</b> セクションで指定されているデータ型は無視されます。基になるソースとターゲットのデータ型を比較し、型の不一致がある場合は、暗黙的な型の変換が行われます。</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>BlobSource <b>から</b> BlobSink <b>に変換する場合は</b>、型の変換はありません。テーブル定義の <b>Structure</b> セクションで指定されているデータ型は無視されます。ここで、 <b>BlobSink</b>以外のターゲットの場合、テーブル定義の <b>Structure</b> セクションで指定されているデータ型は受け入れられます。</p>
		<p>
		また、 <b>Structure</b> がテーブル定義で指定されていない場合、型の処理は、 <b>BlobSink の format</b> プロパティに <b>依存します</b>:
		</p>
		<ul>
			<li> <b>TextFormat:</b> すべての列の種類は文字列として扱われ、すべての列名は "Prop_<0 ～ n>"で設定されます。</li> 
			<li><b>AvroFormat:</b> Avro ファイルに用意された列の種類と名前を使用します。</li> 
			<li><b>JsonFormat:</b> すべての列の種類は文字列として扱われ、Json ファイルに用意された列名を使用します。</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>入力テーブル定義の <b>Structure</b> セクションで指定されているデータ型は無視されます。基になる入力データ ストアで定義されたデータ型が使用されます。列は、Avro シリアル化の null 許容型として指定されます。</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>テーブル定義の <b>Structure</b> セクションで指定されているデータ型は無視されます。基になる Azure テーブルで定義されたデータ型が使用されます。</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>テーブル定義の <b>Structure</b> セクションで指定されているデータ型は無視されます。基になる入力データ ストアで定義されたデータ型が使用されます。</td>
	</tr>

</table>


## チュートリアル
コピー アクティビティを使用して Azure BLOB ストレージから Azure SQL データベースにデータをコピーする方法のチュートリアルは、「[Azure Data Factory を使ってみる][adfgetstarted]」を参照してください。
 
コピー アクティビティを使用して内部設置型の Azure SQL データベースから Azure BLOB ストレージにデータをコピーする方法のチュートリアルは、「[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources]」を参照してください。



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=35.2-->
