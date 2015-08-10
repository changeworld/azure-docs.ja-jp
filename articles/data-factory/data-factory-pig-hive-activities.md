<properties 
	pageTitle="Azure Data Factory で Pig と Hive を使用する" 
	description="Azure データ ファクトリから Azure HDInsight クラスターで Pig および Hive スクリプトを実行してデータを処理する方法について説明します。" 
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
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# Data Factory で Pig と Hive を使用する
Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。この記事では、Azure Data Factory パイプラインで HDInsight アクティビティの Pig 変換と Hive 変換を使用する方法について説明します。Azure Data Factory パイプラインから HDInsight クラスターで MapReduce プログラムを実行する方法の詳細については、「[Data Factory から MapReduce プログラムを起動する][data-factory-map-reduce]」 を参照してください。

## チュートリアル: Azure Data Factory で Hive を使用する
このチュートリアルでは、Data Factory パイプラインで HDInsight のアクティビティの Hive 変換を使用する手順を説明します。

### 前提条件
1. 「[Azure Data Factory を使ってみる][adfgetstarted]」の記事にあるチュートリアルを完了してください。
2. 以下の内容を記述した **hivequery.hql** ファイルを **C:\\ADFGetStarted** の下の **Hive** という名前のサブフォルダー内に作成します。
    		
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

	> [AZURE.NOTE]**Tez** エンジンを使用して HQL ファイルで Hive クエリを実行するには、ファイルの先頭に "\*\*set hive.execution.engine=tez\*\*;" を追加します。
		
3.  **hivequery.hql** を BLOB ストレージ内の **adftutorial** コンテナーにアップロードします。


### チュートリアル

#### 出力テーブルの作成
        
1. **Data Factory エディター**で、**[新しいデータセット]** をクリックし、コマンド バーの **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON スクリプトを、次の JSON スクリプトに置き換えます。

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. コマンド バーの **[デプロイ]** をクリックして、テーブルをデプロイします。


### HDInsight クラスター用のリンクされたサービスを作成する
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターは、データを処理する Azure Data Factory サービスによって自動的に作成および管理されます。オンデマンド HDInsight のリンクされたサービスの詳細については、「[Azure HDInsight オンデマンドのリンクされたサービス](https://msdn.microsoft.com/library/dn893526.aspx)」を参照してください。試しに、オンデマンド クラスターを使用してみましょう。オンデマンド HDInsight クラスターの作成には 15 分以上かかります。また、HDInsight クラスターでジョブが稼働している時間に対してのみ課金されます。

#### オンデマンド HDInsight クラスターを使用するには
1. コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[オンデマンド HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。 
	1. **clusterSize** プロパティには、HDInsight クラスターのサイズを指定します。
	2. **jobsContainer** プロパティには、クラスター ログを格納する既定のコンテナーの名前を指定します。このチュートリアルでは、「**adfjobscontainer**」と指定します。
	3. **timeToLive** プロパティには、クラスターが削除されるまでの間にアイドル状態でいられる時間を指定します。 
	4. **version** プロパティには、使用する HDInsight のバージョンを指定します。このプロパティを除外した場合は、最新バージョンが使用されます。  
	5. **linkedServiceName** には、入門チュートリアルで作成した **StorageLinkedService** を指定します。 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. コマンド バーの **[デプロイ]** をクリックして、リンクされたサービスをデプロイします。
   
   
#### 独自の HDInsight クラスターを使用するには 

1. コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。 
	1. **clusterUri** プロパティには、HDInsight の URL を入力します。たとえば、https://<clustername>.azurehdinsight.net/ などです。     
	2. **UserName** プロパティには、HDInsight クラスターにアクセスできるユーザー名を入力します。
	3. **Password** プロパティには、ユーザーのパスワードを入力します。 
	4. **LinkedServiceName** プロパティには、「**StorageLinkedService**」と入力します。これは、入門チュートリアルで作成したリンク サービスです。 

2. コマンド バーの **[デプロイ]** をクリックして、リンクされたサービスをデプロイします。

### パイプラインの作成およびスケジュール設定
   
1. コマンド バーの **[新しいパイプライン]** をクリックします。コマンドが表示されない場合は、**[... (省略記号)** をクリックすると表示されます。 
2. 右側のウィンドウの JSON を、次の JSON スクリプトに置き換えます。**HDInsightLinkedService** リンク サービスの作成手順を実行した場合、独自のクラスターを使用するには、次の JSON で **HDInsightOnDemandLinkedService** を **HDInsightLinkedService** に置き換えます。 


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
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"defines":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]**StartDateTime** の値を現在の 3 日前の日付に置き換え、**EndDateTime** の値を現在の日付に置き換えます。StartDateTime と EndDateTime は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。出力テーブルは毎日生成されるようスケジュール設定されているので、3 つのスライスが生成されることになります。
	> 
	> JSON で**ストレージ アカウント**を実際のストレージ アカウント名に置き換えてください。
	
	JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](http://go.microsoft.com/fwlink/?LinkId=516971)を参照してください。
2. コマンド バーの **[デプロイ]** をクリックして、パイプラインをデプロイします。
4. 記事「[Data Factory を使ってみる][adfgetstarted]」の「[データセットとパイプラインを監視する][adfgetstartedmonitoring]」セクションを参照してください。 

	> [AZURE.NOTE]出力テーブルのスライスの **[アクティビティの実行の詳細]** ブレード (出力テーブル、スライス、ポータルで実行するアクティビティの順に選択) に、HDInsight クラスターにより作成されたログへのリンクが表示されます。これらのログは、ポータルで直接確認することもできますが、コンピューターにダウンロードすることもできます。
  

## Pig JSON の使用例
パイプライン JSON 内で Pig または Hive アクティビティを定義する際は、**type** プロパティを **HDInsightActivity** に設定する必要があります。

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
			"defines":
			{	
				"param1": "param1Value"
 			}
		}
	}

