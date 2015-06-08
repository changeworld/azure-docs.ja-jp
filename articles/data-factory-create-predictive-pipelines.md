<properties 
	pageTitle="Data Factory - Data Factory および Machine Learning を使用して予測パイプラインを作成する | Azure" 
	description="作成する方法について説明します Azuer データ ファクトリと Azure の機械学習を使用して予測のパイプラインの作成。" 
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
	ms.date="05/05/2015" 
	ms.author="spelluru"/>

# Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する 
## 概要
運用を実現するパブリッシュ [Azure 機械学習][azure-machine-learning] Azure のデータのファクトリのパイプライン内のモデルです。これらのパイプラインは、予測のパイプラインと呼ばれます。予測パイプラインを作成するには、次が必要です。

-	発行されたワークスペース モデルの API キーとバッチ スコアリング URL (下図を参照)。
-	Azure blob の記憶域、入力の CSV ファイル (または)、入力を格納する Azure SQL データベースを保持しているデータを評価を受けたします。 
-	結果のスコア、CSV ファイル (または)、出力データを保持する Azure SQL データベースを保持する、Azure blob ストレージです。 

	![マシン ラーニングのダッシュ ボード][machine-learning-dashboard]

	URL を上の図に示されているからでとして取得される、AzureMLLinkedService のスコアリング バッチ マイナス ' * * * * ヘルプ '。 https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

A **予測パイプライン** はこれらの部分から成ります。

-	入力テーブルと出力テーブル
-	Azure のストレージと Azure の SQL と Azure ML サービスにリンク
-	Azure ML バッチ スコアリング アクティビティのパイプライン

> [AZURE.NOTE]Azure データ ファクトリ (ADF) パイプラインでは、発行された Azure マシン ラーニング Web サービスによって公開されている Web サービスのパラメーターを使用することができます。詳細については、この記事では、Web サービスのパラメーターのセクションを参照してください。

## 例
この例は、入力と出力の両方のデータを保持するために Azure のストレージを使用します。Azure のストレージを使用する代わりに、Azure SQL データベースを使用することもできます。

学習することをお勧め、 [Azure のデータのファクトリを使い始める][adf-getstarted] この例では、この例とデータの工場出荷時のアイテム (関連付けられているサービス、テーブル、パイプライン) を作成するデータ、工場出荷時エディターを使用して開始する前にチュートリアルです。
 

1. Azure ストレージ用のリンクされたサービスを作成します。スコアリングの入力ファイルと出力ファイルが異なるストレージ アカウントにある場合は、リンクされたサービスが 2 つ必要です。JSON の例を次に示します。

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. 入力と出力の Azure Data Factory テーブルを作成します。他のデータの工場出荷時のテーブルとは異なりこれら必要があります両方含ま両方 **folderPath** と **fileName** 値。パーティション分割を使用すると、各バッ チ実行 (各データ スライス) で一意の入力ファイルと出力ファイルを処理または生成できます。入力を CSV ファイル形式に変換し、これを各スライスのストレージ アカウントに配置するには、いくつかのアップストリーム アクティビティを含める必要があります。その場合は、次の例に示す「waitOnExternal」設定を含めないと、ScoringInputBlob は別のアクティビティの出力テーブルになります。

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
	
	Csv ファイルのスコアを計算してからバッチには、列ヘッダー行が必要です。使用している場合、 **コピー アクティビティ** を作成または移動、csv、blob ストレージに、シンク プロパティを設定する必要があります **blobWriterAddHeader** に **true**です。次に例を示します。
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Csv ファイルにヘッダー行があるない場合、次のエラーを参照して可能性があります。 **アクティビティでエラー: 文字列を読み取り中にエラー。予期しないトークン: StartObject します。パス '、行 1、位置 1 の**です。
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


4. リンクの種類のサービスを作成: **AzureMLLinkedService**, 、API キーおよびモデルのバッチのスコアリングの URL を提供します。
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. 含む、パイプラインの最後に、作成、 **AzureMLBatchScoringActivity**です。これは、入力テーブルから入力ファイルの場所を取得し、AzureML バッチ スコアリング API を呼び出し、出力テーブルで指定された BLOB にバッチ スコアリング出力をコピーします。他の Data Factory テーブルとは異なり、AzureMLBatchScoringActivity は 1 つの入力テーブルと 1 つの出力テーブルのみを含むことができます。

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
		        ]
		    }
		}


