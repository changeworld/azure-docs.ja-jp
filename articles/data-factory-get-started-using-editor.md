<properties 
	pageTitle="Azure Data Factory を使い始める" 
	description="このチュートリアルでは、BLOB から Azure SQL Database インスタンスにデータをコピーするサンプル データ パイプラインを作成する方法を示します。" 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# チュートリアル: の作成し、データ工場出荷時のエディターを使用して、データの出荷時の監視
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##このチュートリアルの内容
このチュートリアルには、次の手順が含まれています。

手順 | 説明
-----| -----------
[手順 1: Azure のデータのファクトリを作成します。](#CreateDataFactory) | このステップでは、Azure のデータのファクトリという名前を作成します **ADFTutorialDataFactory**です。  
[手順 2: 関連付けられているサービスを作成します。](#CreateLinkedServices) | このステップでは 2 つの関連付けられているサービスを作成します。 **StorageLinkedService** と **AzureSqlLinkedService**です。StorageLinkedService、Azure のストレージをリンクし、[AzureSqlLinkedService に Azure SQL データベース、ADFTutorialDataFactory をリンクします。Azure blob ストレージ内の blob コンテナーで、パイプラインの入力データが存在して、出力データは、Azure SQL データベース内のテーブルに格納します。そのため、これらの 2 つのデータ ストアに、データのファクトリに関連付けられているサービスとして追加します。      
[手順 3: 入力を作成し、テーブルの出力](#CreateInputAndOutputDataSets) | 前の手順では入力/出力データを含むデータ ストアを参照する、関連付けられているサービスを作成しました。このステップでは 2 つのデータ工場出荷時のテーブル--を定義する **EmpTableFromBlob** と **EmpSQLTable** --データ ストアに格納されている入力/出力データを表します。EmpTableFromBlob は、ソース データとは、EmpSQLTable の blob を含む blob コンテナーを指定する、出力データを格納する SQL テーブルを指定します。データの構造、データの可用性などの他のプロパティも指定しています. 
[手順 4: を作成して、パイプラインの実行](#CreateAndRunAPipeline) | このステップでは、パイプラインという名前を作成します **ADFTutorialPipeline** 、ADFTutorialDataFactory にします。パイプラインには、 **コピー アクティビティ** 、出力の Azure の SQL テーブルには、Azure のデータのコピーの入力を blob ことです。
[手順 5: スライスとパイプラインを監視します。](#MonitorDataSetsAndPipeline) | このステップでは Azure プレビュー ポータルを使用して入力と出力テーブルのスライスを監視します。
 

## <a name="CreateDataFactory"></a>手順 1: Azure のデータのファクトリを作成します。
このステップで、Azure のデータのファクトリという名前の作成に、Azure プレビュー ポータルを使用する **ADFTutorialDataFactory**です。

1.	ログインした後、 [Azure プレビュー ポータル][azure-preview-portal], 、] をクリックして **新規** 、左下隅にあるから次のように選択します。 **データ分析** で、 **作成** ] をクリックしてブレード、 **データ ファクトリ** で、 **データ分析** ブレードします。 

	![新しい DataFactory -> します。][image-data-factory-new-datafactory-menu]

6.  **データの新しいファクトリ** ブレード。
	1. 入力 **ADFTutorialDataFactory** の **名前**です。 
	
  		![新しいデータの工場出荷時のブレード][image-data-factory-getstarted-new-data-factory-blade]
	2. クリックして **リソース グループ名** し、次の操作します。
		1. クリックして **新しいリソース グループの作成**です。
		2.  **リソース グループの作成** ブレードで入力 **ADFTutorialResourceGroup** の **名前** のリソース グループ、および] をクリックします **[ok]**です。 

			![リソース グループを作成します。][image-data-factory-create-resource-group]

		名前を使用することを前提ともこのチュートリアルの手順: **ADFTutorialResourceGroup** 、リソース グループです。リソース グループの詳細については、次を参照してください。 [リソース グループを使用して、Azure リソースを管理する](resource-group-overview.md)です。  
7.  **データの新しいファクトリ** ブレード、ことに注意して **スタート ボードに追加** が選択されています。
8. クリックして **作成** で、 **データの新しいファクトリ** ブレードします。

	> [AZURE.NOTE]Azure Data Factor の名前はグローバルに一意にする必要があります。エラーが発生する場合: **データ工場出荷時の名前"ADFTutorialDataFactory"は使用できません**, 、データのファクトリ (たとえば、yournameADFTutorialDataFactory) の名前を変更および再作成してみてください。このチュートリアルの残りの手順を実行中に ADFTutorialFactory の代わりに、この名前を使用します。参照してください [データ Factory - 名前付け規則][data-factory-naming-rules] データ工場出荷時の成果物の名前付け規則についてのトピックです。
	 
	![データのファクトリは使用できません。][image-data-factory-name-not-available]

9. クリックして **通知** ハブ、左側に、作成プロセスからの通知を探します。クリックして **X** を閉じるには、 **通知** ブレードが開いている場合。
10. 作成が完了したら、後に表示されます、 **データ ファクトリ** ブレードの次のようです。

    ![データの工場出荷時のホーム ページ][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>手順 2: 関連付けられているサービスを作成します。
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。データ ストアには、Azure ストレージ、Azure SQL データベース、または内部設置型の SQL Server データベースを指定できます。

このステップでは 2 つの関連付けられているサービスを作成します。 **StorageLinkedService** と **AzureSqlLinkedService**です。Azure のストレージ アカウントにサービスへのリンクをリンクする StorageLinkedService され AzureSqlLinkedService リンクするには、Azure SQL データベース、 **ADFTutorialDataFactory**です。StorageLinkedService 内の blob コンテナーから AzureSqlLinkedService 内の SQL テーブルにデータをコピーするこのチュートリアルの後半では、パイプラインは作成します。

### Azure のストレージ アカウントのリンクされているサービスを作成します。
1.	 **データ ファクトリ** ブレードで] をクリックして **作成者と展開** タイルを起動する、 **エディター** データ ファクトリのです。

	![タイルの作成とデプロイ][image-author-deploy-tile]

	> [AZURE.NOTE]参照してください [データ工場出荷時のエディター][data-factory-editor] データ工場出荷時のエディターの詳細の概要についてのトピックです。
	 
5.  **エディター**, 、] をクリックして **データ ストアの新しい** ] ボタンをクリックし、ツールバー **Azure ストレージ** 、ドロップ ダウン メニューからです。右側のウィンドウで、リンクされている Azure のストレージ サービスを作成するためには、JSON テンプレートが表示されます。

	![新しいデータ ストアのボタンのエディター][image-editor-newdatastore-button]
    