**以下の点に注意してください。**
	
- アクティビティの **type** が **HDInsightActivity** に設定されています。
- **linkedServiceName** が **MyHDInsightLinkedService** に設定されています。HDInsight のリンクされたサービスを作成する方法の詳細については、以下の HDInsight のリンクされたサービスのセクションを参照してください。
- **transformation** の **type** が **Pig** に設定されています。
- **script** プロパティに対して Pig スクリプトをインラインで指定するか、または Azure BLOB ストレージ内にスクリプト ファイルを格納し、**scriptPath** プロパティを使用してそのファイルを参照することができますが、詳細については、この記事の後半で説明します。 
- **defines** を使用して Pig スクリプトのパラメーターを指定します。詳細については、この記事の後半で説明します。 


## Hive JSON の使用例


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
			"defines":
			{	
				"param1": "param1Value"
            }
		}
	}

**以下の点に注意してください。**
	
- アクティビティの **type** が **HDInsightActivity** に設定されています。
- **linkedServiceName** が **MyHDInsightLinkedService** に設定されています。 
- **transformation** の **type** が **Hive** に設定されています。
- **script** プロパティに対して Hive スクリプトをインラインで指定するか、または Azure BLOB ストレージ内にスクリプト ファイルを格納し、**scriptPath** プロパティを使用してそのファイルを参照することができますが、詳細については、この記事の後半で説明します。 
- **defines** を使用して Hive スクリプトのパラメーターを指定します。詳細については、この記事の後半で説明します。 

> [AZURE.NOTE]コマンドレット、JSON スキーマ、スキーマ内のプロパティの詳細については、[開発者用リファレンス](http://go.microsoft.com/fwlink/?LinkId=516908)を参照してください。


## HDInsight アクティビティでの Pig スクリプトと Hive スクリプトの使用
HDInsight クラスターに関連付けられている Azure BLOB ストレージに Pig/Hive スクリプトを格納すれば、以下に示す JSON 内のプロパティを使用して Pig/Hive アクティビティからそれらのスクリプトを参照できます。

* **scriptPath** – Pig または Hive スクリプト ファイルへのパス
* **scriptLinkedService** – スクリプト ファイルを含む Azure Storage アカウント

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
						"defines":
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


> [AZURE.NOTE]**Tez** エンジンを使用して Hive クエリを実行するには、Hive クエリを実行する前に "\*\*set hive.execution.engine=tez\*\*;" を実行します。
> 
> コマンドレット、JSON スキーマ、スキーマ内のプロパティの詳細については、[開発者用リファレンス](http://go.microsoft.com/fwlink/?LinkId=516908)を参照してください。

## パラメーター化された Pig および Hive クエリ
Data Factory の Pig アクティビティと Hive アクティビティでは、**defines** を使用することで、Pig スクリプトと Hive スクリプト内で使われるパラメーターの値を指定できます。defines セクションは、パラメーター名とパラメーター値で構成されています。

**defines** を使用して Hive スクリプトのパラメーターを指定する方法については、以下の例を参照してください。パラメーター化された Hive スクリプトを使用するには、次の手順に従います。

1.	**defines** でパラメーターを定義します。
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
							"defines":
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


## 関連項目

記事 | 説明
------ | ---------------
[チュートリアル: Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory の開発者向けリファレンス][developer-reference] | この開発者用リファレンスは、コマンドレット、JSON スクリプト、関数などの包括的なリファレンス コンテンツです。 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=July15_HO5-->