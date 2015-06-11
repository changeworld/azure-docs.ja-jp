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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Data Factory で Pig と Hive を使用する
Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。この記事では、Azure のデータのファクトリのパイプラインでの Pig と Hive の変換での HDInsight のアクティビティの使用について説明します。参照してください [データ ファクトリからの MapReduce プログラムの起動][data-factory-map-reduce] MapReduce を実行する方法の詳細について、HDInsight でプログラムが、Azure のデータの工場出荷時のパイプラインからクラスターします。

## チュートリアル: Azure のデータのファクトリでの Hive を使用します。
このチュートリアルでは、出荷時のデータ パイプラインでの Hive の変換で、HDInsight のアクティビティを使用するための手順を説明します。

### 前提条件
1. 」からチュートリアルを完了 [Azure のデータのファクトリを使い始める][adfgetstarted] 記事です。
2. アップロード **emp.txt** としては、上記のチュートリアルで作成したファイル **hiveinput\emp.txt** adftutorial コンテナー、blob ストレージにします。 **Hiveinput** フォルダーが自動的に作成、 **adftutorial** を次の構文の emp.txt ファイルをアップロードするときにコンテナーです。

	> [AZURE.NOTE]Emp.txt ファイルは、このチュートリアルでは、ダミーのファイルのみです。実際の入力データの送信元、 **hivesampletable** 、HDInsight クラスター上に既に存在します。パイプラインでは、emp.txt ファイルをまったく使用しません。
	
2. 作成 **hivequery.hql** という名前のサブフォルダー内のファイル **Hive** [ **C:\ADFGetStarted** を次の内容です。
    		
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

	> [AZURE.NOTE]使用する、 **Tez** HQL ファイルでの Hive クエリを実行するには追加のエンジン"* * hive.execution.engine=tez**; の設定」で、ファイルの上部にあります。
		
3.  アップロード、 **hivequery.hql** を **adftutorial** コンテナー、blob ストレージに


### チュートリアル

#### 入力テーブルの作成
1.  **データ ファクトリ** ブレードで、 **ADFTutorialDataFactory**, 、] をクリックして **作成者と展開** データ工場出荷時のエディターを起動します。
	
	![データの工場出荷時のブレード][data-factory-blade]

2.  **データ工場出荷時のエディター**, 、] をクリックして **新しいデータセット**, 、順にクリック **Azure Blob ストレージ** コマンド バーからです。
3. 右側のウィンドウでは、JSON スクリプトを次の JSON スクリプトに置き換えます。    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**次に注意してください。**
	
	- 場所 **型** に設定されている **AzureBlobLocation**です。
	- **linkedServiceName** に設定されている **StorageLinkedService** Azure のストレージ アカウントを定義します。
	- **folderPath** 、入力データ用の blob container\folder を指定します。 
	- **頻度日 =** と **間隔 = 1** 場合は、スライスを毎日使用
	- **waitOnExternal** このデータは、別のパイプラインでは生成されませんが、それではなくに生成される外部データのファクトリのことを意味します。 
	

	参照してください [データ工場出荷時の開発者向けリファレンス][developer-reference] JSON のプロパティの説明についてはします。

2. クリックして **展開** 、テーブルを展開するには、コマンド バーにします。
  
#### 出力テーブルの作成
        
1.  **データ工場出荷時のエディター**, 、] をクリックして **新しいデータセット**, 、順にクリック **Azure Blob ストレージ** コマンド バーからです。
2. 右側のウィンドウでは、JSON スクリプトを次の JSON スクリプトに置き換えます。

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

2. クリックして **展開** 、テーブルを展開するには、コマンド バーにします。


### HDInsight クラスター用のリンクされたサービスを作成する
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。試しに、オンデマンド クラスターを使用してみましょう。

