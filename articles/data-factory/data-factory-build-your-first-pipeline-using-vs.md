<properties
	pageTitle="Visual Studio を使用した初めての Azure Data Factory パイプラインの作成"
	description="このチュートリアルでは、Visual Studio を使用して、サンプルの Azure Data Factory パイプラインを作成します。"
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
	ms.date="10/15/2015"
	ms.author="spelluru"/>

# Visual Studio を使用した初めての Azure Data Factory パイプラインの作成
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


この記事では、Visual Studio を使用して最初のパイプラインを作成する方法を学習します。このチュートリアルの手順は次のとおりです。

2.	リンクされたサービス (データ ストア、計算) を作成する。
3.	データセットを作成する。
3.	パイプラインを作成する。
4.	Data Factory を作成し、リンクされたサービス、データセット、パイプラインをデプロイする。 

この記事では、Azure Data Factory サービスの概念については説明しません。サービスの詳細については、記事「[Azure Data Factory サービスの概要](data-factory-introduction.md)」を参照してください。

> [AZURE.IMPORTANT]「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」という記事を参照し、前提条件の手順を完了してから、このチュートリアルを実行してください。

## チュートリアル: Visual Studio を使用して Data Factory のエンティティを作成してデプロイする 

### 前提条件

コンピューターに以下がインストールされている必要があります。

