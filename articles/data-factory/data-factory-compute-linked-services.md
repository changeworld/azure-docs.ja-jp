<properties 
	pageTitle="コンピューティングのリンクされたサービス | Microsoft Azure" 
	description="Azure Data Factory パイプラインでデータの変換/処理に使用できるコンピューティング環境について学習します。" 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# コンピューティングのリンクされたサービス

この記事では、データの処理または変換に利用できるさまざまなコンピューティング環境について説明します。これらのコンピューティング環境を Azure Data Factory にリンクする「リンクされたサービス」の構成時に Data Factory でサポートされるさまざまな構成 (オンデマンドと Bring Your Own の比較) に関する詳細も提供します。

## オンデマンドのコンピューティング環境

この種類の構成では、コンピューティング環境は Azure Data Factory サービスにより完全管理されます。データを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。オンデマンドのコンピューティング環境のために「リンクされたサービス」を作成し、構成し、ジョブ実行、クラスター管理、ブートストラップ アクションの詳細設定を制御できます。

> [AZURE.NOTE]オンデマンド構成は現在のところ、Azure HDInsight クラスターにのみ対応しています。

## Azure HDInsight のオンデマンドのリンクされたサービス

オンデマンド HDInsight クラスターはデータを処理するために Azure Data Factory サービスによって自動的に作成されます。このクラスターはクラスターに関連付けられているストレージ アカウント (JSON の linkedServiceName プロパティ) と同じリージョンで作成されます。

オンデマンド HDInsight のリンクされたサービスに関する次の**重要な**点に注意してください。

- 作成したオンデマンド HDInsight クラスターは Azure サブスクリプションに表示されません。Azure Data Factory サービスがあなたの代わりにオンデマンド HDInsight クラスターを管理します。
- オンデマンド HDInsight クラスターで実行されるジョブのログは HDInsight クラスターに関連付けられているストレージ アカウントにコピーされます。これらのログには Azure ポータルの **[アクティビティ実行の詳細]** ブレードからアクセスできます。詳細については、「[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」という記事を参照してください。 
- HDInsight クラスターが稼動し、ジョブを実行している時間に対してのみ課金されます。

> [AZURE.IMPORTANT]オンデマンドで Azure HDInsight クラスターをプロビジョニングするには一般的に **15 分**以上かかります。

### 例

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 4,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "00:05:00",
	      "version": "3.1",
		  "osType": "linux",
	      "linkedServiceName": "MyBlobStore"
	      "additionalLinkedServiceNames": [
	        "otherLinkedServiceName1",
	        "otherLinkedServiceName2"
	      ]
	    }
	  }
	}

### プロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティは **HDInsightOnDemand** に設定されます。 | あり
clusterSize | オンデマンド クラスターのサイズです。このオンデマンド クラスターに配置するノードの数を指定します。 | あり 
jobscontainer | pig/hive/package ジョブにより使用されるデータを保存し、クラスター ログが保管される BLOB コンテナーです。 | あり
timetolive | <p>オンデマンド HDInsight クラスターに許可されるアイドル時間です。他のアクティブなジョブがクラスターにない場合、アクティビティ実行の完了後にオンデマンド HDInsight クラスターが起動状態を維持する時間を指定します。</p><p>たとえば、アクティビティ実行に 6 分かかるときに timetolive が 5 分に設定されている場合、アクティビティ実行の 6 分間の処理の後、クラスターが起動状態を 5 分間維持します。別のアクティビティ実行が 6 分の時間枠で実行される場合、それは同じクラスターで処理されます。</p><p>オンデマンド HDInsight クラスターの作成は高額な作業であり (時間もかかることがあります)、オンデマンド HDInsight クラスターを再利用し、Data Factory のパフォーマンスを改善する必要がある場合にこの設定を利用します。</p><p>timetolive 値を 0 に設定した場合、アクティビティ実行の処理直後にクラスターが削除されます。その一方で、高い値を設定した場合、クラスターは不必要にアイドル状態を維持し、コストの上昇を招きます。そのため、ニーズに合わせて適切な値を設定することが重要です。</p><p>timetolive プロパティ値が適切に設定されている場合、複数のパイプラインでオンデマンド HDInsight クラスターの同じインスタンスを共有できます。</p> | はい
version | HDInsight クラスターのバージョン | いいえ
linkedServiceName | データの保存し、処理するためのオンデマンド クラスターで使用される BLOB ストアです。 | あり
additionalLinkedServiceNames | Data Factory サービスがあなたの代わりに登録できるように、HDInsight の「リンクされたサービス」の追加ストレージ アカウントを指定します。 | いいえ
osType | オペレーティング システムの種類使用可能な値: windows (既定値) および linux | いいえ 