#### オンデマンド HDInsight クラスターを使用するには
1. クリックして **新しいコンピューティング** クリックし、コマンド バーから **オンデマンドでの HDInsight クラスター** ] メニューからです。
2. JSON スクリプトでは、次のように 
	1.  **ClusterSize** プロパティでは、HDInsight クラスターのサイズを指定します。
	2.  **JobsContainer** プロパティでは、クラスターのログが格納される既定のコンテナーの名前を指定します。このチュートリアルでは、次のように指定します。 **adfjobscontainer**です。
	3.  **TimeToLive** プロパティ、どのくらいの期間、クラスターが削除される前にアイドル状態できるを指定します。 
	4.  **バージョン** プロパティを使用する場合、HDInsight のバージョンを指定します。このプロパティを除外する場合は、最新のバージョンが使用します。  
	5.  **LinkedServiceName**, 、指定 **StorageLinkedService** ことを Get で作成したチュートリアルを開始します。 

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

2. クリックして **展開** リンクされているサービスをデプロイするには、コマンド バー。
   
   
#### 独自の HDInsight クラスターを使用するには 

1. クリックして **新しいコンピューティング** クリックし、コマンド バーから **HDInsight クラスター** ] メニューからです。
2. JSON スクリプトでは、次のように 
	1.  **ClusterUri** プロパティで、HDInsight の URL を入力します。例: https://<clustername>.azurehdinsight.net/     
	2.  **UserName** プロパティ、HDInsight クラスターへのアクセス権を持つユーザーの名前を入力します。
	3.  **パスワード** プロパティでは、ユーザーのパスワードを入力します。 
	4.  **LinkedServiceName** プロパティ、入力 **StorageLinkedService**です。これは、入門チュートリアルで作成したリンクのサービスです。 

2. クリックして **展開** リンクされているサービスをデプロイするには、コマンド バー。

### パイプラインの作成およびスケジュール設定
   
1. クリックして **新しいパイプライン** コマンド バーでします。クリックして、コマンドが表示されない場合は、 **しています.(省略記号)** 表示されます。 
2. 次の JSON スクリプトを右側のウィンドウで、JSON を置き換えます。独自のクラスターを使用して、作成する手順の後にかどうか、 **HDInsightLinkedService** サービスでは、リンクされている置換 **HDInsightOnDemandLinkedService** で **HDInsightLinkedService** で、次の JSON です。 


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
						"linkedServiceName": "HDInsightLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
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

	> [AZURE.NOTE]置換 **StartDateTime** 値と現在の日付の前に 3 日間は、および **EndDateTime** 、現在の日付を持つ値です。StartDateTime と EndDateTime の両方がである必要があります [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)です。例: 2014年-10-14T16:32:41Z です。出力テーブルは毎日生成されるようスケジュール設定されているので、3 つのスライスが生成されることになります。
	
	> [AZURE.NOTE]置き換える **、ストレージ アカウント** 、ストレージ アカウントの名前で、JSON でします。
	
	参照してください [JSON スクリプト参照](http://go.microsoft.com/fwlink/?LinkId=516971) JSON のプロパティに関する詳細です。
2. クリックして **展開** 、パイプラインを展開するには、コマンド バーにします。
4. 参照してください [データセットおよびパイプラインを監視する][adfgetstartedmonitoring] 」の「 [データ ファクトリの][adfgetstarted] 記事です。 

	> [AZURE.NOTE] **アクティビティの実行の詳細** 、出力テーブルのスライスのブレード (出力テーブルを選択] を選択]-> [スライスには、ポータルで、アクティビティの実行を選択]-> [)、HDInsight クラスターが作成されたログへのリンクが表示されます。ポータル自体に表示し、コンピューターにダウンロードしたりすることが可能です。
  

## Pig JSON の使用例
JSON、パイプラインで、Pig や Hive のアクティビティを定義するときに、 **型** プロパティを設定する必要があります。 **HDInsightActivity**です。

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

**次に注意してください。**
	