6. 置き換える **accountname** と **accountkey** アカウント名とアカウント キーの値を Azure ストレージ アカウントをします。

	![Blob ストレージのエディター JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]参照してください [JSON スクリプト参照](http://go.microsoft.com/fwlink/?LinkId=516971) JSON のプロパティに関する詳細です。

6. クリックして **展開** 、StorageLinkedService を展開するには、ツールバーのです。メッセージが表示されることを確認 **リンク サービス作成が正常に** 、タイトル バーにします。

	![エディターの Blob ストレージを展開します。][image-editor-blob-storage-deploy]

### Azure SQL データベースのリンクされているサービスを作成します。
1.  **データ工場出荷時のエディター** , 、] をクリックして **データ ストアの新しい** ] ボタンをクリックし、ツールバー **Azure SQL データベース** 、ドロップ ダウン メニューからです。右側のウィンドウで、リンクされている Azure の SQL サービスを作成するためには、JSON テンプレートが表示されます。

	![Editr Azure SQL の設定][image-editor-azure-sql-settings]

2. 置き換える **servername**, 、**databasename**, 、**username@servername**, 、および **パスワード** 、Azure の SQL サーバー、データベース、ユーザーのアカウントとパスワードの名前を持つ 。
3. をクリックして **展開** 、ツールバーを作成し、AzureSqlLinkedService を展開します。 
   

