<properties title="Use Pig and Hive with Azure Data Factory" pageTitle="Azure Data Factory で Pig と Hive を使用する" description="Azure データ ファクトリから Azure HDInsight クラスターで Pig および Hive スクリプトを実行してデータを処理する方法について説明します。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Data Factory で Pig と Hive を使用する
Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、  特定の処理操作を実行します。たとえば、コピー アクティビティは、ソース ストレージからターゲット ストレージにデータをコピーします。Hive/Pig 変換を備えた HDInsight アクティビティは Azure HDInsight クラスターを使用して Hive/Pig スクリプトでデータを処理します。HDInsight アクティビティは 1 つ以上の入力を消費し、1 つ以上の出力を生成することが可能です。 

## この記事の内容

セクション | 説明
------- | -----------
[Pig JSON の使用例](#PigJSON) | このセクションでは Pig 変換を使用する HDInsight アクティビティを定義するための JSON スキーマについて説明します。 
[Hive JSON の使用例](#HiveJSON) | このセクションでは Hive 変換を使用する HDInsight アクティビティを定義するための JSON スキーマについて説明します。 
[Azure BLOB ストレージに格納されている Pig および Hive スクリプトを使用する](#ScriptInBlob) | Pig/Hive 変換を使用して HDInsight アクティビティから Azure Blob ストレージに格納されている Pig/Hive スクリプトを参照する方法を説明します。
[パラメーター化された Pig および Hive クエリ](#ParameterizeQueries) | Pig および Hive スクリプト内で使われるパラメーターの値を、JSON 内の **extendedProperties** プロパティを使用することによって指定する方法を説明します。
[Azure Data Factory で Hive を使用する](#Waltkthrough) | Hive を使ってデータ処理を行うパイプラインの作成手順を説明します。  



パイプライン JSON 内で Pig または Hive アクティビティを定義する際は、**type** プロパティを **HDInsightActivity** に設定する必要があります。

## <a name="PigJSON"></a> Pig JSON の使用例

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**以下の点に注意してください。**
	
- アクティビティの **type** は **HDInsightActivity** に設定します。
- **linkedServiceName** は **MyHDInsightLinkedService** に設定します。 
- **transformation** の **type** は **Pig** に設定します。
- **script** プロパティに対して Pig スクリプトをインラインで指定するか、または Azure BLOB ストレージ内にスクリプト ファイルを格納し、**scriptPath** プロパティを使用してそのファイルを参照することができますが、詳細については、この記事の後半で説明します。 
- **extendedProperties** を使用して Pig スクリプトのパラメーターを指定します。詳細については、この記事の後半で説明します。 


## <a name="HiveJSON"></a> ## Hive JSON の使用例


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**以下の点に注意してください。**
	
- アクティビティの **type** は **HDInsightActivity** に設定します。
- **linkedServiceName** は **MyHDInsightLinkedService** に設定します。 
- **transformation** の **type** は **Hive** に設定します。
- **script** プロパティに対して Hive スクリプトをインラインで指定するか、または Azure BLOB ストレージ内にスクリプト ファイルを格納し、**scriptPath** プロパティを使用してそのファイルを参照することができますが、詳細については、この記事の後半で説明します。 
- **extendedProperties** を使用して Hive スクリプトのパラメーターを指定します。詳細については、この記事の後半で説明します。 

> [WACOM.NOTE] コマンドレット、JSON スキーマ、およびスキーマ内のプロパティの詳細については、「[Data Factory Developer Reference (Data Factory 開発者向けリファレンス)](http://go.microsoft.com/fwlink/?LinkId=516908)」を参照してください。 


## <a name="ScriptInBlob"></a>Azure BLOB ストレージに格納されている Pig および Hive スクリプトを使用する
HDInsight クラスターに関連付けられている Azure BLOB ストレージに Pig/Hive スクリプトを格納すれば、以下に示す JSON 内のプロパティを使用して Pig/Hive アクティビティからそれらのスクリプトを参照できます。 

* **scriptPath** - Pig または Hive スクリプト ファイルへのパス
* **scriptLinkedService** - スクリプト ファイルを含む Azure Storage アカウント

次に示す JSON の例は Hive アクティビティを使用したサンプル パイプラインであり、**StorageLinkedService** で表される Azure BLOB ストレージ内の **adfwalkthrough** コンテナーにある **scripts** フォルダーに格納された **transformdata.hql** ファイルを参照しています。

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
					},
					"policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"retry": 1,
						"timeout": "01:00:00"
					}
            	}
        	]
      	}
	}

  

