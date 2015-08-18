<properties
	pageTitle="Azure Data Factory を使用した初めてのパイプラインの作成"
	description="このチュートリアルでは、Azure HDInsight を使用してデータを変換するサンプル データ パイプラインを、Visual Studio を使用して作成する方法を示します。"
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
	ms.topic="get-started-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Azure Data Factory を使用した初めてのパイプラインの作成
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


この記事では、Visual Studio を使用して最初のパイプラインを作成する方法を学習します。このチュートリアルの手順は次のとおりです。

1.	データ ファクトリを作成する
2.	リンクされたサービス (データ ストア、計算) とデータセットを作成する
3.	パイプラインを作成する

この記事では、Azure Data Factory サービスの概念については説明しません。サービスの詳細については、記事「[Azure Data Factory サービスの概要](data-factory-introduction.md)」を参照してください。

## 手順 1: データ ファクトリを作成する

1.	[Azure プレビュー ポータル](http://portal.azure.com/)にログインした後、次の操作を行います。
	1.	左下隅の **[新規]** をクリックします。 
	2.	**[作成]** ブレードの **[データ分析]** をクリックします。
	3.	**[データ分析]** ブレードの **[Data Factory]** をクリックします。

		![[作成] ブレード](./media/data-factory-build-your-first-pipeline-using-vs/create-blade.png)

2.	**[新しいデータ ファクトリ]** ブレードで、[名前] フィールドに「**DataFactoryMyFirstPipeline**」と入力します。

	![New data factory blade](./media/data-factory-build-your-first-pipeline-using-vs/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] Azure Data Factory の名前はグローバルで一意となります。ファクトリを作成するには、データ ファクトリの名前の先頭にあなたの名前を付ける必要があります。 
3.	リソース グループを作成していない場合は、リソース グループを作成する必要があります。これを行うには、次の手順を実行します。
	1.	**[リソース グループ名]** をクリックします。
	2.	**[リソース グループ]** ブレードで、**[新規リソース グループの作成]** を選択します。
	3.	**[リソース グループの作成]** ブレードで、**[名前]** フィールドに「**ADF**」と入力します。
	4.	**[OK]** をクリックします。
	
		![Create resource group](./media/data-factory-build-your-first-pipeline-using-vs/create-resource-group.png)
4.	リソース グループを選択した後、データ ファクトリを作成する正しいサブスクリプションを使用していることを確認します。
5.	**[新しいデータ ファクトリ]** ブレードで **[作成]** をクリックします。
6.	次のように、Azure プレビュー ポータルの**スタート画面**にデータ ファクトリを作成中であることが示されます。   

	![データ ファクトリを作成中の状態](./media/data-factory-build-your-first-pipeline-using-vs/creating-data-factory-image.png)
7. ご利用ありがとうございます。 これで、最初のデータ ファクトリが正常に作成されました。データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。 	

	![Data Factory blade](./media/data-factory-build-your-first-pipeline-using-vs/data-factory-blade.png)

以降の手順では、このチュートリアルで使用するリンクされたサービス、データセット、およびパイプラインを作成する方法を説明します。

## チュートリアル: Visual Studio を使用して Data Factory のエンティティを作成してデプロイする 

### 前提条件

コンピューターに:
- Visual Studio 2013
- がインストールされており、Azure SDK for Visual Studio 2013 がダウンロードされている必要があります。[Azure ダウンロード ページ](http://azure.microsoft.com/downloads/) に移動して、**.NET** セクションの**[VS 2013 install]** をクリックします。


### Visual Studio プロジェクトの作成 
1. **Visual Studio 2013** を起動します。**[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。**[新しいプロジェクト]** ダイアログ ボックスが表示されます。  
2. **[新しいプロジェクト]** ダイアログ ボックスで、**[DataFactory]** テンプレートを選択し、**[Empty Data Factory Project]** をクリックします。DataFactory テンプレートが表示されない場合は、Visual Studio を終了し、Azure SDK for Visual Studio 2013 をインストールし、Visual Studio を再度開きます。  

	![New Project dialog box](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. プロジェクトの**名前**、**場所**、**ソリューション**の名前を入力して、**[OK]** をクリックします。

	![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### リンクされたサービスの作成
この手順では、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクした後、Hive 処理からの出力データを表すデータセットを作成します。


#### Azure ストレージのリンクされたサービスを作成する


4. ソリューション エクスプローラーの **[Linked Services]** を右クリックして、**[追加]** をポイントし、**[新しい項目]** をクリックします。      
5. **[新しい項目の追加]** ダイアログ ボックスで、一覧から **[Azure Storage Linked Service]** を選択し、**[追加]** をクリックします。 

	![新規のリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. **accountname** と **accountkey** を Azure ストレージ アカウントとそのキーで置き換えます。

	![Azure ストレージのリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. **AzureStorageLinkedService1.json** ファイルを保存します。

#### Azure HDInsight のリンクされたサービスを作成する
次に、Hive スクリプトの実行に使用するオンデマンド HDInsight クラスター用のリンクされたサービスを作成します。

1. **ソリューション エクスプローラー**の **[Linked Services]** を右クリックして、**[追加]** をポイントし、**[新しい項目]** をクリックします。
2. **[HDInsight On Demand Linked Service]** を選択し、**[追加]** をクリックします。 
3. **JSON** を次のように置き換えます。

		{
		    "name": "HDInsightOnDemandLinkedService",
		    "properties": {
		        "version": "3.1",
		        "clusterSize": 1,
		        "timeToLive": "00:05:00",
		        "jobsContainer": "adfjobs",
		        "linkedServiceName": "StorageLinkedService",
		        "type": "HDInsightOnDemandLinkedService"
		    }
		}
	
	次の表に、このスニペットで使用される JSON プロパティの説明を示します。
	
	プロパティ | 説明
	-------- | -----------
	バージョン | バージョン 3.1 の HDInsight を作成することを指定します。 
	ClusterSize | 1 ノードの HDInsight クラスターを作成します。 
	TimeToLive | 削除されるまでの HDInsight クラスターのアイドル時間を指定します。
	JobsContainer | HDInsight によって生成されるログを保存するために作成されるジョブ コンテナーの名前を指定します。
	linkedServiceName | HDInsight によって生成されるログを保存するために使用されるストレージ アカウントを指定します。

4. **HDInsightOnDemandLinkedService1.json** ファイルを保存します。
 
### 出力データセットを作成する
次に、Azure BLOB ストレージに格納されるデータを表す出力データセットを作成します。

1. **ソリューション エクスプローラー**を右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。 
2. 一覧から **[Azure BLOB]** を選択し、**[追加]** をクリックします。 
3. エディターに表示されている **JSON** を次のスニペットに置き換えます。この JSON スニペットでは、**AzureBlobOutput** という名前のデータセットを作成し、Hive スクリプトによって生成されるデータの構造を指定します。さらに、結果を **data** という名前の BLOB コンテナーおよび **partitioneddata** という名前のフォルダーに格納することを指定します。**availability** セクションでは、出力データセットが 1 か月ごとに生成されることを指定します。
	
		{
		    "name": "AzureBlobOutput",
		    "properties": {
		        "location": {
		            "type": "AzureBlobLocation",
		            "folderPath": "data/partitioneddata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            },
		            "linkedServiceName": "StorageLinkedService"
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        }
		    }
		}

4. **AzureBlobLocation1.json** ファイルを保存します。


### 最初のパイプラインを作成する
この手順では、最初のパイプラインを作成します。

1. **ソリューション エクスプローラー** の **[パイプライン]** を右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。 
2. 一覧から **[Hive 変換パイプライン]** を選択し、**[追加]** をクリックします。 
3. **JSON** を次のスニペットに置き換え、**storageaccountname** をストレージ アカウントの名前に置き換えます。

		{
			"name": "MyFirstPipeline",
			"properties": {
			"description": "My first Azure Data Factory pipeline",
		 	"activities": [
		      {
		            "type": "HDInsightActivity",
		            "transformation": {
		                    "scriptPath": "script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "type": "Hive",
		                    "extendedProperties": {
		                        "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "outputs": [   {  "name": "AzureBlobOutput"    }   ],
		                "policy": {  
		                    "concurrency": 1,
		                    "retry": 3
						},
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-01-01",
		        "end": "2014-01-02"
		    }
		}
 
	この JSON スニペットでは、Hive を使用して HDInsight クラスターのデータを処理する 1 つのアクティビティで構成されるパイプラインを作成します。
	
	Hive スクリプト ファイル **partitionweblogs.hql** は、Azure ストレージ アカウント (scriptLinkedService によって指定され、**StorageLinkedService** という名前) および **script** という名前のコンテナーに格納されます。

	**extendedProperties** セクションは、Hive 構成値 (例: ${hiveconf:PartitionedData}) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

	パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

	アクティビティ JSON では、Hive スクリプトがリンクされたサービス **HDInsightOnDemandLinkedService** によって指定されたコンピューティングで実行することを指定します。
3. **HiveActivity1.json** ファイルを保存します。 

### Data Factory エンティティの発行/デプロイ
  
1. 有効になっていない場合は、ツールバー領域で、**[Data Factory]** を右クリックして Data Factory ツールバーを有効にします。 
19. **[Data Factory toolbar]** で、**ドロップダウン ボックス**をクリックし、Azure サブスクリプション内に、すべてのデータ ファクトリを表示します。**[Visual Studio にサインイン]** ダイアログ ボックスが表示された場合。 
	20. データ ファクトリを作成する Azure サブスクリプションに関連付けられている**電子メール アカウント**を入力し、**パスワード**を入力し、**[サインイン]** をクリックします。
	21. サイン インが成功すると、Azure サブスクリプション内のすべてのデータ ファクトリが表示されます。このチュートリアルでは、新しいデータ ファクトリを作成します。       
22. ドロップダウン リストの **[DataFactoryMyFirstPipeline]** を選択し、**[発行]** ボタンをクリックして、リンクされたサービス、データセット、パイプラインをデプロイ/発行します。    

	![[発行] ボタン](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

23. 上の図に示されている **[Data Factory Task List]** ウィンドウで発行のステータスを確認する必要があります。発行が成功したことを確認します。


## サーバー エクスプローラーを使用して Data Factory のエンティティを確認する

1. **Visual Studio** のメニューで **[ビュー]** をクリックし、**[サーバー エクスプローラー]** をクリックします。
2. [サーバー エクスプローラー] ウィンドウで、**[Azure]** を展開し、**[Data Factory]** を展開します。**[Visual Studio にサインイン]** が表示されたら、Azure サブスクリプションに関連付けられている**アカウント**を入力して **[続行]** をクリックします。**パスワード**を入力し、**[サインイン]** をクリックします。Visual Studio は、サブスクリプション内のすべての Azure データ ファクトリに関する情報を取得しようとします。**[Data Factory Task List]** ウィンドウで、この操作のステータスが確認できます。

	![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. [Data Factory] を右クリックし、**[Export Data Factory to New Project]** を選択して、既存のデータ ファクトリに基づいて Visual Studio プロジェクトを作成します。

	![データ ファクトリのエクスポート](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Visual Studio の Data Factory ツールを更新する

Visual Studio の Azure Data Factory ツールを更新するには、次のように行います。

1. メニューで**[ツール]** をクリックし、**[拡張機能と更新プログラム]** を選択します。
2. 左ウィンドウで **[更新]** を選択し、**[Visual Studio ギャラリー]** を選択します。
3. **[Visual Studio の Azure Data Factory ツール]** を選択して、**[更新]** をクリックします。このエントリが表示されない場合は、ツールは既に最新バージョンです。 

Azure プレビュー ポータルを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については「[データセットとパイプラインを監視する](data-factory-monitor-manage-pipelines.md)」をご覧ください。
 

## 次のステップ
この記事では、オンデマンド HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。コピー アクティビティを使用して Azure BLOB から Azure SQL にデータをコピーする方法については、「[チュートリアル: Azure BLOB から Azure SQL にデータをコピーする](data-factory-get-started.md)」をご覧ください。
  

<!-----HONumber=August15_HO6-->