- Visual Studio 2013 または Visual Studio 2015
- Azure SDK for Visual Studio 2013 または Visual Studio 2015 をダウンロードします。[Azure ダウンロード ページ](http://azure.microsoft.com/downloads/)に移動し、**.NET** セクションの **[VS 2013]** または **[VS 2015]** をクリックします。
- Visual Studio 用の最新の Azuer Data Factory プラグイン ([VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) または [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)) をダウンロードします。Visual Studio 2013 を使用している場合は、メニューで **[ツール]**、**[拡張機能と更新プログラム]**、**[オンライン]**、**[Visual Studio ギャラリー]**、**[Microsoft Azure Data Factory Tools for Visual Studio]**、**[更新]** の順にクリックして、プラグインを更新することもできます。 
	
	

### Visual Studio プロジェクトの作成 
1. **Visual Studio 2013** または **Visual Studio 2015** を起動します。**[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。**[新しいプロジェクト]** ダイアログ ボックスが表示されます。  
2. **[新しいプロジェクト]** ダイアログで、**[DataFactory]** テンプレートを選択し、**[空の Data Factory プロジェクト]** をクリックします。   

	![New Project dialog box](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. プロジェクトの**名前**、**場所**、**ソリューション**の名前を入力し、**[OK]** をクリックします。

	![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### リンクされたサービスの作成
この手順では、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクした後、Hive 処理からの出力データを表すデータセットを作成します。


#### Azure Storage のリンクされたサービスを作成する


4. ソリューション エクスプローラーの **[リンクされたサービス]** を右クリックして、**[追加]** をポイントし、**[新しい項目]** をクリックします。      
5. **[新しい項目の追加]** ダイアログ ボックスで、一覧から **[Azure Storage のリンクされたサービス]** を選択し、**[追加]** をクリックします。 

	![新規のリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. **accountname** と **accountkey** を Azure ストレージ アカウントの名前とそのキーで置き換えます。

	![Azure Storage のリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. **AzureStorageLinkedService1.json** ファイルを保存します。

#### Azure HDInsight のリンクされたサービスを作成する
次に、Hive スクリプトの実行に使用するオンデマンド HDInsight クラスター用のリンクされたサービスを作成します。

1. **ソリューション エクスプローラー**の **[リンクされたサービス]** を右クリックして、**[追加]** をポイントし、**[新しい項目]** をクリックします。
2. **[HDInsight のオンデマンドのリンクされたサービス]** を選択し、**[追加]** をクリックします。 
3. **JSON** を次のように置き換えます。

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
		    }
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

1. **ソリューション エクスプローラー**で、右クリックして **[追加]** をポイントし、**[新しい項目]** をクリックします。 
2. 一覧から **[Azure BLOB]** を選択し、**[追加]** をクリックします。 
3. エディターの **JSON** を次のスニペットに置き換えます。この JSON スニペットでは、**AzureBlobOutput** というデータセットを作成し、Hive スクリプトによって生成されるデータの構造を指定します。さらに、**data** という BLOB コンテナーと **partitioneddata** というフォルダーに結果を保存することを指定します。**availability** セクションでは、出力データセットが 1 か月ごとに生成されることを指定します。
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
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

1. **ソリューション エクスプローラー**で、**[パイプライン]** を右クリックして **[追加]** をポイントし、**[新しい項目]** をクリックします。 
2. 一覧から **[Hive 変換パイプライン]** を選択し、**[追加]** をクリックします。 
3. **JSON** を次のスニペットに置き換えます。

	> [AZURE.IMPORTANT]**storageaccountname** をストレージ アカウントの名前に置き換えます。

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "AzureStorageLinkedService1",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
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
	
	Hive スクリプト ファイル **partitionweblogs.hql** は、Azure ストレージ アカウント (scriptLinkedService によって指定された、**AzureStorageLinkedService1** という名前のアカウント) と **script** という名前のコンテナーに保存されます。

	**extendedProperties** セクションは、Hive 構成値 (例: ${hiveconf:PartitionedData}) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

	パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

	アクティビティ JSON では、リンクされたサービス (**HDInsightOnDemandLinkedService**) によって指定されたコンピューティングで Hive スクリプトが実行されることを指定します。
3. **HiveActivity1.json** ファイルを保存します。

### 依存関係として partitionweblogs.hql を追加する 

1. **[ソリューション エクスプローラー]** ウィンドウで **[依存関係]** を右クリックし、**[追加]** をポイントして **[既存の項目]** をクリックします。  
2. **C:\\ADFGettingStarted** に移動し、**partitionweblogs.hql** ファイルを選択して **[追加]** をクリックします。 

次の手順でソリューションを公開すると、BLOB ストレージのスクリプト コンテナーに HQL ファイルがアップロードされます。

### Data Factory エンティティの発行/デプロイ

18. ソリューション エクスプローラーでプロジェクトを右クリックし、**[発行]** をクリックします。 
19. **[Microsoft アカウントの新規登録]** ダイアログ ボックスが表示されたら、Azure サブスクリプションを所有するアカウントの資格情報を入力し、**[サインイン]** をクリックします。
20. 次のダイアログ ボックスが表示されます。

	![[発行] ダイアログ ボックス](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Data Factory の構成ページで、次の操作を行います。
	1. **[Data Factory の新規作成]** オプションを選択します。
	2. **[名前]** に「**FirstPipelineUsingVS**」と入力します。 
	
		> [AZURE.IMPORTANT]Azure Data Factory の名前はグローバルに一意にする必要があります。発行時に "**"FirstPipelineUsingVS" という名前のデータ ファクトリは使用できません**" というエラーが発生した場合は、名前を変更します (yournameFirstPipelineUsingVS など)。Data Factory アーティファクトの名前付け規則については、[Data Factory - 名前付け規則](data-factory-naming-rules.md)に関するトピックを参照してください。
		> 
		> データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
	3. **[サブスクリプション]** フィールドで適切なサブスクリプションを選択します。 
	4. 作成するデータ ファクトリの**ソース グループ**を選択します。 
	5. データ ファクトリの**リージョン**を選択します。 
	6. **[次へ]** をクリックし、**[項目の発行]** ページに切り替えます。(**[次へ]** ボタンが無効になっている場合は、**Tab** キーを押して [名前] フィールドの外に移動します)。 
23. **[項目の発行]** ページで、すべての Data Factory エンティティが選択されていることを確認し、**[次へ]** をクリックして **[概要]** ページに切り替えます。     
24. 概要を確認し、**[次へ]** をクリックし、デプロイメント プロセスを開始し、**[デプロイ ステータス]** を表示します。
25. **[デプロイ ステータス]** ページに、デプロイメント プロセスのステータスが表示されます。デプロイメントが完了したら、[完了] をクリックします。 
 

## サーバー エクスプローラーを使用して Data Factory のエンティティを確認する

1. **Visual Studio** のメニューで **[ビュー]** をクリックし、**[サーバー エクスプローラー]** をクリックします。
2. [サーバー エクスプローラー] ウィンドウで、**[Azure]** を展開し、**[Data Factory]** を展開します。**[Visual Studio にサインイン]** が表示されたら、Azure サブスクリプションに関連付けられている**アカウント**を入力して **[続行]** をクリックします。**パスワード**を入力し、**[サインイン]** をクリックします。Visual Studio は、サブスクリプション内のすべての Azure データ ファクトリに関する情報を取得しようとします。**[Data Factory タスク リスト]** ウィンドウで、この操作のステータスを確認します。

	![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. データ ファクトリを右クリックし、**[Data Factory を新しいプロジェクトにエクスポートする]** を選択して、既存のデータ ファクトリに基づいて Visual Studio プロジェクトを作成します。

	![データ ファクトリのエクスポート](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Visual Studio の Data Factory ツールを更新する

Visual Studio の Azure Data Factory ツールを更新するには、次のように行います。

1. メニューで **[ツール]** をクリックし、**[拡張機能と更新プログラム]** を選択します。
2. 左ウィンドウで **[更新]** を選択し、**[Visual Studio ギャラリー]** を選択します。
3. **[Visual Studio の Azure Data Factory ツール]** を選択して、**[更新]** をクリックします。このエントリが表示されない場合は、ツールは既に最新バージョンです。 

Azure プレビュー ポータルを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については、「[データセットとパイプラインを監視する](data-factory-monitor-manage-pipelines.md)」を参照してください。
 

## 次のステップ
この記事では、オンデマンド HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。コピー アクティビティを使用して Azure BLOB から Azure SQL にデータをコピーする方法については、「[チュートリアル: Azure BLOB から Azure SQL にデータをコピーする](data-factory-get-started.md)」を参照してください。
  
## フィードバックの送信
この記事に関するフィードバックをお待ちしています。少しのお時間をとって、[電子メール](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-vs.md)でフィードバックをお寄せください。

<!---HONumber=Oct15_HO4-->