- アクティビティ **型** に設定されている **HDInsightActivity**です。
- **linkedServiceName** に設定されている **MyHDInsightLinkedService**です。リンクされている HDInsight サービスを作成するのには、詳細については、以下のリンクの HDInsight サービスのセクションを参照してください。
-  **型** の **変換** に設定されている **Pig**です。
- Pig スクリプトのインラインを指定することができます、 **スクリプト** プロパティまたは store のスクリプト ファイルで、Azure blob ストレージとを使用して、ファイルを参照してください **scriptPath** プロパティで、この記事の後半で説明しています。 
- 使用して、Pig のスクリプトのパラメーターを指定する、 **extendedProperties**です。詳細については、この記事の後半で説明します。 


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
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**次に注意してください。**
	
- アクティビティ **型** に設定されている **HDInsightActivity**です。
- **linkedServiceName** に設定されている **MyHDInsightLinkedService**です。 
-  **型** の **変換** に設定されている **Hive**です。
- Hive スクリプトのインラインを指定することができます、 **スクリプト** プロパティまたは store のスクリプト ファイルで、Azure blob ストレージとを使用して、ファイルを参照してください **scriptPath** プロパティで、この記事の後半で説明しています。 
- 使用して、Hive スクリプトのパラメーターを指定する、 **extendedProperties**です。詳細については、この記事の後半で説明します。 

> [AZURE.NOTE]参照してください [開発者向けリファレンス](http://go.microsoft.com/fwlink/?LinkId=516908) の詳細については、コマンドレット、JSON スキーマ、およびスキーマのプロパティです。


## アクティビティの HDInsight で Pig や Hive スクリプトを使用してください。
HDInsight クラスターに関連付けられている Azure BLOB ストレージに Pig/Hive スクリプトを格納すれば、以下に示す JSON 内のプロパティを使用して Pig/Hive アクティビティからそれらのスクリプトを参照できます。

* **scriptPath** – Pig または Hive スクリプト ファイルへのパス
* **scriptLinkedService** – Azure のストレージ アカウントをスクリプト ファイルが含まれています。

サンプル パイプラインの次の JSON の例を参照する Hive アクティビティを使用して **transformdata.hql** ファイルに格納されている **スクリプト** フォルダーで、 **adfwalkthrough** によって表される、Azure blob ストレージ内のコンテナー、 **StorageLinkedService**です。

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
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
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


> [AZURE.NOTE]使用する、 **Tez** Hive クエリの実行、実行エンジン"* * hive.execution.engine=tez**; の設定"、Hive クエリを実行する前にします。
> 
> 参照してください [開発者向けリファレンス](http://go.microsoft.com/fwlink/?LinkId=516908) の詳細については、コマンドレット、JSON スキーマ、およびスキーマのプロパティです。

## パラメーター化された Pig および Hive クエリ
データ工場出荷時の Pig や Hive のアクティビティを使用して、Pig や Hive スクリプトで使用されるパラメーターの値を指定できます。 **extendedProperties**です。ExtendedProperties セクションは、パラメーター名とパラメーター値で構成されています。

使用して、Hive スクリプトのパラメーターを指定するための次の例を参照してください。 **extendedProperties**です。パラメーター化された Hive スクリプトを使用するには、次の手順に従います。

1.	パラメーターを定義する **extendedProperties**です。
2.	使用して、パラメーターを参照してください、インラインで Hive スクリプト (または) Hive スクリプト ファイルは、ブログの記憶域に格納されている、 **${hiveconf:parameterName}**です。

   
    		
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


## 関連項目

記事 | 説明
------ | ---------------
[チュートリアル: 移動し、データのファクトリを使用してログ ファイルの処理][adf-tutorial] | この記事では、実際のほぼを実装する方法について説明する、エンド ツー エンド チュートリアル insights にログ ファイルからデータを変換する Azure のデータのファクトリを使用して世界中のシナリオです。
[Azure のデータの工場出荷時の開発者向けリファレンス][developer-reference] | コマンドレット、JSON スクリプト、関数などの包括的な参照の内容を開発者向けリファレンスには. 

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

<!---HONumber=GIT-SubDir--> 