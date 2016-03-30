<properties 
	pageTitle="チュートリアル: コピー アクティビティがあるパイプラインを Data Factory Editor で作成する" 
	description="このチュートリアルでは、Azure クラシック ポータルの Data Factory エディターを使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。" 
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
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# チュートリアル: コピー アクティビティがあるパイプラインを Data Factory Editor で作成する
> [AZURE.SELECTOR]
- [チュートリアルの概要](data-factory-get-started.md)
- [Data Factory エディターの使用](data-factory-get-started-using-editor.md)
- [Visual Studio の使用](data-factory-get-started-using-vs.md)
- [PowerShell の使用](data-factory-monitor-manage-using-powershell.md)
- [コピー ウィザードの使用](data-factory-copy-data-wizard-tutorial.md)


このチュートリアルで実行する手順は次のとおりです。

手順 | 説明
-----| -----------
[Azure Data Factory を作成する](#create-data-factory) | この手順では、**ADFTutorialDataFactory** という名前の Azure Data Factory を作成します。  
[リンクされたサービスの作成](#create-linked-services) | この手順では、**AzureStorageLinkedService** と **AzureSqlLinkedService** の 2 つのリンクされたサービスを作成します。AzureStorageLinkedService は Azure Storage を、AzureSqlLinkedService は Azure SQL Database を、それぞれ ADFTutorialDataFactory にリンクします。パイプラインの入力データは、Azure BLOB ストレージの BLOB コンテナーにあります。また出力データは、Azure SQL Database のテーブルに格納されます。そのため、これら 2 つのデータ ストアをリンクされたサービスとしてデータ ファクトリに追加します。      
[入力データセットと出力データセットを作成する](#create-datasets) | 前の手順では、入力/出力データを含むデータ ストアを参照する、リンクされたサービスを作成しました。この手順では、**EmpTableFromBlob** と **EmpSQLTable** の 2 つの Data Factory テーブルを定義します。これらはデータ ストアに格納されている入力/出力データを表します。EmpTableFromBlob の場合、ソース データを持つ BLOB を含む BLOB コンテナーを指定します。EmpSQLTable の場合は、出力データを格納する SQL テーブルを指定します。また、データの構造や可用性など、他のプロパティも指定します。 
[パイプラインを作成する。](#create-pipeline) | この手順では、**ADFTutorialPipeline** という名前のパイプラインを ADFTutorialDataFactory に作成します。このパイプラインには、Azure BLOB から Azure SQL 出力テーブルに入力データをコピーする**コピー アクティビティ**があります。コピー アクティビティにより、Azure Data Factory ではデータ移動が実行されます。また、このアクティビティは、安全で信頼性が高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。コピー アクティビティの詳細については、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」をご覧ください。 
[パイプラインの監視](#monitor-pipeline) | この手順では、Azure ポータルを使用して、入力テーブルと出力テーブルのスライスを監視します。

> [AZURE.IMPORTANT] 
「[チュートリアルの概要](data-factory-get-started.md)」という記事を参照し、前提条件の手順を完了してから、このチュートリアルを実行してください。

## データ ファクトリの作成
この手順では、Azure ポータルを使用して、**ADFTutorialDataFactory** という名前の Azure Data Factory を作成します。

1.	[Azure ポータル][azure-portal]にログインしたら、左下隅にある **[新規]** をクリックします。**[作成]** ブレードで **[データ分析]** を選択し、**[データ分析]** ブレードで **[Data Factory]** をクリックします。 

	![New->DataFactory][image-data-factory-new-datafactory-menu]

6. **[新しいデータ ファクトリ]** ブレードで以下の手順を実行します。
	1. **[名前]** に「**ADFTutorialDataFactory**」と入力します。 
	
  		![[新しいデータ ファクトリ] ブレード][image-data-factory-getstarted-new-data-factory-blade]
	2. **[リソース グループ名]** をクリックし、以下の手順を実行します。
		1. **[新しいリソース グループを作成]** をクリックします。
		2. **[リソース グループの作成]** ブレードで、リソース グループの **[名前]** に「**ADFTutorialResourceGroup**」と入力し、**[OK]** をクリックします。 

			![リソース グループの作成][image-data-factory-create-resource-group]

		このチュートリアルの一部の手順は、**ADFTutorialResourceGroup** という名前のリソース グループを使用することを前提としています。リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../resource-group-overview.md)に関するページを参照してください。  
7. **[新しいデータ ファクトリ]** ブレードで、**[スタート画面に追加する]** が選択されていることを確認してください。
8. **[新しいデータ ファクトリ]** ブレードで **[作成]** をクリックします。

	Azure Data Factory の名前はグローバルに一意にする必要があります。**""ADFTutorialDataFactory" という名前の Data Factory は使用できません"** というエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactory など) 作成し直してください。Data Factory アーティファクトの名前付け規則については、[Data Factory - 名前付け規則](data-factory-naming-rules.md)に関するトピックを参照してください。
	 
	![使用できない Data Factory 名][image-data-factory-name-not-available]
	
	> [AZURE.NOTE] データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。

9. 左側の **[通知]** ハブをクリックし、作成プロセスからの通知を探します。**[通知]** ブレードが開いている場合は、**[X]** をクリックして閉じます。
10. 作成が完了すると、次に示すような **[Data Factory]** ブレードが表示されます。

    ![データ ファクトリのホーム ページ][image-data-factory-get-stated-factory-home-page]

## リンクされたサービスの作成
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。データ ストアには、Azure Storage、Azure SQL Database、またはオンプレミスの SQL Server データベースを指定できます。

この手順では、**AzureStorageLinkedService** と **AzureSqlLinkedService** の 2 つのリンクされたサービスを作成します。リンクされたサービス AzureStorageLinkedService は Azure ストレージ アカウントを、AzureSqlLinkedService は Azure SQL Database を **ADFTutorialDataFactory** にリンクします。このチュートリアルの後半で、AzureStorageLinkedService 内の BLOB コンテナーから AzureSqlLinkedService 内の SQL テーブルにデータをコピーするパイプラインを作成します。

### Azure ストレージ アカウント用にリンクされたサービスを作成する
1.	**[Data Factory]** ブレードで、**[作成とデプロイ]** タイルをクリックして、Data Factory の**エディター**を起動します。

	![[作成とデプロイ] タイル][image-author-deploy-tile]

	 
5. **エディター**のツール バーで **[新しいデータ ストア]** ボタンをクリックし、ドロップダウン メニューから **[Azure Storage]** を選択します。Azure Storage のリンクされたサービスを作成するための JSON テンプレートが右側のウィンドウに表示されます。

	![エディターの [新しいデータ ストア] ボタン][image-editor-newdatastore-button]
    
6. **accountname** と **accountkey** を Azure ストレージ アカウントの名前とキーの値に置き換えます。

	![エディターの Blob Storage JSON](./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png)
	
	JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](http://go.microsoft.com/fwlink/?LinkId=516971)を参照してください。

6. ツール バーの **[デプロイ]** をクリックして、AzureStorageLinkedService をデプロイします。タイトル バーに **"リンクされたサービスが正常に作成されました"** というメッセージが表示されていることを確認します。

	![エディターの Blob Storage デプロイ][image-editor-blob-storage-deploy]

### Azure SQL Database 用にリンクされたサービスを作成する
1. **Data Factory エディター**のツール バーで **[新しいデータ ストア]** ボタンをクリックし、ドロップダウン メニューから **[Azure SQL Database]** を選択します。Azure SQL のリンクされたサービスを作成するための JSON テンプレートが右側のウィンドウに表示されます。

	![エディターの Azure SQL 設定][image-editor-azure-sql-settings]

2. **servername**、**databasename**、****username@servername**、**password** を、Azure SQL のサーバー名、データベース名、ユーザー アカウント、パスワードに置き換えます。
3. ツール バーの **[デプロイ]** をクリックして、AzureSqlLinkedService を作成してデプロイします。 
   

## データセットを作成する
前の手順では、**AzureStorageLinkedService** と **AzureSqlLinkedService** というリンクされたサービスを作成し、Azure ストレージ アカウントと Azure SQL Database を **ADFTutorialDataFactory** という Data Factory にリンクしました。この手順では、**EmpTableFromBlob** と **EmpSQLTable** の 2 つのデータ ファクトリ テーブルを定義します。これらは AzureStorageLinkedService と AzureSqlLinkedService が参照するデータ ストアに格納されている入力/出力データを表します。EmpTableFromBlob の場合、ソース データを持つ BLOB を含む BLOB コンテナーを指定します。EmpSQLTable の場合は、出力データを格納する SQL テーブルを指定します。

### 入力データセットの作成 
テーブルとは四角形のデータセットで、スキーマを持っています。この手順では、リンクされたサービス **AzureStorageLinkedService** が表す Azure Storage 内の BLOB コンテナーをポイントする **EmpBlobTable** という名前のテーブルを作成します。

1. Data Factory **エディター**のツール バーで **[新しいデータセット]** ボタンをクリックし、ドロップダウン メニューから **[BLOB テーブル]** をクリックします。 
2. 右側のウィンドウの JSON スクリプトを、次の JSON スニペットに置き換えます。 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     以下の点に注意してください。
	
	- dataset の **type** は **AzureBlob** に設定されています。
	- **linkedServiceName** は **AzureStorageLinkedService** に設定されています。このリンクされたサービスは手順 2. で作成しました。
	- **folderPath** が **adftutorial** コンテナーに設定されています。フォルダー内の BLOB の名前を指定することもできます。BLOB の名前を指定しない場合、コンテナー内のすべての BLOB からのデータが入力データと見なされます。  
	- format の **type** が **TextFormat** に設定されています。
	- テキスト ファイル内に 2 つのフィールド (**FirstName** と **LastName**) があり、コンマ (**columnDelimiter**) で区切られています。	
	- **availability** が **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。そのため、Data Factory サービスでは、指定した BLOB コンテナー (**adftutorial**) のルート フォルダーにある入力データを 1 時間ごとに検索します。 
	

	**入力****テーブル**に **fileName** を指定しない場合、入力フォルダー (**folderPath**) のすべてのファイルまたは BLOB が入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。例については、[チュートリアル][adf-tutorial]のサンプル ファイルを参照してください。
 
	**output table** に **fileName** を指定しない場合、**folderPath** に生成されるファイルには Data.&lt;Guid&gt;.txt という形式で名前が付けられます (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)。

	**folderPath** と **fileName** を **SliceStart** 時刻に基づいて動的に設定するには、**partitionedBy** プロパティを使用します。次の例では、folderPath に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。たとえば、スライスが 2014-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2014/10/20 に設定され、fileName は 08.csv に設定されます。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](http://go.microsoft.com/fwlink/?LinkId=516971)を参照してください。

2. ツール バーの **[デプロイ]** をクリックし、**EmpTableFromBlob** テーブルを作成してデプロイします。エディターのタイトル バーに **"テーブルが正常に作成されました"** というメッセージが表示されていることを確認します。

### 出力データセットの作成
手順のこの部分では、リンクされたサービス **AzureSqlLinkedService** が表す Azure SQL Database 内の SQL テーブルをポイントする **EmpSQLTable** という名前の出力テーブルを作成します。

1. Data Factory **エディター**のツール バーで **[新しいデータセット]** ボタンをクリックし、ドロップダウン メニューから **[Azure SQL テーブル]** をクリックします。 
2. 右側のウィンドウの JSON スクリプトを、次の JSON スニペットに置き換えます。

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     以下の点に注意してください。
	
	* dataset の **type** が **AzureSQLTable** に設定されています。
	* **linkedServiceName** が **AzureSqlLinkedService** (手順 2. で作成したリンク) に設定されています。
	* **tablename** は **emp** に設定されています。
	* データベース内の emp テーブルに 3 つの列 (**ID**、**FirstName**、**LastName**) がありますが、ID は識別子の列であるため、ここでは **FirstName** と **LastName** のみを指定する必要があります。
	* **availability** が **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。Data Factory サービスは、Azure SQL Database 内の **emp** テーブルに 1 時間ごとに出力データ スライスを生成します。


3. ツール バーの **[デプロイ]** をクリックし、**EmpSQLTable** テーブルを作成してデプロイします。


## パイプラインの作成
この手順では、**EmpTableFromBlob** を入力、**EmpSQLTable** を出力として使用し、**コピー アクティビティ**を持つパイプラインを作成します。

1. **Data Factory エディター**のツール バーで **[新しいパイプライン]** をクリックします。ボタンが表示されない場合は、ツール バーの **[...] (省略記号)** をクリックします。または、ツリー ビューの **[パイプライン]** を右クリックして、**[新しいパイプライン]** をクリックする方法もあります。

	![エディターの [新しいパイプライン] ボタン][image-editor-newpipeline-button]
 
2. 右側のウィンドウの JSON スクリプトを、次の JSON スニペットに置き換えます。
		
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	以下の点に注意してください。

	- activities セクションに、**type** が **CopyActivity** に設定されたアクティビティが 1 つだけあります。
	- アクティビティの入力は **EmpTableFromBlob** に設定され、アクティビティの出力は **EmpSQLTable** に設定されています。
	- **transformation** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。

	**start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。日付の部分のみを指定し、時刻の部分をスキップすることもできます。たとえば、"2015-02-03" と "2015-02-03T00:00:00Z" は同じです。
	
	start と end の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。**end** の時刻は省略可能ですが、このチュートリアルでは使用します。
	
	**end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティとして指定します。
	
	上記の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。
	
	JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](http://go.microsoft.com/fwlink/?LinkId=516971)を参照してください。

4. ツール バーの **[デプロイ]** をクリックし、**ADFTutorialPipeline** を作成してデプロイします。**"パイプラインが正常に作成されました"** というメッセージが表示されていることを確認します。
5. ここで、**[X]** をクリックして **[エディター]** ブレードを閉じます。もう一度 **[X]** をクリックして、[ADFTutorialDataFactory] ブレードをツール バーおよびツリー ビューと共に閉じます。**"保存されていない編集は破棄されます"** というメッセージが表示されたら、**[OK]** をクリックします。
6. **ADFTutorialDataFactory** の **[Data Factory]** ブレードに戻ります。

**お疲れさまでした。** これで、Azure Data Factory、リンクされたサービス、テーブル、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。
 
### ダイアグラム ビューでの Data Factory の表示 
1. **[Data Factory]** ブレードで、**[ダイアグラム]** をクリックします。

	![[Data Factory] ブレードの [ダイアグラム] タイル][image-datafactoryblade-diagramtile]

2. 以下のような図が表示されるはずです。

	![ダイアグラム ビュー][image-data-factory-get-started-diagram-blade]

	パイプラインとテーブルは、拡大、縮小、100% に拡大、ウィンドウのサイズに合わせて大きさを変更、自動的に配置などの表示が可能です。また、系列情報を表示 (選択した項目の上位項目や下位項目を強調表示) することもできます。オブジェクト (入力/出力テーブルまたはパイプライン) をダブルクリックすると、そのオブジェクトのプロパティを表示できます。 
3. ダイアグラム ビューで **[ADFTutorialPipeline]** を右クリックして **[パイプラインを開く]** をクリックします。パイプライン内のアクティビティに加えて、アクティビティの入力データセットと出力データセットが表示されます。このチュートリアルでは、パイプラインのアクティビティ (コピー アクティビティ) は、入力データセットとして EmpTableBlob、出力データセットとして EmpSQLTable の 1 つだけです。   

	![パイプラインを開く](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. 左上にある階層リンクで **[Data Factory]** をクリックして、ダイアグラム ビューに戻ります。ダイアグラム ビューには、すべてのパイプラインが表示されます。この例では 1 つのパイプラインのみ作成しました。
 

## パイプラインの監視
このステップでは、Azure クラシック ポータルを使用して、Azure Data Factory の状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。監視用のコマンドレットの使用方法については、「[PowerShell コマンドレットを使用した Data Factory の監視と管理][monitor-manage-using-powershell]」を参照してください。

1. まだ開いていない場合は、[Azure クラシック ポータル (プレビュー)][azure-portal] に移動します。 
2. **ADFTutorialDataFactory** のブレードが開いていない場合は、**スタート画面**で **[ADFTutorialDataFactory]** をクリックして開きます。 
3. 作成したテーブルの数と名前、およびパイプラインの名前がブレードに表示されます。

	![名前のついたホーム ページ][image-data-factory-get-started-home-page-pipeline-tables]

4. **[データセット]** タイルをクリックします。
5. **[データセット]** ブレードで、**[EmpTableFromBlob]** をクリックします。これは、**ADFTutorialPipeline** 用の入力テーブルです。

	![EmpTableFromBlob を選択したデータセット][image-data-factory-get-started-datasets-emptable-selected]   
5. 現在の時刻までのデータ スライスが既に生成されており、**準備完了**になっています。これは、**emp.txt** ファイルが BLOB コンテナー **adftutorial\\input** 内に常に存在しているためです。下部の **[最近失敗したスライス]** セクションにスライスが表示されていないことを確認します。

	**[最近更新したスライス]** と **[最近失敗したスライス]** の一覧は、どちらも **[最終更新時刻]** で並べ替えられます。次の状況では、スライスの更新時刻が変更されます。
    

	-  **Set-AzureRmDataFactorySliceStatus** を使用したり、スライスの **[スライス]** ブレードで **[実行]** をクリックしたりすることで、スライスの状態を手動で更新した場合。
	-  スライスの実行 (実行の開始、実行の終了と失敗、実行の終了と成功など) により、スライスの状態が変わります。
 
	一覧のタイトルをクリックするか、**[...] (省略記号)** をクリックすると、さらに多くのスライスが一覧表示されます。スライスをフィルター処理するには、ツール バーの **[フィルター]** をクリックします。
	
	代わりに、スライスの開始時刻と終了時刻で並べ替えられたデータ スライスを表示するには、**[データ スライス (スライスの時刻別)]** タイルをクリックします。

	![データ スライス (スライスの時刻別)][DataSlicesBySliceTime]

6. **[データセット]** ブレードで、**[EmpSQLTable]** をクリックします。これは、**ADFTutorialPipeline** 用の出力テーブルです。

	![[データセット] ブレード][image-data-factory-get-started-datasets-blade]



	 
6. **[EmpSQLTable]** ブレードが次のように表示されます。

	![[テーブル] ブレード][image-data-factory-get-started-table-blade]
 
7. 現在の時刻までのデータ スライスが既に生成されており、**準備完了**になっています。下部の **[問題のあるスライス]** セクションにはスライスが表示されていません。
8. **[...] (省略記号)** をクリックし、すべてのスライスを表示します。

	![[データ スライス] ブレード][image-data-factory-get-started-dataslices-blade]

9. 一覧の任意のデータ スライスをクリックすると、**[データ スライス]** ブレードが表示されます。

	![[データ スライス] ブレード][image-data-factory-get-started-dataslice-blade]
  
	スライスが **[準備完了]** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、**[準備完了でない上位スライス]** の一覧に表示されます。

11. **[データ スライス]** ブレードの下部にある一覧に、すべてのアクティビティの実行状況が表示されます。**[アクティビティの実行状況]** をクリックすると、**[アクティビティの実行状況の詳細]** ブレードが表示されます。

	![アクティビティの実行状況の詳細][image-data-factory-get-started-activity-run-details]

	
12. **[X]** をクリックすると、すべてのブレードが閉じられ、**ADFTutorialDataFactory** のホーム ブレードに戻ります。
14. (省略可能) **ADFTutorialDataFactory** のホーム ページで **[パイプライン]** をクリックし、**[パイプライン]** ブレードの **[ADFTutorialPipeline]** をクリックして、入力テーブル (**Consumed**) または出力テーブル (**Produced**) を表示します。
15. **SQL Server Management Studio** を起動し、Azure SQL Database に接続して、データベース内の **emp** テーブルに行が挿入されていることを確認します。

	![SQL クエリの結果][image-data-factory-get-started-sql-query-results]


## 概要 
このチュートリアルでは、Azure Data Factory を作成し、Azure BLOB から Azure SQL Database にデータをコピーしました。また、Azure ポータルを使用して、データ ファクトリ、リンクされたサービス、テーブル、パイプラインを作成しました。以下は、このチュートリアルで実行した手順の概要です。

1.	**Azure Data Factory を作成します**。
2.	データ ストアとコンピューティングをデータ ファクトリに**リンクするサービス** (**リンクされたサービス**と呼びます) を作成します。
3.	パイプラインの入力データと出力データを記述した**テーブル**を作成します。
4.	**パイプライン**を作成します。パイプラインは、入力を処理し、出力を生成する 1 つ以上のアクティビティで構成されます。パイプラインの **開始**時間と**終了**時間を指定して、パイプラインの有効期間を設定します。有効期間は、データ スライスが生成される期間を定義します。


## 関連項目
Azure Data Factory の**コピー アクティビティ**の詳細については、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」を参照してください。

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png
 

<!---HONumber=AcomDC_0323_2016-->