## <a name="CreateInputAndOutputDataSets"></a>手順 3: 入力を作成し、テーブルの出力
関連付けられているサービスを作成した前の手順で **StorageLinkedService** と **AzureSqlLinkedService** 、データの工場出荷時に、Azure のストレージ アカウントと Azure SQL データベースをリンクする: **ADFTutorialDataFactory**です。このステップでは 2 つのデータ工場出荷時のテーブル--を定義する **EmpTableFromBlob** と **EmpSQLTable** --それぞれの StorageLinkedService と AzureSqlLinkedService により参照されるデータ ストアに格納されている入力/出力データを表します。EmpTableFromBlob、EmpSQLTable のソース データと blob を含む blob コンテナーを指定するが、出力データを格納する SQL テーブルを指定します。

### 入力テーブルの作成 
テーブルとは四角形のデータセットで、スキーマを持っています。このステップではという名前のテーブルを作成します **EmpBlobTable** によって表される、Azure ストレージ内の blob コンテナーを指す、 **StorageLinkedService** サービスにリンクします。

1.  **エディター** をクリックして、データのファクトリ **新しいデータセット** ] ボタンをクリックして、ツールバーを **Blob テーブル** 、ドロップ ダウン メニューからです。 
2. 次の JSON 断片を右側のウィンドウで JSON を置き換えます。 

        {
     	    "name": "EmpTableFromBlob",
		    "properties":
    		{
        		"structure":  
       			 [ 
            		{ "name": "FirstName", "type": "String"},
            		{ "name": "LastName", "type": "String"}
        		],
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     以下の点に注意してください。
	
	- 場所 **型** に設定されている **AzureBlobLocation**です。
	- **linkedServiceName** に設定されている **StorageLinkedService**です。このリンクのサービスを手順 2. で作成しました。
	- **folderPath** に設定されている、 **adftutorial** コンテナーです。フォルダー内の BLOB の名前を指定することもできます。BLOB の名前を指定しない場合、コンテナー内のすべての BLOB からのデータが入力データと見なされます。  
	- 形式 **型** に設定されている **TextFormat**
	- 2 つのフィールドは、テキスト ファイル – **FirstName** と **LastName** 、コンマで区切られた – (* * * * columnDelimiter)	
	-  **可用性** に設定されている **1 時間ごと** (* * * * 頻度に設定されている **時間** と **間隔** に設定されている **1** )、データのファクトリのサービスになります入力データの 1 時間ごと、blob コンテナー内のルート フォルダーにあるため、(* * adftutorial * *) を指定します。 
	

	指定しない場合、 **ファイル名** の **入力** **テーブル**, 、入力フォルダーからすべてのファイルと blob (* * * * folderPath) の入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。サンプル ファイルを参照してください、 [チュートリアル][adf-tutorial] 例についてはします。
 
	指定しない場合、 **ファイル名** の **出力テーブル**, 、内のファイルの生成、 **folderPath** 、次の形式で名前が指定しますデータです。 & lt;。Guid & gt;。txt (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt。)。

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

	> [AZURE.NOTE]参照してください [JSON スクリプト参照](http://go.microsoft.com/fwlink/?LinkId=516971) JSON のプロパティに関する詳細です。

2. クリックして **展開** 作成および展開するには、ツールバーの **EmpTableFromBlob** テーブルです。いることを確認、 **正常に作成されたテーブル** 、エディターのタイトル バーにメッセージをします。

### 出力テーブルの作成
という名前の出力テーブルを作成する、ステップのこのパートで **EmpSQLTable** で表されるポイント SQL テーブルに、Azure SQL データベースを **AzureSqlLinkedService** サービスにリンクします。

1.  **エディター** をクリックして、データのファクトリ **新しいデータセット** ] ボタンをクリックして、ツールバーを **Azure の SQL テーブル** 、ドロップ ダウン メニューからです。 
2. 次の JSON 断片を右側のウィンドウで JSON を置き換えます。

        {
    		"name": "EmpSQLTable",
    		"properties":
    		{
        		"structure":
        		[
                	{ "name": "FirstName", "type": "String"},
                	{ "name": "LastName", "type": "String"}
        		],
        		"location":
        		{
            		"type": "AzureSqlTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "AzureSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     以下の点に注意してください。
	
	* 場所 **型** に設定されている **AzureSQLTableLocation**です。
	* **linkedServiceName** に設定されている **AzureSqlLinkedService** (このリンク サービス手順 2. で作成した)。
	* **tablename** に設定されている **emp**です。
	* – 3 つの列がある **ID**, 、**FirstName**, と **LastName** –、データベース内には、emp テーブル内のみを指定する必要があるために、ID が id 列が **FirstName** と **LastName** ここです。
	*  **可用性** に設定されている **1 時間ごと** (* * * * 頻度に設定 **時間** と **間隔** に設定 **1**)。データのファクトリのサービスがで 1 時間ごとに、出力データのスライスの生成は、 **emp** 、Azure SQL データベースのテーブルにします。


3. クリックして **展開** 作成および展開するには、ツールバーの **EmpSQLTable** テーブルです。


## <a name="CreateAndRunAPipeline"></a>手順 4: を作成して、パイプラインの実行
このステップでパイプラインを作成する、 **コピー アクティビティ** を使用して **EmpTableFromBlob** の入力としてと **EmpSQLTable** 出力として。

1.  **エディター** をクリックして、データのファクトリ **新しいパイプライン** ツールバーのボタンをクリックします。ボタンが表示されない場合は、ツール バーの **[...] (省略記号)** をクリックします。または、ツリー ビューの **[パイプライン]** を右クリックして、**[新しいパイプライン]** をクリックする方法もあります。

	![エディターの新しいパイプラインのボタン][image-editor-newpipeline-button]
 
2. 次の JSON 断片を右側のウィンドウで JSON を置き換えます。

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpTableFromBlob"} ],
						"outputs": [ {"name": "EmpSQLTable"} ],		
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		
					}
        		],

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	以下の点に注意してください。

	- セクションでは、アクティビティ、アクティビティの 1 つだけが持つ **型** に設定されている **CopyActivity**です。
	- 入力に設定されているアクティビティ **EmpTableFromBlob** に設定されているアクティビティの出力と **EmpSQLTable**です。
	-  **変換** セクションで、 **BlobSource** がソースの種類として指定されていると **SqlSink** は、シンクの種類として指定します。

	> [AZURE.NOTE]値を置き換える、 **開始** 、現在の日付を持つプロパティと **終了** で、次の日の値です。日付の部分のみを指定し、日付、時刻の時間部分をスキップできます。たとえば、「2015年-02-03」と同じです"2015年-02-03T00:00:00Z"これら両方の開始し、終了の datetimes である必要があります [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)です。例: 2014年-10-14T16:32:41Z です。 **終了** 時刻は省略可能では、このチュートリアルで使用します。値を指定しない場合、 **終了** として計算されます。 プロパティを"* * スタート + 48 時間 * *"です。パイプラインを無期限に実行する次のように指定します。 **9999-09-09** の値として、 **終了** プロパティです。上記の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。
	
	> [AZURE.NOTE]参照してください [JSON スクリプト参照](http://go.microsoft.com/fwlink/?LinkId=516971) JSON のプロパティに関する詳細です。

4. クリックして **展開** 作成および展開するには、ツールバーの **ADFTutorialPipeline**です。いることを確認、 **パイプラインが正常に作成された** メッセージです。
5. これで、閉じる、 **エディター** ] をクリックしてブレード **X**です。クリックして **X** 、ツールバーとツリー ビューでは、ADFTutorialDataFactory ブレードを閉じるには、もう一度です。表示する場合は **、保存されていない編集が破棄されます** メッセージで、クリックして **[ok]**です。
6. する必要があります、 **データ ファクトリ** ブレードで、 **ADFTutorialDataFactory**です。

**ご利用ありがとうございます。** これで、Azure Data Factory、リンクされたサービス、テーブル、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。
 
### ダイアグラム ビューで、データのファクトリを表示します。 
1.  **データ ファクトリ** ブレードで] をクリックして **ダイアグラム**です。

	![データ工場出荷時のブレード - [ダイアグラムのタイル][image-datafactoryblade-diagramtile]

2. 以下のような図が表示されるはずです。

	![ダイアグラム ビュー][image-data-factory-get-started-diagram-blade]

	ズーム イン、ズーム アウト、ズームを 100% ズームに合わせて自動的に、パイプラインと、テーブルに配置および系列の情報を表示する (上位および下位の項目を選択したアイテムの強調表示) することができます。(入力/出力テーブルまたはパイプライン) のプロパティを表示するオブジェクト上の二重 blick ことができます。 
3. 右クリックして **ADFTutorialPipeline** 、ダイアグラム ビューに **開くパイプライン**です。アクティビティの入力呼び出し力のデータセットと、パイプライン内のアクティビティが表示されます。このチュートリアルでは、アクティビティの 1 つだけ入力データセットと出力データセットとして EmpSQLTable EmpTableBlob でパイプライン (コピー アクティビティ) があります。   

	![開いているパイプライン](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. クリックして **データ ファクトリ** 、ダイアグラム ビューに戻るには、左上隅にある階層リンク内です。ダイアグラム ビューには、すべてのパイプラインが表示されます。この例では 1 つのパイプラインのみ作成しました。
 

## <a name="MonitorDataSetsAndPipeline"></a>手順 5: データセットとパイプラインを監視します。
このステップでは、Azure ポータルを使用して、Azure Data Factory の状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。詳細監視のコマンドレットの使用については、次を参照してください。 [モニターと PowerShell コマンドレットを使用して、管理データ ファクトリ][monitor-manage-using-powershell]です。

1. 移動 [Azure ポータル (プレビュー)][azure-preview-portal] 開かれていない場合。 
2. 場合、ブレードの **ADFTutorialDataFactory** が開いていないをクリックして開きます **ADFTutorialDataFactory** 上、 **スタート ボード**です。 
3. 作成したテーブルの数と名前、およびパイプラインの名前がブレードに表示されます。

	![名前のホーム ページ][image-data-factory-get-started-home-page-pipeline-tables]

4. ここでは、クリックして **データセット** 並べて表示します。
5.  **データセット** ブレードをクリックして **EmpTableFromBlob**です。これは、入力テーブルの **ADFTutorialPipeline**です。

	![選択した EmpTableFromBlob を持つデータセット][image-data-factory-get-started-datasets-emptable-selected]   
5. 現在の時刻までのデータ スライスを既に生成されているし、していることを確認 **準備ができて** ため、 **emp.txt** ファイルすべて、時間が存在する blob コンテナーに: **adftutorial\input**です。あるスライスなしに表示することを確認、 **最近スライスが失敗しました** 下部のセクションです。

	どちらも **スライスを最近更新された** と **最近スライスが失敗しました** リストはによって並べ替えられます、 **最終更新時刻**です。次の状況では、スライスの更新時間が変更されます。
    

	-  スライスのステータスは、手動で更新するなどを使用して、 **セット AzureDataFactorySliceStatus** (または) をクリックして **実行** 上、 **スライス** スライスのブレードします。
	-  スライスは、実行のための状態を変更 (など、実行の開始、実行を終了し、失敗した、実行が成功したが終了したなど)。
 
	リストまたはのタイトルをクリックして **しています.(省略記号)** より大規模な一覧のスライスを参照してください。クリックして **フィルター** をスライス、フィルター処理するには、ツールバーのです。
	
	代わりに、スライスの開始時刻から終了時刻によって並べ替えられたデータのスライスを表示する] をクリックして **(スライスの時刻) でのデータ スライス** 並べて表示します。

	![データのスライスでスライスの時間][DataSlicesBySliceTime]

6. ここで、 **データセット** ブレードをクリックして、 **EmpSQLTable**です。これは、出力テーブルの **ADFTutorialPipeline**です。

	![データ セットのブレード][image-data-factory-get-started-datasets-blade]



	 
6. 表示されます、 **EmpSQLTable** ブレードを次に示すようにします。

	![テーブルのブレード][image-data-factory-get-started-table-blade]
 
7. 現在の時刻までのデータ スライスを既に生成されているし、していることを確認 **準備ができて**です。スライスなしに表示、 **問題スライス** 下部のセクションです。
8. ] をクリック **しています.(省略記号)** すべてのスライスを参照してください。

	![データ スライス ブレード][image-data-factory-get-started-dataslices-blade]

9. 一覧からいずれかのデータ スライスをクリックして表示、 **データ スライス** ブレードします。

	![データ スライス ブレード][image-data-factory-get-started-dataslice-blade]
  
	スライスではない場合、 **準備ができて** 状態、準備ができていないし、現在のスライスでの実行をブロックしているアップ ストリームのスライスにわかり、 **準備ができていない上流のスライス** ] ボックスの一覧です。

11.  **データ スライス** ブレードが表示されます、下部にある一覧で、すべての活動が実行されます。クリックして、 **アクティビティの実行** を参照してください、 **アクティビティの実行の詳細** ブレードします。

	![アクティビティの実行の詳細][image-data-factory-get-started-activity-run-details]

	
12. クリックして **X** のホームのブレードに戻るまで、すべてのブレードを閉じる、 **ADFTutorialDataFactory**です。
14. (省略可能)をクリックして **パイプライン** のホーム ページに **ADFTutorialDataFactory**, 、] をクリックして **ADFTutorialPipeline** で、 **パイプライン** ブレード、および入力テーブルへのドリルスルー (* * * * 消費済み) または出力テーブル (* * Produced * *)。
15. 起動 **SQL Server Management Studio**, 、Azure SQL データベースへの接続や、行が挿入されることを確認、 **emp** 、データベース内のテーブルです。

	![sql クエリの結果][image-data-factory-get-started-sql-query-results]