> [WACOM.NOTE] コマンドレット、JSON スキーマ、およびスキーマ内のプロパティの詳細については、「[Data Factry Developer Reference (Data Factory 開発者向けリファレンス)](http://go.microsoft.com/fwlink/?LinkId=516908)」を参照してください。

## <a name="ParameterizeQueries"></a>パラメーター化された Pig および Hive クエリ
Data Factory の Pig および Hive アクティビティでは、**extendedProperties** を使用することで、Pig および Hive スクリプト内で使われるパラメーターの値が指定できます。ExtendedProperties セクションは、パラメーター名とパラメーター値で構成されています。

**extendedProperties** を使用して Hive スクリプトのパラメーターを指定する方法については、以下の例を参照してください。パラメーター化された Hive  スクリプトを使用するには、次の手順に従います。

1.	**extendedProperties** 内でパラメーターを定義します。
2.	インライン Hive スクリプト (または) BLOB ストレージに格納された Hive スクリプト ファイルの中で **${hiveconf:parameterName}** を使用してパラメーターを参照します。

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


-  

## <a name="Walkthrough"></a>Azure Data Factory で Hive を使用する
### 前提条件
1. 「[Get started with Azure Data Factory (Azure Data Factory を使ってみる)][adfgetstarted]」の記事からチュートリアルを完了してください。
2. 上記チュートリアルで作成した **emp.txt** ファイルを **hiveinput\emp.txt** として BLOB ストレージ内のコンテナーにアップロードします。この構文で emp.txt ファイルをアップロードすると、**hiveinput** フォルダーが **adftutorial** コンテナー内に自動で作成されます。 
2. 以下の内容を記述した **hivequery.hql** ファイルを **C:\ADFGetStarted** の下の **Hive** という名前のサブフォルダ―内に作成します。
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;
		
3.  **hivequery.hql** を BLOB ストレージ内の **adftutorial** コンテナーにアップロードします。


### チュートリアル

#### 入力テーブルの作成
1. 以下の内容を記述した **HiveInputBlobTable.json** という名前の JSON ファイルを **C:\ADFGetStarted\Hive** フォルダー内に作成します。
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**以下の点に注意してください。**
	
	- location の **type** は **AzureBlobLocation** に設定します。
	- **linkedServiceName** は Azure ストレージ アカウントを定義する **MyBlobStore** に設定します。
	- **folderPath** は入力データ用の BLOB コンテナー内のフォルダーを指定します。 
	- **frequency=Day** および **interval=1** は、1 日単位でスライスが提供されることを意味します。
	- **waitOnExternal** は、このデータが別のパイプラインによって生成されるのではなく、データ ファクトリの外部で生成されることを意味します。 
	

	JSON プロパティの説明については、「[Data Factory Developer Reference (Data Factory 開発者向けリファレンス)][developer-reference]」を参照してください。  

2. **Azure PowerShell** を起動し、必要に応じて **AzureResourceManager** モードに切り替えます。
    		
    	Switch-AzureMode AzureResourceManager

5. **C:\ADFGetStarted\Hive** フォルダーに移動します。
6. 以下のコマンドを実行して **ADFTutorialDataFactory** 内に入力テーブルを作成します。

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	Data Factory コマンドレットの詳しい概要については、「[Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)][cmdlet-reference]」を参照してください。 
#### 出力テーブルの作成
        
