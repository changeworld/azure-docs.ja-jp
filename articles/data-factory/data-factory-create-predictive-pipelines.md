<properties 
	pageTitle="Data Factory - Data Factory および Machine Learning を使用して予測パイプラインを作成する | Azure" 
	description="Azure Data Factory と Azure Machine Learning を使用して予測パイプラインを作成する方法について説明します" 
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
	ms.date="06/09/2015" 
	ms.author="spelluru"/>

# Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する 
## 概要
発行された [Azure Machine Learning][azure-machine-learning] モデルを Azure Data Factory パイプライン内で運用できます。これらのパイプラインは、予測パイプラインと呼ばれます。予測パイプラインを作成するには、次が必要です。

-	発行されたワークスペース モデルの API キーとバッチ スコアリング URL (下図を参照)。
-	入力 CSV ファイルを保持する Azure BLOB ストレージ (または) 評価する入力データを含む Azure SQL Database。 
-	スコア結果の CSV ファイルを保持する Azure BLOB ストレージ (または) 出力データを保持する Azure SQL Database。 

	![Machine Learning Dashboard][machine-learning-dashboard]

	AzureMLLinkedService のバッチ スコアリング URL が上図に示すように取得されます (-‘**help**’)。: https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

**予測パイプライン**は、次から構成されます。

-	入力テーブルと出力テーブル
-	Azure Storage/Azure SQL と Azure ML のリンクされたサービス
-	Azure ML バッチ スコアリング アクティビティのパイプライン

> [AZURE.NOTE]発行されている Azure Machine Learning Web サービスによって公開されている Web サービス パラメーターを Azure Data Factory (ADF) パイプラインで使用できます。詳細については、この記事の「Web サービス パラメーター」セクションをご覧ください。

## 例
この例では、Azure Storage を使用して、入力データと出力データの両方を保持します。Azure Storage を使用する代わりに Azure SQL Database を使用することもできます。

この例に進む前に、[Get started with Azure Data Factory の使用][adf-getstarted]に関するチュートリアルを行い、Data Factory エディターを使用してこの例の Data Factory アーティファクト (リンクされたサービス、テーブル、パイプライン) を作成することをお勧めします。
 