### 高度なプロパティ

次のプロパティを指定し、オンデマンド HDInsight クラスターを詳細に設定することもできます。

プロパティ | 説明 | 必須
-------- | ----------- | --------
coreConfiguration | 作成する HDInsight クラスターに core 構成パラメーター (core-site.xml と同じ) を指定します。 | いいえ
hBaseConfiguration | HDInsight クラスターに HBase 構成パラメーター (hbase-site.xml) を指定します。 | いいえ
hdfsConfiguration | HDInsight クラスターに HDFS 構成パラメーター (hdfs-site.xml) を指定します。 | いいえ
hiveConfiguration | HDInsight クラスターに hive 構成パラメーター (hive-site.xml) を指定します。 | いいえ
mapReduceConfiguration | HDInsight クラスターに MapReduce 構成パラメーター (mapred-site.xml) を指定します。 | いいえ
oozieConfiguration | HDInsight クラスターに Oozie 構成パラメーター (oozie-site.xml) を指定します。 | いいえ
stormConfiguration | HDInsight クラスターに Storm 構成パラメーター (storm-site.xml) を指定します。 | いいえ
yarnConfiguration | HDInsight クラスターに Yarn 構成パラメーター (yarn-site.xml) を指定します。 | いいえ

#### 例 – オンデマンド HDInsight クラスターと詳細なプロパティ

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "01:30:00",
	      "version": "3.1",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx8000m",
	        "mapreduce.map.java.opts": "-Xmx8000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

## Bring Your Own のコンピューティング環境 

この種類の構成では、既存のコンピューティング環境を Data Factory の「リンクされたサービス」として登録できます。このコンピューティング環境はユーザーにより管理され、Data Factory サービスをこの環境を利用し、アクティビティを実行します。
 
この種類の構成は次のコンピューティング環境でサポートされています。

- Azure HDInsight
- Azure Batch 
- Azure Machine Learning

## Azure HDInsight のリンクされたサービス

Azure HDInsight の「リンクされたサービス」を作成し、独自の HDInsight クラスターを Data Factory に登録できます。

### 例

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "location": "WestUS",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### プロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティは **HDInsight** に設定されます。 | あり
clusterUri | HDInsight クラスターの URI です。 | あり
username | 既存の HDInsight クラスターに接続するために使用されるユーザーの名前を指定します。 | はい
パスワード | ユーザー アカウントのパスワードを指定します。 | あり
location | HDInsight クラスターの場所を指定します (例: WestUS)。 | あり
linkedServiceName | HDInsight クラスターで使用される BLOB ストレージの「リンクされたサービス」の名前です。 | あり

## Azure Batch の「リンクされたサービス」

Azure Batch の「リンクされたサービス」を作成し、仮想マシン (VM) の Batch プールを Data Factory に登録できます。Azure Batch と Azure HDInsight のいずれかを利用し、.NET カスタム アクティビティを実行できます。

Azure Batch サービスの利用が初めての場合、次のトピックを参照してください。
 