## 概要 
このチュートリアルでは、Azure Data Factory を作成し、Azure BLOB から Azure SQL データベースにデータをコピーしました。データ、ファクトリ、関連付けられているサービス、テーブル、およびパイプラインを作成するのにには、Azure プレビュー ポータルを使用します。以下は、このチュートリアルで実行した手順の概要です。

1.	作成、Azure **データ ファクトリ**です。
2.	作成 **サービス リンク** リンク データ ストアを計算している (と呼ばれます **関連付けられているサービス**) データ ファクトリにします。
3.	作成 **テーブル** を表すデータとパイプラインの出力データを入力します。
4.	作成 **パイプライン**です。パイプラインは、入力を処理し、出力を生成する 1 つ以上のアクティビティで構成されます。指定して、パイプラインのアクティブな期間を設定する **開始** 時間と **終了** パイプラインの時間です。有効期間は、データ スライスが生成される期間を定義します。 


> [AZURE.NOTE]サポートされているアクティビティの一覧は、次を参照してください。 [パイプラインとアクティビティ][msdn-activities] トピックと、サポートされている関連付けられているサービスの一覧は、次を参照してください。 [関連付けられているサービス][msdn-linkedservices] MSDN ライブラリでのトピックです。
> 
> Azure PowerShell を使用してこのチュートリアルには、次を参照してください。 [を作成すると、Azure PowerShell を使用してデータのファクトリのモニター][monitor-manage-using-powershell]です。

