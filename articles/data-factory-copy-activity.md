<properties title="Copy data with Azure Data Factory" pageTitle="Azure Data Factory を使用してデータをコピーする" description="Azure データ ファクトリでコピー操作を使用して、データ ソース間でデータをコピーする方法について説明します。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure Data Factory を使用してデータをコピーする (コピー アクティビティ)
パイプラインで**コピー アクティビティ**を使用して、ソースからシンク (ターゲット) にデータを一括でコピーできます。コピー アクティビティは、次のシナリオで使用できます。

- **Azure に入力する**。このシナリオでは、次のサブシナリオのために、内部設置型のデータ ソース (例: SQL Server) から Azure データ ストア (例: Azure BLOB、Azure テーブル、Azure SQL データベース) にデータがコピーされます。
	- さらなる処理のために、Azure 上の 1 か所にデータを収集する。
	- 内部設置型または Azure 以外のクラウド プラットフォームから Azure にデータを移行する。
	- コスト効果の高い階層型の保存を実現するために Azure にデータをアーカイブまたはバックアップする。
- **Azure から出力する**。このシナリオでは、次のサブシナリオのために、Azure (例: Azure BLOB、Azure テーブル、Azure SQL データベース) から内部設置型のデータ マートやデータ ウェアハウス (例: SQL Server) にデータがコピーされます。
	- クラウドのデータ ウェアハウスをサポートしていないためにデータを内部設置型に転送する。
	- 既存の内部設置型ソリューションやレポート インフラストラクチャを活用するためにデータを内部設置型に転送する。
	- 階層型の保存を実現するために内部設置型にデータをアーカイブまたはバックアップする。
- **Azure 間でコピーする**このシナリオでは、Azure の複数のデータ ソースに分散したデータが一元的な Azure データ ストアに集約されます。次に例を示します。Azure テーブルから Azure BLOB へ、Azure BLOB から Azure テーブルへ、Azure テーブルから Azure SQL へ、Azure BLOB から Azure SQL へ。

コピー アクティビティを使用して Azure BLOB ストレージから Azure SQL データベースにデータをコピーする方法のチュートリアルは、「[Azure Data Factory を使ってみる][adfgetstarted]」を参照してください。コピー アクティビティを使用して内部設置型の Azure SQL データベースから Azure BLOB ストレージにデータをコピーする方法のチュートリアルは、「[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources]」を参照してください。


## コピー アクティビティ - コンポーネント
コピー アクティビティは、次のコンポーネントで構成されます。 

- **入力テーブル**。テーブルは、スキーマを持つ四角形のデータ セットです。入力テーブル コンポーネントには、 
- 次のような、アクティビティの入力データが記述されます。テーブルの名前、テーブルの種類、入力データが含まれたデータ ソースを参照するリンクされたサービス。
- **出力テーブル**。出力テーブルには、次のような、アクティビティの出力データが記述されます。テーブルの名前、テーブルの種類、出力データが含まれたデータ ソースを参照するリンクされたサービス。
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
		<td>アクティビティの種類を指定します。<br/><br/><b>type</b> は、<b>CopyActivity</b> に設定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>アクティビティで使用される入力テーブルです。  コピー アクティビティ用の入力テーブルを 1 つだけ指定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>アクティビティで使用される出力テーブルです。  コピー アクティビティ用の出力テーブルを 1 つだけ指定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>transformation のプロパティは type によって異なります。  <b>コピー アクティビティ</b>では、<b>transformation</b> セクション内の <b>source</b> セクションと <b>sink</b> セクションを指定する必要があります。詳細については、この記事の後半で説明します。 </td>
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
次の JSON スクリプトは、入力テーブルを定義します。この入力テーブルは、リンクされたサービス **MyOnPremisesSQLDB** で定義された内部設置型の SQL Server データベースにある SQL テーブル **MyTable** を参照します。**name** は Azure Data Factory テーブルの名前で、**tableName** は SQL Server データベース内の SQL テーブルの名前です。

         
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
次の JSON スクリプトは、**MyDemoBlob** という出力テーブルを定義します。この出力テーブルは、**MyContainer** という BLOB コンテナー内の **MySubFolder** という BLOB フォルダーに含まれている **MyBlob** という Azure BLOB を参照します。
         
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
この例では、次のプロパティを使用して **CopyActivityPipeline** というパイプラインが定義されます。 