1. Azure ストレージ用のリンクされたサービスを作成します。スコアリングの入力ファイルと出力ファイルが異なるストレージ アカウントにある場合は、リンクされたサービスが 2 つ必要です。JSON の例を次に示します。

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. 入力と出力の Azure Data Factory テーブルを作成します。他の Data Factory テーブルとは異なり、この 2 つのテーブルは **folderPath** 値と **fileName** 値を含む必要があります。パーティション分割を使用すると、各バッ チ実行 (各データ スライス) で一意の入力ファイルと出力ファイルを処理または生成できます。入力を CSV ファイル形式に変換し、これを各スライスのストレージ アカウントに配置するには、いくつかのアップストリーム アクティビティを含める必要があります。その場合は、次の例に示す「waitOnExternal」設定を含めないと、ScoringInputBlob は別のアクティビティの出力テーブルになります。

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}
	
	バッチ スコアリング csv ファイルには列ヘッダー行がある必要があります。**コピー アクティビティ**を使用している csv を BLOB ストレージに作成または移動する場合、シンクの **blobWriterAddHeader** プロパティを **true** に設定する必要があります。次に例を示します。
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	csv ファイルにヘッダー行がない場合、次のエラーが表示される場合があります: **アクティビティ エラー: 文字列の読み取りエラー。予期しないトークン: StartObject。パス ''、行 1、位置 1**。
3. この出力例では、パーティション分割を使用して各スライスの実行ごとに一意の出力パスを作成します。これを行わないと、アクティビティはファイルを上書きします。

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. **AzureMLLinkedService** という型を持つリンクされたサービスを作成し、API キーとモデルのバッチ スコアリング URL を入力します。
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. 最後に、**AzureMLBatchScoringActivity** を含むパイプラインを作成します。これは、入力テーブルから入力ファイルの場所を取得し、AzureML バッチ スコアリング API を呼び出し、出力テーブルで指定された BLOB にバッチ スコアリング出力をコピーします。他の Data Factory テーブルとは異なり、AzureMLBatchScoringActivity は 1 つの入力テーブルと 1 つの出力テーブルのみを含むことができます。

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ],

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z"
		    }
		}

	**start** と **end** の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。**end** の時刻は省略可能です。**end** プロパティの値を指定しない場合、"**start + 48 hours**" として計算されます。パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティとして指定します。JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](https://msdn.microsoft.com/library/dn835050.aspx)を参照してください。

## Web サービス パラメーター
発行されている Azure Machine Learning Web サービスによって公開されている Web サービス パラメーターを Azure Data Factory (ADF) パイプラインで使用できます。Azure Machine Learning で実験を作成して Web サービスとして発行した後、この Web サービスを複数の ADF パイプラインまたはアクティビティで使用するときに、Web サービス パラメーターを介して異なる入力を渡すことができます。

### Web サービス パラメーターの値を渡す
パイプライン JSON の **AzureMLBatchScoringActivty** セクションに **transformation** セクションを追加して、そのセクション内で Web サービス パラメーターの値を指定します。次に例を示します。

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


また、次の例に示すように、[Data Factory 関数](https://msdn.microsoft.com/library/dn835056.aspx)を使用して、Web サービス パラメーターの値を渡すこともできます。

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \'{0:yyyy-MM-dd HH:mm:ss}\'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Web サービス パラメーターでは大文字と小文字が区別されるため、アクティビティ JSON に指定した名前が Web サービスによって公開されている名前と一致していることを確認してください。

### Azure SQL のリーダーとライター
Web サービス パラメーターの使用を伴う一般的なシナリオとして、Azure SQL のリーダーとライターを使用するシナリオがあります。リーダー モジュールは、Azure Machine Learning Studio の外部のデータ管理サービスからデータを実験に読み込むために使用します。ライター モジュールは、Azure Machine Learning Studio の外部のデータ管理サービスに実験のデータを保存するために使用します。Azure BLOB と Azure SQL のリーダー/ライターの詳細については、MSDN ライブラリの「[リーダー](https://msdn.microsoft.com/library/azure/dn905997.aspx)」と「[ライター](https://msdn.microsoft.com/library/azure/dn905984.aspx)」をご覧ください。前のセクションの例では、Azure BLOB リーダーと Azure BLOB ライターを使用しています。ここでは、Azure SQL リーダーと Azure SQL ライターの使用について説明します。

#### Azure SQL リーダー
Azure ML Studio では、実験を構築し、入力に Azure SQL リーダーを使用する Web サービスを公開できます。Azure SQL リーダーには Web サービス パラメーターとして公開できる接続プロパティがあり、実行時に接続プロパティの値をバッチ スコアリング要求に渡すことができます。

実行時、Web サービス パラメーターを設定するために、入力 Data Factory テーブルの詳細が Data Factory サービスによって使用されます。この Data Factory サービスとの統合が機能するためには、既定の名前 (Database server name、Database name、Server user account name、Server user account password) を使用する必要があります。

追加の Web サービス パラメーターがある場合は、アクティビティ JSON の **webServiceParameters** セクションを使用します。このセクションに Azure SQL リーダー パラメーターの値を指定した場合、入力 Azure SQL リンク サービスから取得された値はこの値で上書きされます。Azure SQL リーダーの値を webServiceParameters セクションに直接指定することはお勧めしません。このセクションは、追加のパラメーターの値を渡すために使用してください。

Azure Data Factory パイプライン経由で Azure SQL リーダーを使用するには、次の操作を行います。

- **Azure SQL のリンクされたサービス**を作成します。 
- **AzureSqlTableLocation** を使用する Data Factory **テーブル**を作成します。
- Data Factory **テーブル**をパイプライン JSON の **AzureMLBatchScoringActivity** の**入力**として設定します。 



#### Azure SQL ライター
Azure SQL リーダーと同様、Azure SQL ライターにも、Web サービス パラメーターとして公開されるプロパティがあります。Azure SQL ライターは、出力テーブルまたは入力テーブルに関連付けられたリンクされたサービスの設定を使用します。次の表に、リンクされたサービスが入力と出力で使用された場合について説明します。

<table>
<tr>
<td>出力/入力</td>
<td><b>入力は Azure SQL</b></td>
<td><b>入力は Azure BLOB</b></td>
</tr>
<tr>
<td><b>出力は Azure SQL</b></td>
<td><p>Data Factory サービスは、入力のリンクされたサービスからの接続文字列情報を使用して、"Database server name"、"Database name"、"Server user account name"、"Server user account password" の名前で Web サービス パラメーターを生成します。Azure ML Studio では、Web サービス パラメーターにこれらの既定の名前を使用する必要があります。</p>
<p>Azure ML モデルの Azure SQL リーダーと Azure SQL ライターが上記の同じ Web サービス パラメーターを共有する場合は問題ありません。Azure SQL リーダーと Azure SQL ライターが同じ Web サービス パラメーターを共有しない場合、たとえば、Azure SQL ライターが異なるパラメーター名 (Database server name1、Database name1、Server user account name1、Server user account password1 のように末尾に "1" が付加された名前) を使用する場合、アクティビティ JSON の webServiceParameters セクションを使用して、これらの出力 Web サービス パラメーターの値を渡す必要があります。</p>
<p>
他の Web サービス パラメーターについても、アクティビティ JSON の webServiceParameters セクションを使用して値を渡すことができます。 
</p>

</td>
<td>
<p>Data Factory サービスは、出力のリンクされたサービスからの接続文字列情報を使用して、"Database server name"、"Database name"、"Server user account name"、"Server user account password" の名前で Web サービス パラメーターを生成します。Azure ML Studio では、Web サービス パラメーターにこれらの既定の名前を使用する必要があります。</p>
<p>他の Web サービス パラメーターについても、アクティビティ JSON の webServiceParameters セクションを使用して値を渡すことができます。<p>入力 BLOB は、入力の場所として使用されます。</p>
</td>
</tr>
<tr>
<td><b>出力は Azure BLOB</b></td>
<td>Data Factory サービスは、入力のリンクされたサービスからの接続文字列情報を使用して、"Database server name"、"Database name"、"Server user account name"、"Server user account password" の名前で Web サービス パラメーターを生成します。Azure ML Studio では、Web サービス パラメーターにこれらの既定の名前を使用する必要があります。
</td>
<td>
<p>他の Web サービス パラメーターについても、アクティビティ JSON の webServiceParameters セクションを使用して値を渡す必要があります。</p> 

<p>BLOB は、入力の場所と出力の場所として使用されます。</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Azure SQL ライターが ID 列を上書きする場合、キー違反が発生することがあります。このような状況を回避するには、出力テーブルを構造化する必要があります。
> 
> ステージング テーブルをストアド プロシージャ アクティビティで使用して、行をマージしたり、スコアリングの前にデータを切り捨てたりできます。この方法を使用する場合は、executionPolicy の concurrency 設定を 1 に設定します。

### Web サービス パラメーターの使用例
#### Web サービス パラメーターを使用した AzureMLBatchScoringActivity のパイプライン

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	],

			"start": "2015-02-13T00:00:00Z",
        	"end": "2015-02-14T00:00:00Z"
		}
	}
 