## 次のステップ

記事 | 説明
------ | ---------------
[Azure のデータのファクトリのコピーのアクティビティにデータのコピー][copy-activity] | この記事の説明の詳細については、 **コピー アクティビティ** このチュートリアルで使用します。 
[内部設置型データを使用するパイプラインを有効にします。][use-onpremises-datasources] | この資料は、データをコピーする方法について説明するチュートリアル、 **内部設置型 SQL Server データベース** Azure blob にします。 
[チュートリアル: 移動し、データのファクトリを使用してログ ファイルの処理][adf-tutorial] | この記事では、 **エンド ツー エンド チュートリアル** を実装する方法を示す、 **実際のシナリオ** insights にログ ファイルからデータを変換する Azure のデータのファクトリを使用します。
[データの工場出荷時の問題をトラブルシューティングします。][troubleshoot] | この資料で説明する方法 **のトラブルシューティングを行う** Azure のデータのファクトリを発行します。エラーを発生させて (Azure SQL データベースのテーブルを削除する)、ADFTutorialDataFactory でこの記事のチュートリアルを試すことができます。 
[Azure のデータの工場出荷時の開発者向けリファレンス][developer-reference] | コマンドレット、JSON スクリプト、関数などの包括的な参照の内容を開発者向けリファレンスには. 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

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

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir-->