## Web サービスのパラメーター
Azure データ ファクトリ (ADF) パイプラインでは、発行された Azure マシン ラーニング Web サービスによって公開されている Web サービスのパラメーターを使用することができます。Azure の機械学習、実験を作成およびを web サービスとして発行し、複数の ADF パイプラインまたは活動では、Web サービスのパラメーターを使用して、さまざまな入力に渡すことではその web サービスを使用することができます。

### Web サービスのパラメーターの値を渡す
追加、 **変換** セクション、 **AzureMLBatchScoringActivty** セクションで、パイプラインの次の例に示すように、そのセクション内の Web サービスのパラメーターの値を指定する JSON。

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


使用することもできます [工場出荷時のデータ関数](https://msdn.microsoft.com/library/dn835056.aspx) Web の値を渡すときにパラメーターがサービスに次の例に示すようにします。

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Web サービスのパラメーターは大文字小文字が区別、ように、アクティビティで指定した名前 JSON の一致、Web サービスによって公開されているものです。

### Azure SQL のリーダーとライター
Web サービスのパラメーターを使用するための一般的なシナリオは、Azure SQL のリーダーとライターの使用です。リーダー モジュールは、Azure マシン ラーニング Studio の外部のデータ管理サービスからの実験にデータを読み込むために使用し、ライター モジュールに Azure マシン ラーニング Studio の外部のデータ管理サービスへの実験からデータを保存します。詳細については Azure Blob または Azure SQL の読み取り/書き込み、次を参照してください。 [リーダー](https://msdn.microsoft.com/library/azure/dn905997.aspx) と [ライター](https://msdn.microsoft.com/library/azure/dn905984.aspx) MSDN ライブラリのトピックです。前のセクションの例では、Azure Blob のリーダーとライターの Azure Blob を使用します。このセクションでは、Azure SQL のリーダー ロールおよび Azure の SQL ライターを使用してについて説明します。

#### Azure SQL リーダー
Azure ML Studio で実験をビルドし、入力に対して、Azure SQL のリーダーに Web サービスを公開できます。Azure SQL を持つ接続のプロパティは、Web サービスのパラメーターとして公開されるバッチ要求のスコアリングで実行時に渡される接続のプロパティの値を許可します。

実行時に、[Web サービスのパラメーターを埋めるために、入力データの工場出荷時のテーブルの詳細、出荷時のデータ サービスによって使用されます。必要がありますを使用することの既定の名前 (データベース サーバー名、データベース名、サーバーのユーザー アカウント名、サーバーのユーザー アカウントのパスワード)、Web サービスのパラメーター、データのファクトリのサービスとの統合機能に注意してください。

すべて追加の Web サービスのパラメーターを使用した場合、 **webServiceParameters** アクティビティ JSON のセクションです。このセクションでの Azure SQL のリーダーのパラメーターの値を指定する場合、値によって、入力リンクされている Azure の SQL サービスから取得する値が上書きされます。WebServiceParameters セクションで直接 Azure SQL のリーダーの値を指定はお勧めしません。セクションを使用して、追加のパラメーターの値を渡します。

このパイプラインは、Azure のデータのファクトリを使用して、Azure SQL のリーダーを使用するのには、次の手順で行います。

- 作成、 **サービスが Azure SQL にリンクされている**です。 
- データのファクトリを作成する **テーブル** を使用して **AzureSqlTableLocation**です。
- そのデータのファクトリを設定する **テーブル** として、 **入力** の **AzureMLBatchScoringActivity** 、パイプラインの JSON でします。 



#### Azure の SQL ライター
として Azure SQL のリーダーを使用する Azure の SQL ライターこともできます Web サービスのパラメーターとして公開されるプロパティ。Azure の SQL ライターは、リンクに、入力テーブルまたは出力テーブルに関連付けられているサービスからいずれかの設定を使用します。次の表では、入力と出力リンクのサービスを使用するとサービス リンクされている場合について説明します。

<table>
<tr>
<td>出力または入力</td>
<td><b>入力が Azure の SQL</b></td>
<td><b>入力が Azure Blob です。</b></td>
</tr>
<tr>
<td><b>出力は、Azure の SQL</b></td>
<td><p>出荷時のデータ サービスには、リンクしているサービスからの接続文字列情報を使用して名前を持つ web サービスのパラメーターを生成する。"データベース サーバー名"、"データベース名"、"サーバーのユーザー アカウント名"、"サーバーのユーザー アカウントのパスワード"です。Azure の ML Studio で Web サービスのパラメーターの既定の名前ではこれらを使用することに注意してください。</p>
<p>Azure SQL のリーダーと Azure の ML モデルでの Azure の SQL ライターは、上記で説明した Web サービスの同じパラメーターを共有する場合は十分です。共有していない Web サービスの同じ paramers、たとえば、Azure の SQL ライターでパラメーター名を使用する場合の場合: server name1、データベース name1、サーバーのユーザー アカウントの name1、サーバーのユーザー データベース ('1' 末尾) を含む password1 のアカウントは、アクティビティの JSON の webServiceParameters セクションで、これらの出力の web サービスのパラメーターの値を渡す必要があります。</p>
<p>
JSON のアクティビティの webServiceParameters セクションを使用して他の任意の Web サービス パラメーターの値を渡すことができます。 
</p>

</td>
<td>
<p>出荷時のデータ サービスは、出力リンクされているサービスからの接続文字列情報を使用して名前を持つ web サービスのパラメーターを生成する。"データベース サーバー名"、"データベース名"、"サーバーのユーザー アカウント名"、"サーバーのユーザー アカウントのパスワード"です。Azure の ML Studio で Web サービスのパラメーターの既定の名前ではこれらを使用することに注意してください。</p>
<p>JSON のアクティビティの webServiceParameters セクションを使用して他の任意の Web サービス パラメーターの値を渡すことができます。<p>入力の blob は、入力場所として使用されます。</p>
</td>
</tr>
<tr>
<td><b>出力は、Azure Blob です。</b></td>
<td>出荷時のデータ サービスには、リンクしているサービスからの接続文字列情報を使用して名前を持つ web サービスのパラメーターを生成する。"データベース サーバー名"、"データベース名"、"サーバーのユーザー アカウント名"、"サーバーのユーザー アカウントのパスワード"です。Azure の ML Studio で Web サービスのパラメーターの既定の名前ではこれらを使用することに注意してください。
</td>
<td>
<p>任意の JSON のアクティビティの WebServiceParameters セクションを使用して Web サービスのパラメーターの値を渡す必要があります。</p> 

<p>Blob は、入力と出力の場所として使用されます。</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Azure の SQL ライターは、id 列を上書きすることは、キーの違反が発生する可能性があります。この状況を回避するには、出力テーブルを構成することを確認してください。
> 
> ストアド プロシージャのアクティビティを含むステージング テーブルを使用して、行をマージするかを評価する前に、データを切り捨てることができます。この方法を使用する場合は、実行ポリシーの同時実行の設定を 1 に設定します。

### Web サービスのパラメーターを使用した例
#### パイプラインには、Web サービスのパラメーターを持つ AzureMLBatchScoringActivity

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
	    	]
		}
	}
 