- Azure Batch サービスの概要については「[Azure Batch の技術概要](../batch/batch-technical-overview.md)」
- Azure Batch アカウントを作成するための [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) コマンドレット (または) Azure 管理ポータルを利用して Azure Batch アカウントを作成するための [Azure 管理ポータル](../batch/batch-technical-overview.md)コマンドレットの使用方法の詳細については、「[PowerShell を使用した Azure Batch アカウントの管理](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)」トピックをご覧ください。
- Azure Batch プールを作成するための [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) コマンドレット。

### 例

	{
	  "name": "AzureBatchLinkedService",
	  "properties": {
	    "type": "AzureBatch",
	    "typeProperties": {
	      "accountName": "<Azure Batch account name>",
	      "accessKey": "<Azure Batch account key>",
	      "poolName": "<Azure Batch pool name>",
	      "linkedServiceName": "<Specify associated storage linked service reference here>"
	    }
	  }
	}

**accountName** プロパティのバッチ アカウントの名前に「**.<リージョン名**」を追加します。例:
	
			"accountName": "mybatchaccount.eastus" 

もう 1 つの選択肢は下のように batchUri エンドポイントを指定することです。

			accountName: "adfteam",
			batchUri: "https://eastus.batch.azure.com",

### プロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティは **AzureBatch** に設定されます。 | あり
accountName | Azure Batch アカウントの名前です。 | あり
accessKey | Azure Batch アカウントのアクセス キーです。 | あり
poolName | 仮想マシンのプールの名前です。 | あり
linkedServiceName | この Azure Batch の「リンクされたサービス」に関連付けられている Azure ストレージの「リンクされたサービス」の名前です。この「リンクされたサービス」はアクティビティの実行に必要なファイルのステージングとアクティビティ実行ログの保存に利用されます。 | あり


## Azure Machine Learning のリンクされたサービス

Azure 機械学習の「リンクされたサービス」を作成し、機械学習のバッチ スコアリング エンドポイントを Data Factory に登録します。

### 例

	{
	  "name": "AzureMLLinkedService",
	  "properties": {
	    "type": "AzureML",
	    "typeProperties": {
	      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
	      "apiKey": "<apikey>"
	    }
	  }
	}

### プロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
型 | type プロパティは **AzureML** に設定されます。 | あり
mlEndpoint | バッチ スコアリング URL です。 | あり
apiKey | 公開されたワークスペース モデルの API です。 | あり


## Azure Data Lake Analytics リンク サービス
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory にリンクしてから、パイプラインで [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)を使用します。

次の例は、Azure Data Lake Analytics リンク サービスの JSON 定義です。

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


次の表は、JSON 定義で使用されるプロパティの説明です。

プロパティ | 説明 | 必須
-------- | ----------- | --------
型 | type プロパティを **AzureDataLakeAnalytics** に設定する必要があります。 | あり
accountName | Azure Data Lake Analytics アカウント名。 | あり
dataLakeAnalyticsUri | Azure Data Lake Analytics URI。 | いいえ 
authorization | Data Factory Editor で **[承認]** ボタンをクリックし、OAuth ログインを完了すると、承認コードが自動的に取得されます。 | あり 
subscriptionId | Azure サブスクリプション ID | いいえ (指定されていない場合は Data Factory のサブスクリプションが使用されます)。 
resourceGroupName | Azure リソース グループ名 | いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。
sessionId | OAuth 承認セッションのセッション ID です。各セッション ID は一意であり、使用されるのが 1 回のみの場合があります。セッション ID は、Data Factory Editor で自動生成されます。 | あり


## Azure SQL のリンクされたサービス

Azure SQL のリンクされたサービスを作成し、[ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)で使用して、Data Factory パイプラインからストアド プロシージャを起動します。このリンクされたサービスの詳細については、[Azure SQL コネクタ](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)に関する記事を参照してください。


  



     
 
   

<!---HONumber=Nov15_HO3-->