上の JSON の例に関する説明:

- Azure ML モデルでは、Azure SQL リーダーと Azure SQL ライターの両方が使用されています。
- Web サービスを介して公開される場合、パラメーターの既定の名前が使用されます。
	- **リーダー**: Database server name、Database name、Server user account name、Server user account password。
	- **ライター**: Database server name1、Database name1、Server user account name1、Server user account password1。
	
		この場合、リーダーとライターはパラメーターを共有しません。  
- Data Factory サービスは、入力リーダーに一致する **Database server name**、**Database name**、**Server user account name**、**Server user account password** という名前の Web サービス パラメーターの値を自動的に生成します。したがって、下のアクティビティ JSON の **webServiceParameters** を介してこれらのパラメーター値を明示的に渡す必要はありません。  
- ライターのパラメーター ("1" サフィックスが付加されたパラメーター) は、Data Factory サービスによって自動的に設定されません。したがって、これらのパラメーターについては、アクティビティ JSON の **webServiceParameters** セクションで値を指定する必要があります。  
- **Customer ID**、**scored labels**、**scored probabilities** は、コンマ区切りの列として保存されます。 
- この例の **Data table name** は、出力データベースのテーブルに対応します。
- **start** と **end** の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。**end** の時刻は省略可能です。**end** プロパティの値を指定しない場合、"**start + 48 hours**" として計算されます。パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティとして指定します。JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](https://msdn.microsoft.com/library/dn835050.aspx)を参照してください。




## 関連項目

記事 | 説明
------ | ---------------
[Azure Data Factory の開発者用リファレンス][developer-reference] | この開発者用リファレンスは、コマンドレット、JSON スクリプト、.NET クラス ライブラリ、関数などの包括的なリファレンス コンテンツです。 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

 

<!---HONumber=July15_HO3-->