上記の JSON 例を示します。

- Azure SQL のリーダーと Azure の SQL ライターの両方が Azure ML モデルで使用します。
- Web サービス経由で公開されていると、に、パラメーターに既定の名前が使用します。
	-  **リーダー**: データベースのサーバー名、データベース名、サーバーのユーザー アカウント名、およびサーバーのユーザー アカウントのパスワード。
	-  **ライター**: server name1、データベース name1、サーバーのユーザー アカウントの name1、およびサーバーのユーザー アカウントの password1 データベースします。
	
		リーダーとライターを共有しないパラメーターここで注意してください。  
- データのファクトリのサービスは、名前を持つ Web サービスのパラメーターの値を自動的に生成されます **データベース サーバー名**, 、**データベース名**, 、**サーバーのユーザー アカウント名**, と **Server のユーザー アカウントのパスワード**, 、入力リーダーの名前と一致します。そのためを使用してこれらのパラメーターの値を明示的に渡す必要はありません **webServiceParameters** 、アクティビティの次の JSON にします。  
- ライター ('1' サフィックスが付いているもの) のパラメーターが自動的に入力されていません、出荷時のデータ サービスによってです。これらのパラメーターの値を指定する必要があるため、 **webServiceParameters** アクティビティ JSON のセクションです。  
- **顧客 ID**, 、**ラベルの評価を受けた**, 、および **確率の評価を受けた** はコンマで区切って列として保存します。 
-  **データ テーブル名** この例では、出力のデータベース内のテーブルに対応します。




## 関連項目

記事 | 説明
------ | ---------------
[Azure のデータの工場出荷時の開発者向けリファレンス][developer-reference] | コマンドレット、スクリプトの JSON、.NET クラス ライブラリ、関数などの包括的な参照の内容を開発者向けリファレンスには. 

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

<!---HONumber=GIT-SubDir-->