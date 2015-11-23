<properties 
	pageTitle="Azure Machine Learning バッチ実行アクティビティを使用して予測パイプラインを作成する | Microsoft Azure" 
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
	ms.date="11/04/2015" 
	ms.author="spelluru"/>

# Azure Machine Learning アクティビティを使用して予測パイプラインを作成する   
## 概要

> [AZURE.NOTE]Azure Data Factory サービスについては、「[Azure Data Factory の概要](data-factory-introduction.md)」および「[Azure Data Factory を使用した初めてのパイプラインの作成](data-factory-build-your-first-pipeline.md)」を参照してください。

## はじめに

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) では、予測分析ソリューションをビルド、テスト、およびデプロイできます。大まかに次の 3 つの手順で行われます。

1. **トレーニング実験を作成する**。Azure ML Studio を使用します。これは、供給するトレーニング データを活用した予測分析モデルのトレーニングとテストに使用できる、コラボレーションと視覚化に対応した開発環境です。
2. **トレーニング実験を予測実験に変換する**。既存のデータでモデルがトレーニングされ、それを使用して新しいデータをスコア付けする準備ができると、スコア付け用に実験を用意し、合理化します。
3. **Web サービスとしてデプロイする**。Azure Web サービスとしてスコア付け実験を発行できます。この Web サービスのエンドポイントを使用して、ユーザーはモデルにデータを送信し、モデルの予測を受信できます。  

Azure Data Factory を使用すると、発行された [Azure Machine Learning][azure-machine-learning] Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。Azure Data Factory パイプラインで**バッチ実行アクティビティ**を使用すると、Azure ML Web サービスを呼び出して、データの予測を一括で行うことができます。詳細については、「[バッチ実行アクティビティを使用して、Azure ML Web サービスを呼び出す](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity)」を参照してください。

時間の経過と共に、Azure ML スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。次の手順を実行することで、Data Factory パイプラインから Azure ML モデルを再トレーニングできます。

1. 予測実験ではなく、トレーニング実験を Web サービスとして発行します。前のシナリオで予測実験を Web サービスとして公開したのと同様に Azure ML Studio で行います。
2. Azure ML バッチ実行アクティビティを使用して、トレーニング実験用 Web サービスを呼び出します。基本的には、Azure ML バッチ実行アクティビティを使用して、トレーニング Web サービスとスコア付け Web サービスの両方を呼び出すことができます。 
  
再トレーニングを実行したら、スコア付け Web サービス (Web サービスとして公開した予測実験) を新しくトレーニングを行ったモデルで更新する必要があります。次の手順に従って行います。

1. スコア付け Web サービスに既定以外のエンドポイントを追加します。Web サービスの既定のエンドポイントは更新できません。そのため、Azure 管理ポータルを使用して既定以外のエンドポイントを新しく作成する必要があります。この概念と手順については、[エンドポイントの作成](../machine-learning/machine-learning-create-endpoint.md)に関するページを参照してください。
2. 既存のスコア付け用 Azure ML のリンクされたサービスを、既定以外のエンドポイントを使用するように更新します。更新された Web サービスを使用するには、新しいエンドポイントの使用を開始する必要があります。
3. **Azure ML 更新リソース アクティビティ**を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。  