- **type** プロパティは、**CopyActivity** に設定されます。
- **MyOnPremTable** が入力として指定されます (**inputs** タグ)。
- **MyAzureBlob** が出力として指定されます (**outputs** タグ)。
- **Transformation** セクションには、**source** と **sink** という 2 つのサブ セクションがあります。ソースの種類は **SqlSource** に設定され、シンクの種類は **BlobSink** に設定されます。**sqlReaderQuery** は、変換 (射影) がソース上で実行されるように定義します。すべてのプロパティの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)][json-script-reference]」を参照してください。

         
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
		<td>Azure テーブル クエリ文字列。<br/>例: "ColumnA eq ValueA"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>例外 "テーブルが存在しません" を無視するかどうかを指定します。</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>カスタム クエリを使用してデータを読み取ります。</td>
		<td>SQL クエリ文字列。<br/><br/>例: "Select * from MyTable"。<br/><br/>指定しない場合、"select <構造で定義された列> from MyTable" クエリ。</td>
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
		<td>行キーとして使用される、指定された列名の列の値です。<br/><br/>指定しない場合、各行の GUID が使用されます。</td>
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
		<td>N<br/><br/>(既定値 = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。</td>
		<td>(単位 = 時間)<br/><br/>例: "00:20:00" (20 分)</td>
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
IaaS 上の SQL では、IaaS プロバイダーとしての Azure がサポートされます。次のネットワークおよび VPN のトポロジがサポートされます。ケース 2 とケース 3 では Data Management Gateway が必要ですが、ケース 1 では必要ない点に注意してください。Data Management Gateway の詳細については、「[Enable your pipelines to work with on-premises data (パイプラインによる内部設置型データの操作の有効化)][use-onpremises-datasources]」を参照してください。

1.	パブリック DNS 名と静的パブリック ポートを持つ VM: プライベート ポート マッピング
2.	SQL エンドポイントが公開されていないパブリック DNS 名を持つ VM
3.	仮想ネットワーク
	<ol type='a'>
	<li>一覧の最後に次のトポロジが含まれている Azure クラウド VPN </li>	
	<li>Azure Virtual Network を使用した内部設置型からクラウドへのサイト間 VPN を持つ VM</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## 構造体定義を使用した列のフィルター処理
テーブルの種類によっては、ソースの列のサブセットを指定できます。指定するには、テーブル定義の **Structure** 定義の列を、基になるデータ ソースの列よりも少なく設定します。次の表は、さまざまな種類のテーブルの列フィルタリング ロジックをまとめたものです。 

<table>

	<tr>
		<th align="left">テーブルの種類</th>
		<th align="left">列フィルタリング ロジック</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>テーブル JSON 内の <b>Structure</b> 定義は、BLOB の構造と一致する必要があります。  列のサブセットを選択するには、次のセクション「変換ルール - 列マッピング」で説明する列マッピング機能を使用します。</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation と OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>コピー アクティビティ定義の中でプロパティ <b>SqlReaderQuery</b> が指定されている場合、テーブルの <b>Structure</b> 定義は、クエリで選択された列と一致している必要があります。</p>
			<p>プロパティ <b>SqlReaderQuery</b> が指定されていない場合は、コピー アクティビティによって、テーブル定義の <b>Structure</b> 定義で指定された列に基づいて自動的に SELECT クエリが作成されます。</p>
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

**出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されたファイルに次の形式で名前が付けられます。Data.<Guid>.txt (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)

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
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型が無視されます。  コピー アクティビティ時のデータ抽出には、基になる SQL データベースで定義されたデータ型が使用されます。</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型が無視されます。  基になるソースとターゲットのデータ型が比較され、型の不一致がある場合は暗黙的な型変換が実行されます。</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p><b>BlobSource</b> から <b>BlobSink</b> への転送時に、型変換は実行されません。テーブル定義の <b>Structure</b> セクションで定義された型は無視されます。  <b>BlobSink</b> 以外のターゲットの場合は、テーブル定義の <b>Structure</b> セクションで定義されたデータ型が受け入れられます。</p>
		<p>
		テーブル定義で <b>Structure</b> が指定されていない場合、型の処理は <b>BlobSink</b> の <b>format</b> プロパティに基づいて実行されます。
		</p>
		<ul>
			<li> <b>TextFormat: </b> すべての列の種類は文字列として扱われ、すべての列名は "Prop_<0 ～ n>"で設定されます。</li> 
			<li><b>AvroFormat: </b> Avro ファイルに用意された列の種類と名前を使用します。</li> 
			<li><b>JsonFormat: </b> すべての列の種類は文字列として扱われ、Json ファイルに用意された列名を使用します。</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型が無視されます。  基になる入力データ ストアで定義されたデータ型が使用されます。  列は Avro シリアル化の null 許容型として指定されます。</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型が無視されます。  基になる Azure テーブルで定義されたデータ型が使用されます。</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>テーブル定義の <b>Structure</b> セクションで定義されたデータ型が無視されます。  基になる入力データ ストアで定義されたデータ型が使用されます。</td>
	</tr>

</table>

## 制限事項

> [WACOM.NOTE] パイプラインが中断または削除された場合や、Data Factory が削除された場合でも、実行中のコピー操作は中断されません。完了するまで引き続き実行されます。ただし、Data Management Gateway 構成マネージャーまたはサービス アプレットを使用して、Data Management Gateway サービスを再起動することによって、内部設置型のデータ ソースに関連するコピー操作を停止できます。 



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