1. 以下の内容を記述した **HiveOutputBlobTable.json** という名前の JSON ファイルを作成して **C:\ADFGetStarted\Hive** フォルダーに保存します。

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. 以下のコマンドを実行して **ADFTutorialDataFactory** 内に出力テーブルを作成します。
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### HDInsight クラスター用のリンクされたサービスを作成する
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。試しに、オンデマンド クラスターを使用してみましょう。 

#### オンデマンド HDInsight クラスターを使用するには
1. 以下の内容を記述した **HDInsightOnDemandCluster.json** という名前の JSON ファイルを作成して **C:\ADFGetStarted\Hive** フォルダーに保存します。


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. **Azure PowerShell** を起動し、以下のコマンドを実行して **AzureResourceManager** モードに切り替えます。**AzureResourceManager** モードでは Azure Data Factory コマンドレットが使用できます。

         switch-azuremode AzureResourceManager
		

3. **C:\ADFGetstarted\Hive** フォルダーに移動します。
4. 以下のコマンドを実行して、オンデマンド HDInsight クラスター用のリンクされたサービスを作成します。
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. **Azure Preview Portal** 内の **Data Factory** ブレードにテーブルおよびリンクされたサービスが表示されます。    
   
#### 独自の HDInsight クラスターを使用するには 

1. 以下の内容を記述した **MyHDInsightCluster.json** という名前の JSON ファイルを作成して **C:\ADFGetStarted\Hive** フォルダーに保存します。JSON ファイルを保存する前にクラスター名、ユーザー名、およびパスワードを適切な値に置き換えます。  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. **Azure PowerShell** を起動し、以下のコマンドを実行して **AzureResourceManager** モードに切り替えます。**AzureResourceManager** モードでは Azure Data Factory コマンドレットが使用できます。

         switch-azuremode AzureResourceManager
		

3. **C:\ADFGetstarted\Hive** フォルダーに移動します。
4. 以下のコマンドを実行して、独自の HDInsight クラスター用のリンクされたサービスを作成します。
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### パイプラインの作成およびスケジュール設定
   
1. 以下の内容を記述した **ADFTutorialHivePipeline.json** という名前の JSON ファイルを作成して **C:\ADFGetStarted\Hive** フォルダーに保存します。リンクされたサービスである **MyHDInsightCluster** の作成手順を実行した場合、独自のクラスターを使用するには、以下の JSON 内の **HDInsightOnDemandCluster** を **MyHDInsightCluster** に置き換えてください。 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		]
      		}
		}

2. 以下のコマンドを実行して、パイプラインを作成します。
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. パイプラインのスケジュールを設定します。
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] **StartDateTime** の値を現在の 3 日前の日付に置き換え、**EndDateTime** の値を現在の日付に置き換えます。StartDateTime と EndDateTime はいずれも UTC 時間で、[ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります。次に例を示します。2014-10-14T16:32:41Z。 
	> **EndDateTime** を指定しない場合は、"**StartDateTime + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9/9/9999** を **EndDateTime** として指定します。
  	
	出力テーブルは毎日生成されるようスケジュール設定されているので、3 つのスライスが生成されることになります。 

4. 記事「[Get started with Data Factory (Data Factory を使ってみる)][adfgetstarted]」の「[Monitor datasets and pipeline (データセットとパイプラインの監視)][adfgetstartedmonitoring]」セクションを参照してください。   

## 関連項目

記事 | 説明
------ | ---------------
[Azure Data Factory の概要][data-factory-introduction] | この記事では、Azure Data Factory のサービス、コンセプト、利用価値、およびサポートするシナリオを紹介します。
[Azure Data Factory を使ってみる][adf-getstarted] | この記事には、Azure BLOB から Azure SQL データベースにデータをコピーする Azure Data Factory サンプルの作成方法についての詳細なチュートリアルが記載されています。
[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources] | この記事には、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[チュートリアル: Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。 
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。  
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 


[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[開発者向けリファレンス]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure ポータル]: http://portal.azure.com