詳細については、「[更新リソース アクティビティを使用して Azure ML モデルを更新する](#updating-azure-ml-models-using-the-update-resource-activity)」を参照してください。

## バッチ実行アクティビティを使用して Azure ML Web サービスを呼び出す

Azure Data Factory を使用してデータの移動と処理を調整した後、Azure Machine Learning を使用してバッチを実行します。これを実現するのには、以下を行う必要があります。

1. Azure Machine Learning のリンクされたサービスを作成します。以下のものが必要ですが。
	1. Batch 実行 API の**要求 URI**。要求 URI は、Web サービス ページ (下図) で **[Batch 実行]** リンクをクリックするとわかります。
	1. 発行済みの Azure Machine Learning Web サービスの **API キー**。API キーは、発行した Web サービスをクリックするとわかります。 
 2. **AzureMLBatchExecution** アクティビティを使用します。

	![Machine Learning ダッシュボード](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### シナリオ: Azure Blob Storage のデータを参照する Web サービスの入力/出力を使用する
このシナリオの Azure Machine Learning Web サービスは、Azure Blob Storage 内のファイルのデータを使用して予測を作成し、Blob Storage に予測結果を保存します。次の JSON では、AzureMLBatchExecution アクティビティを使用する Azure Data Factory パイプラインが定義されています。このアクティビティは、入力としてデータセット **DecisionTreeInputBlob** を使用し、出力として **DecisionTreeResultBlob** を使用します。**DecisionTreeInputBlob** は **webServiceInput** JSON プロパティを使用して Web サービスに入力として渡され、**DecisionTreeResultBlob** は **webServiceOutputs** JSON プロパティを使用して Web サービスに出力として渡されます。アクティビティの入力/出力であるデータセットだけを、Web サービスの入力および出力として渡すことができます。


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE]AzureMLBatchExecution アクティビティの入力および出力だけを、パラメーターとして Web サービスに渡すことができます。たとえば、上の JSON スニペットでは、DecisionTreeInputBlob は AzureMLBatchExecution アクティビティへの入力であり、webServiceInput パラメーターを使用して Web サービスに入力として渡されます。

### 例

この例では、Azure Storage を使用して、入力データと出力データの両方を保持します。

この例に進む前に、「[Azure Data Factory を使用した初めてのパイプラインの作成][adf-build-1st-pipeline]」チュートリアルを行い、Data Factory エディターを使用してこの例の Data Factory アーティファクト (リンクされたサービス、データセット、パイプライン) を作成することをお勧めします。
 

1. **Azure Storage** 用の**リンクされたサービス**を作成します。入力ファイルと出力ファイルが異なるストレージ アカウントにある場合は、リンクされたサービスが 2 つ必要です。JSON の例を次に示します。

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. **入力**用の Azure Data Factory **データセット**を作成します。他の Data Factory データセットとは異なり、これらは **folderPath** 値と **fileName** 値を含む必要があります。パーティション分割を使用すると、各バッ チ実行 (各データ スライス) で一意の入力ファイルと出力ファイルを処理または生成できます。入力を CSV ファイル形式に変換し、これを各スライスのストレージ アカウントに配置するには、いくつかのアップストリーム アクティビティを含める必要があります。その場合は、次の例に示す **external** および **externalData** の設定を含めないと、DecisionTreeInputBlob は別のアクティビティの出力データセットになります。

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
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
	
	入力 csv ファイルには列ヘッダー行がある必要があります。**コピー アクティビティ**を使用している csv を Blob Storage に作成または移動する場合、シンクの **blobWriterAddHeader** プロパティを **true** に設定する必要があります。次に例を示します。
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	csv ファイルにヘッダー行がない場合、次のエラーが表示される場合があります: **アクティビティ エラー: 文字列の読み取りエラー。予期しないトークン: StartObject。パス ''、行 1、位置 1**。
3. **出力**用の Azure Data Factory **データセット**を作成します。この例では、パーティション分割を使用して各スライスの実行ごとに一意の出力パスを作成します。これを行わないと、アクティビティはファイルを上書きします。

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. **AzureMLLinkedService** 型の**リンクされたサービス**を作成し、API キーとモデルのバッチ実行 URL を入力します。
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. 最後に、**AzureMLBatchExecution** アクティビティを含むパイプラインを作成します。このパイプラインは、入力データセットから入力ファイルの場所を取得し、Azure Machine Learning バッチ実行 API を呼び出して、出力データセットで指定された BLOB にバッチ実行の出力をコピーします。 

	> [AZURE.NOTE]AzureMLBatchExecution アクティビティは、0 個以上の入力と 1 個以上の出力を使用できます。

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	**start** と **end** の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。**end** の時刻は省略可能です。**end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティとして指定します。JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](https://msdn.microsoft.com/library/dn835050.aspx)を参照してください。

	> [AZURE.NOTE]AzureMLBatchExecution アクティビティへの入力の指定は省略可能です。

### シナリオ: リーダー/ライター モジュールを使用してさまざまなストレージ内のデータを参照する

Azure ML を実験するときのもう 1 つの一般的なシナリオは、リーダー モジュールとライター モジュールを使用することです。リーダー モジュールは実験にデータを読み込むために使用し、ライター モジュールは実験からデータを保存するために使用します。リーダーおよびライター モジュールの詳細については、MSDN ライブラリの「[リーダー](https://msdn.microsoft.com/library/azure/dn905997.aspx)」と「[ライター](https://msdn.microsoft.com/library/azure/dn905984.aspx)」をご覧ください。

リーダー/ライター モジュールの各プロパティには Web サービスのパラメーターを使用するのがよい方法です。これらの Web パラメーターを使用すると、実行時に値を構成できます。たとえば、Azure SQL Database: XXX.database.windows.net を使用するリーダー モジュールで実験を作成できます。Web サービスをデプロイした後、Web サービスのコンシューマーを有効にして、YYY.database.windows.net という名前の別の Azure SQL Server を指定できます。Web サービスのパラメーターを使用して、この値を構成できます。

> [AZURE.NOTE]Web サービスの入力と出力は、Web サービスのパラメーターとは異なるものです。最初のシナリオでは、Azure ML Web サービスに対して入力と出力を指定する方法を説明しました。このシナリオでは、リーダー/ライター モジュールのプロパティに対応するパラメーターを Web サービスに渡します。

Web サービス パラメーターを使用するシナリオを見てみましょう。Azure Machine Learning がサポートするデータ ソース (例: Azure SQL Database) の 1 つからデータを読み取るためにリーダー モジュールを使用する Azure Machine Learning Web サービスをデプロイしてあります。バッチ実行が実行された後、ライター モジュールを使用して結果が書き込まれます (Azure SQL Database)。Web サービスの入力と出力は実験では定義されていません。この場合は、リーダー/ライター モジュールに関連する Web サービス パラメーターを設定することをお勧めします。これにより、AzureMLBatchExecution アクティビティを使用するときにリーダー/ライター モジュールを構成できます。Web サービスのパラメーターは、次に示すように、アクティビティの JSON の **globalParameters** セクションで指定します。


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

また、次の例に示すように、[Data Factory 関数](https://msdn.microsoft.com/library/dn835056.aspx)を使用して、Web サービス パラメーターの値を渡すこともできます。

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Web サービス パラメーターでは大文字と小文字が区別されるため、アクティビティ JSON に指定した名前が Web サービスによって公開されている名前と一致していることを確認してください。

### リーダー モジュールを使用して Azure BLOB の複数のファイルからデータを読み取る
ビッグ データ パイプライン (Pig、Hive など) は、拡張子が付いていない出力ファイルを 1 つ以上生成できます。たとえば、外部 Hive テーブルを指定するとき、外部 Hive テーブルのデータを 000000\_0 という名前で Azure Blob Storage に格納できます。実験では、リーダー モジュールを使用して複数のファイルを読み取り、予測に使用できます。

Azure Machine Learning の実験でリーダー モジュールを使用する場合は、入力として Azure BLOB を指定できます。Azure Blob Storage 内のファイルは、HDInsight で実行する Pig および Hive スクリプトによって生成される出力ファイル (例: 000000\_0) でもかまいません。リーダー モジュールを使用すると、次に示すように、ファイルを含むコンテナー/フォルダーを指し示すようにリーダー モジュールの **Path to container, directory or blob** プロパティを構成することによって、(拡張子がない) ファイルを読み取ることができます。アスタリスク (つまり *) は、**コンテナー/フォルダー内のすべてのファイル (つまり、data/aggregateddata/year=2014/month-6/*)** が実験の一部として読み取られることを指定します。

![Azure BLOB のプロパティ](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### 例 
#### Web サービス パラメーターを使用した AzureMLBatchExecution のパイプライン

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
上の JSON の例に関する説明:

- デプロイされた Azure Machine Learning Web サービスは、リーダー モジュールとライター モジュールを使用して、Azure SQL Database のデータを読み書きします。この Web サービスでは、Database server name、Database name、Server user account name、Server user account password という 4 つのパラメーターが公開されています。  
- **start** と **end** の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。**end** の時刻は省略可能です。**end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティとして指定します。JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](https://msdn.microsoft.com/library/dn835050.aspx)を参照してください。
 

## 更新リソース アクティビティを使用して Azure ML モデルを更新する
時間の経過と共に、Azure ML スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。再トレーニングが完了したら、再トレーニング済みの ML モデルでスコア付け Web サービスを更新する必要があります。Web サービスを使用して Azure ML モデルの再トレーニングと更新を有効にするための標準的な手順を次に示します。

1. [Azure ML Studio](https://studio.azureml.net) で実験を作成します。 
2. モデルの準備が整ったら、Azure ML Studio を使用して、**トレーニング実験**とスコア付け/**予測実験**の両方の Web サービスを発行します。

次の表で、この例で使用する Web サービスについて説明します。詳細については、「[プログラムによる Machine Learning のモデルの再トレーニング](../machine-learning/machine-learning-retrain-models-programmatically.md)」を参照してください。

| Web サービスの種類 | 説明 
| :------------------ | :---------- 
| **トレーニング Web サービス** | トレーニング データを受信し、トレーニング済みのモデルを作成します。再トレーニングの出力は、Azure Blob Storage 内の .ilearner ファイルになります。**既定のエンドポイント**が、トレーニング実験を Web サービスとして発行するときに自動的に作成されます。エンドポイントは複数作成することができますが、この例では、既定のエンドポイントのみを使用します。 |
| **スコア付け Web サービス** | ラベルの付いていないデータの例を受信し、予測を作成します。予測の出力は、実験の構成に応じてさまざまな形式 (.csv ファイル、Azure SQL Database の行など) をとります。既定のエンドポイントが、予測実験を Web サービスとして発行するときに自動的に作成されます。[Azure ポータル](https://manage.windowsazure.com)を使用して、2 つ目の**更新可能な既定以外のエンドポイント**を作成する必要があります。エンドポイントは複数作成することができますが、この例では、更新可能な既定以外のエンドポイントを 1 つだけ使用します。手順の詳細については、[エンドポイントの作成](../machine-learning/machine-learning-create-endpoint.md)に関する記事を参照してください。       
 
次の図は、Azure ML でのトレーニングとスコア付けのエンドポイントの関係を示しています。

![Web サービス](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


**Azure ML バッチ実行アクティビティ**を使用して、**トレーニング Web サービス**を呼び出すことができます。これは、データのスコア付け用 Azure ML Web サービス (スコア付け Web サービス) を呼び出す場合と同じです。前のセクションで、Azure Data Factory パイプラインから Azure ML Web サービスを呼び出す方法について詳しく説明しています。
  
**Azure ML 更新リソース アクティビティ**を使用して**スコア付け Web サービス**を呼び出し、新しくトレーニングを行ったモデルで Web サービスを更新することができます。前の表で説明したように、更新可能な既定以外のエンドポイントを作成して使用する必要があります。データ ファクトリ内の既存のリンクされたサービスも、既定以外のエンドポイントを使用するように更新して、常に最新の再トレーニングを行ったモデルが使用されるようにする必要があります。

次のシナリオでは、Azure Data Factory パイプラインから Azure ML モデルの再トレーニングと更新を行う方法について、例を用いて詳しく説明します。
 
### シナリオ: Azure ML モデルの再トレーニングと更新
このセクションでは、**Azure ML バッチ実行アクティビティ**を使用して、モデルを再トレーニングし、**Azure ML 更新リソース アクティビティ**を使用して、スコア付け Web サービスでモデルを更新するサンプル パイプラインを提供します。この例では、すべてのリンクされたサービス、データ セット、およびパイプラインの JSON スニペットも提供されます。

サンプル パイプラインのダイアグラム ビューを次に示します。ご覧のように、Azure ML バッチ実行アクティビティはトレーニングの入力を受け取り、トレーニングの出力 (iLearner ファイル) を作成します。Azure ML 更新リソース アクティビティはトレーニングの出力を受け取り、スコア付け Web サービスのエンドポイントでモデルを更新します。更新リソース アクティビティは出力を作成しません。placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### Azure Blob Storage のリンクされたサービス:
Azure Storage には次のデータが格納されています。

- トレーニング データ。これは、Azure ML トレーニング Web サービス用の入力データです。  
- iLearner ファイル。これは、Azure ML トレーニング Web サービスの出力です。更新リソース アクティビティへの入力としても使用します。  
   
リンクされたサービスのサンプルの JSON 定義を次に示します。

	{
		"name": "StorageLinkedService",
	  	"properties": {
	    	"type": "AzureStorage",
			"typeProperties": {
	    		"connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
			}
		}
	}


#### トレーニングの入力データセット:
次のデータセットは、Azure ML トレーニング Web サービス用の入力トレーニング データを示しています。Azure ML バッチ実行アクティビティはこのデータセットを入力として使用します。

	{
	    "name": "trainingData",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "labeledexamples",
	            "fileName": "labeledexamples.arff",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
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

#### トレーニングの出力データセット:
次のデータセットは、Azure ML トレーニング Web サービスの出力 iLearner ファイルを示しています。Azure ML バッチ実行アクティビティがこのデータセットを作成します。このデータセットは、Azure ML 更新リソース アクティビティへの入力としても使用されます。

	{
	    "name": "trainedModelBlob",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "trainingoutput",
	            "fileName": "model.ilearner",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        }
	    }
	}

#### Azure ML トレーニング エンドポイント用のリンクされたサービス 
次の JSON スニペットは、トレーニング Web サービスの既定のエンドポイントを示す Azure Machine Learning のリンクされたサービスを定義します。

	{	
		"name": "trainingEndpoint",
	  	"properties": {
	    	"type": "AzureML",
	    	"typeProperties": {
	    		"mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
	      		"apiKey": "myKey"
	    	}
	  	}
	}

**Azure ML Studio** で、次の操作を行い、**mlEndpoint** と **apiKey** の値を取得します。

1. 左側のメニューで **[Web サービス]** をクリックします。
2. Web サービスの一覧で、**[トレーニング Web サービス]** をクリックします。 
3. **[API キー]** ボックスの隣にある [コピー] をクリックし、クリップボードに API キーをコピーします。Data Factory JSON エディターにこの API キーを貼り付けます。
4. **Azure ML studio** で、**[バッチ実行]** リンクをクリックし、**[要求]** セクションで **[要求 URI]** をコピーします。コピーした要求 URI を Data Factory JSON エディターに貼り付けます。   


#### Azure ML の更新可能なスコア付けエンドポイント用のリンクされたサービス:
次の JSON スニペットは、スコア付け Web サービスの更新可能な既定以外のエンドポイントを参照する Azure Machine Learning のリンクされたサービスを定義します。

	{
	    "name": "updatableScoringEndpoint2",
	    "properties": {
	        "type": "AzureML",
	        "typeProperties": {
	            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
	            "apiKey": "endpoint2Key",
	            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
	        }
	    }
	}


Azure ML のリンクされたサービスの作成とデプロイの前に、[エンドポイントの作成](../machine-learning/machine-learning-create-endpoint.md)に関する記事に記載された手順に従い、スコア付け Web サービス用に 2 つ目の (更新可能な既定以外の) エンドポイントを作成します。

更新可能な既定以外のエンドポイントを作成したら、**[バッチ実行]** をクリックし、**mlEndpoint** JSON プロパティの URI の値を取得します。さらに、**[リソースの更新]** のリンクをクリックし、**updateResourceEndpoint** JSON プロパティの URI の値を取得します。API キーは、エンドポイントのページにあります (右下隅)。

![updatable endpoint](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### プレースホルダーの出力データセット:
Azure ML 更新リソース アクティビティは出力を作成しませんが、Azure Data Factory では、パイプラインのスケジュールを進めるために出力データセットが必要です。この例では、ダミー/プレースホルダーのデータセットを使用しています。

	{
	    "name": "placeholderBlob",
	    "properties": {
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        },
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "any",
	            "format": {
	                "type": "TextFormat"
	            }
	        }
	    }
	}


#### パイプライン
パイプラインには、**AzureMLBatchExecution** と **AzureMLUpdateResource** の 2 つのアクティビティが含まれています。Azure ML バッチ実行アクティビティはトレーニング データを入力として使用し、.iLearner ファイルを出力として作成します。このアクティビティは、トレーニング Web サービス (Web サービスとして公開されたトレーニング実験) と入力トレーニング データを呼び出し、Web サービスから ilearner ファイルを受け取ります。placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


	{
	    "name": "pipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "retraining",
	                "type": "AzureMLBatchExecution",
	                "inputs": [
	                    {
	                        "name": "trainingData"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "typeProperties": {
	                    "webServiceInput": "trainingData",
	                    "webServiceOutputs": {
	                        "output1": "trainedModelBlob"
	                    }              
	                 },
	                "linkedServiceName": "trainingEndpoint",
	                "policy": {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "02:00:00"
	                }
	            },
	            {
	                "type": "AzureMLUpdateResource",
	                "typeProperties": {
	                    "trainedModelName": "Training Exp for ADF ML [trained model]",
	                    "trainedModelDatasetName" :  "trainedModelBlob"
	                },
	                "inputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "placeholderBlob"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "name": "AzureML Update Resource",
	                "linkedServiceName": "updatableScoringEndpoint2"
	            }
	        ]
	    }
	}




## よく寄せられる質問

**Q:** AzureMLBatchScoring アクティビティを使用しています。AzureMLBatchExecution アクティビティを使用するように切り替えた方がいいですか?

**A:** はい。AzureMLBatchScoring アクティビティを使用して Azure Machine Learning と統合している場合は、最新の AzureMLBatchExecution アクティビティを使用することをお勧めします。AzureMLBatchScoring アクティビティは非推奨になっており、将来のリリースで削除されます。

AzureMLBatchExecution アクティビティは、Azure SDK および Azure PowerShell の 2015 年 8 月のリリースで導入されました。

AzureMLBatchExecution アクティビティには入力は必要ありません (入力の依存関係が必要ない場合)。また、Web サービス入力と Web サービス出力を使用するか、または使用しないかを、明示できます。Web サービス入力/出力を使用する場合は、使用する関連 Azure BLOB データセットを指定できます。さらに、Web サービスによって提供される Web サービス パラメーターの値を明確に指定できます。

それでも引き続き AzureMLBatchScoring アクティビティを使用したい場合は、「[Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する](data-factory-create-predictive-pipelines.md)」で詳細をご覧ください。


**Q:** ビッグ データ パイプラインによって生成される複数のファイルがあります。AzureMLBatchExecution アクティビティを使用して、すべてのファイルで処理できますか。

**A:** はい。詳しくは、「**リーダー モジュールを使用して Azure BLOB の複数のファイルからデータを読み取る**」をご覧ください。


## 関連項目

- [Azure ブログの投稿: Azure Data Factory と Azure Machine Learning の概要](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=Nov15_HO3-->