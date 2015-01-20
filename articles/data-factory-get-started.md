<properties title="Get started using Azure Data Factory" pageTitle="Azure Data Factory を使い始める" description="このチュートリアルでは、BLOB から Azure SQL Database インスタンスにデータをコピーするサンプル データ パイプラインを作成する方法を示します。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Azure Data Factory を使ってみる
この記事を使用して、Azure Data Factory を使い始めることができます。この記事のチュートリアルでは、Azure Data Factory を作成し、その Data Factory 内にパイプラインを作成して、Azure BLOB ストレージから Azure SQL データベースにサンプル データをコピーする方法について説明します。

開発者が実行する必要がある一般的な手順を次に示します。 

1.	**Azure Data Factory** を作成します。
2.	データ ストアおよびコンピューティング サービスを Data Factory にリンクするために、**リンクされたサービス**を作成します。たとえば、リンクされたサービスで、Azure SQL データベースまたは HDInsight クラスターを Data Factory にリンクできます。
3.	パイプライン用の入力データおよび出力データを表す**テーブル**を作成します。テーブルは、Data Factory にリンクされているデータ ストア内のデータの実際の場所も指定します。たとえば、テーブルは、Azure SQL データベース内の SQL テーブルの名前や Azure BLOB 内の BLOB コンテナーの名前を指定できます。 
4.	**パイプライン**を作成します。パイプラインは、入力データを処理し、出力データを生成する 1 つ以上のアクティビティで構成されます。コピー アクティビティがソース データ ストアからターゲット データ ストアにデータをコピーし、HDInsight アクティビティが Hive/Pig スクリプトを使用して入力データを処理します。  
5.	パイプラインの**有効期間**を (データ処理用に) 指定します。有効期間は、データ スライスが生成される時間を定義します。パイプラインの開始日時と終了日時を指定するか、常時実行することができます。

このチュートリアルでは、次のことについて説明します。 

1.	**Azure プレビュー ポータル**を使用して Azure Data Factory およびデータ ストアのリンクされたサービスを作成します。
2.	**Azure PowerShell** を使用してテーブルとパイプラインを作成します。現在のリリースのポータルでは、テーブルとパイプラインの作成をサポートしていません。


##前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- Azure サブスクリプション。サブスクリプションの入手方法の詳細については、[購入オプション] [azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。
- [Azure PowerShell][download-azure-powershell] をコンピューター上にダウンロードしてインストールします。
- トピック「[Introduction to Azure Data Factory (Azure Data Factory の概要)][data-factory-introduction]」に目を通します。
- Azure ストレージ アカウントこのチュートリアルでは、BLOB ストレージをソース データ ストアとして使用します。Azure ストレージを作成する手順については、「[About Storage Accounts (ストレージ アカウントについて)][data-factory-create-storage]」を参照してください。
- Azure SQL データベース。このチュートリアルでは、サンプル データベースを作成し、そのデータベースをターゲット データ ストアとして使用します。Azure SQL データベースを作成する手順については、「[How to create and configure an Azure SQL Database (Azure SQL データベースを作成して構成する方法)][data-factory-create-sql-database]」を参照してください。

##このチュートリアルの内容

手順 | 説明
-----| -----------
[手順 1:Azure Data Factory を作成する](#CreateDataFactory) | この手順では、**ADFTutorialDataFactory** という名前の Azure Data Factory を作成します。 
[手順 2:リンクされたサービスの作成](#CreateLinkedServices) | この手順では、**MyBlobStore** と **MyAzureSQLStore** の 2 つのリンクされたサービスを作成します。MyBlobStore は Azure ストレージを、MyAzureSQLStore は Azure SQL データベースを ADFTutorialDataFactory にリンクします。
[手順 3:入力データセットと出力データセットを作成する](#CreateInputAndOutputDataSets) | この手順では、2 つのデータセット (**EmpTableFromBlob** と **EmpSQLTable**) を定義します。これらは、次の手順で作成する ADFTutorialPipeline の**コピー アクティビティ**用の入力および出力として使用されます。
[手順 4:パイプラインを作成して実行する](#CreateAndRunAPipeline) | この手順では、**ADFTutorialPipeline** という名前のパイプラインを作成します。このパイプラインには、Azure BLOB から Azure データベース出力テーブルにデータをコピーする**コピー アクティビティ**があります。
[手順 5:データ セットとパイプラインを監視する](#MonitorDataSetsAndPipeline) | この手順では、Azure Management Studio を使用してデータセットとパイプラインを監視します。
 


## <a name="CreateDataFactory"></a>手順 1.Azure Data Factory を作成する
この手順では、Azure プレビュー ポータルを使用して、**ADFTutorialDataFactory** という名前の Azure Data Factory を作成します。

1.	[Azure プレビュー ポータル][azure-preview-portal]にログインしたら、左下隅の **[新規]** をクリックし、**[新規]** ブレードの **[Data Factory]** をクリックします。 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	**[Data Factory]** が **[新規]** ブレードに表示されていない場合は、下にスクロールします。  


6. **[新しい Data Factory]** ブレードで以下を実行します。
	1. **[名前]** に「**ADFTutorialDataFactory**」と入力します。 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. **[リソース グループ名]** をクリックし、以下を実行します。
		1. **[新しいリソース グループを作成]** をクリックします。
		2. **[リソース グループを作成]** ブレードで、リソース グループの **[名前]** に「**ADFTutorialResourceGroup**」と入力し、**[OK]** をクリックします。 

			![Create Resource Group][image-data-factory-create-resource-group]

		このチュートリアルの一部の手順は、**ADFTutorialResourceGroup** という名前のリソース グループを使用することを前提としています。異なるリソース グループを使用する場合は、ADFTutorialResourceGroup の代わりにここで選択したリソース グループを使用する必要があります。  
7. **[新しい Data Factory]** ブレードで、**[スタート画面に追加する]** が選択されていることを確認してください。
8. **[新しい Data Factory]** ブレードで **[作成]** をクリックします。

	> [WACOM.NOTE] Azure Data Factor の名前はグローバルに一意にする必要があります。**"ADFTutorialDataFactory" という名前の Data Factory は使用できません**というエラーが発生した場合は、名前を変更します (yournameADFTutorialDataFactory など)。このチュートリアルの手順の実行中に、この名前を ADFTutorialFactory の代わりに使用します。  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. 左側の **[通知]** ハブをクリックし、作成プロセスからの通知を探します。
10. 作成が完了すると、次に示すような Data Factory ブレードが表示されます。
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. また、以下のように、**スタート画面**から **ADFTutorialFactory** をクリックして表示することもできます。 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>手順 2.リンクされたサービスの作成
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。データ ストアには、Azure ストレージ、Azure SQL データベース、または内部設置型の SQL Server データベースを指定できます。

この手順では、**MyBlobStore** と **MyAzureSQLStore** の 2 つのリンクされたサービスを作成します。リンクされたサービス MyBlobStore は Azure Storage Account を、MyAzureSQLStore は Azure SQL データベースを **ADFTutorialDataFactory** にリンクします。このチュートリアルの後半で、MyBlobStore 内の BLOB コンテナーから MyAzureSQLStore 内の SQL テーブルにデータをコピーするパイプラインを作成します。

### Azure ストレージ アカウント用にリンクされたサービスを作成する
1.	**[DATA FACTORY]** ブレードで、**[リンクされたサービス]** タイルをクリックして、**[リンクされたサービス]** ブレードを起動します。

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. **[リンクされたサービス]** ブレードで、**[データ ストアを追加]** をクリックします。

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. **[新しいデータ ストア]** ブレードで、以下を実行します。  
	1. データ ストアの **[名前]** を入力します。このチュートリアルでは、**名前**として「**MyBlobStore**」を入力します。
	2. データ ストアの **[説明]** を入力します (オプション)。
	3. **[種類]** をクリックします。
	4. **[Azure ストレージ アカウント]** を選択し、**[OK]** をクリックします。
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  **[新しいデータ ストア]** ブレードに戻ると、以下のように表示されます。

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. Azure ストレージ アカウントの **[アカウント名]** と **[アカウント キー]** を入力し、**[OK]** をクリックします。   

6. **[新しいデータ ストア]** ブレードで **[OK]** をクリックすると、**[リンクされたサービス]** ブレードの **[データ ストア]** の一覧に **[myblobstore]** が表示されます。(左側にある) **[通知]** ハブにメッセージがないか確認します。

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Azure SQL データベース用にリンクされたサービスを作成する
1. **[リンクされたサービス]** ブレードのツール バーで、**[データ ストアを追加]** をクリックし、別のデータ ソース (Azure SQL データベース) を追加します。
2. [新しいデータ ストア] ブレードで、以下を実行します。
	1. データ ストアの **[名前]** を入力します。このチュートリアルでは、**名前**として「**MyAzureSQLStore**」を入力します。 
	2. データ ストアの **[説明]** を入力します (オプション)。
	3. **[種類]** をクリックし、**[Azure SQL データベース]** を選択します。
3. Azure SQL データベースの **[サーバー]**、**[データベース]**、**[ユーザー名]**、および **[パスワード]** を入力し、**[OK]** をクリックします。

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. **[新しいデータ ストア]** ブレードで **[OK]** をクリックすると、**[リンクされたサービス]** ブレードに両方のストアが表示されます。

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>手順 3.入力テーブルと出力テーブルを作成する

前の手順で、**MyBlobStore** と **MyAzureSQLStore** というリンクされたサービスを作成し、Azure ストレージ アカウントと Azure SQL データベースを **ADFTutorialDataFactory** という Data Factory にリンクしました。この手順では、次の手順で作成するパイプラインのコピー アクティビティ用に入力データと出力データを表すテーブルを作成します。 

テーブルとは四角形のデータセットで、現在サポートされている唯一の種類のデータセットです。入力テーブルは MyBlobStore がポイントする Azure ストレージ内の BLOB コンテナーを参照し、出力テーブルは MyAzureSQLStore がポイントする Azure SQL データベース内の SQL テーブルを参照します。  
 
データセットとパイプラインの作成は、現時点では Azure プレビュー ポータルでサポートされていないため、Azure PowerShell コマンドレットを使用してテーブルとパイプラインを作成します。テーブルを作成する前に、まず次の操作を実行する必要があります (詳細な手順はリストの後にあります)。

* MyBlobStore がポイントする Azure BLOB ストレージ内に **adftutorial** という名前の BLOB コンテナーを作成する。 
* **emp.txt** という名前のテキスト ファイルを作成し、BLOB として **adftutorial** コンテナーにアップロードする。 
* MyAzureSQLStore がポイントする Azure SQL データベース内に **emp** という名前のテーブルを作成する。
* ハード ドライブ上に **ADFGetStarted** という名前のフォルダーを作成する。  

### Azure BLOB ストレージと Azure SQL データベースをチュートリアル用に準備する
1. メモ帳を起動し、次のテキストを貼り付け、**emp.txt** という名前でハード ドライブの **C:\ADFGetStarted** フォルダーに保存します。 

        John, Doe
		Jane, Doe
				
2. [Azure ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/)などのツールを使用して **adftutorial** コンテナーを作成し、このコンテナーに **emp.txt** ファイルをアップロードします。

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. 次の SQL スクリプトを使用して、**emp** テーブルを Azure SQL データベースに作成します。Azure SQL 管理コンソールを使用して、Azure SQL データベースに接続し、SQL スクリプトを実行できます。SQL Server Management Studio を使用してこのタスクを行うこともできます。 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	Azure SQL 管理コンソールを起動するには、次の図に示すように、**[管理]** をクリックします。
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### 入力テーブルの作成 
テーブルとは四角形のデータセットで、スキーマを持っています。この手順では、リンクされたサービス **MyBlobStore** が表す Azure ストレージ内の BLOB コンテナーをポイントする **EmpBlobTable** という名前のテーブルを作成します。


1. BLOB 内の emp.txt のデータを表す Data Factory テーブルの JSON ファイルを作成します。メモ帳を起動し、次の JSON スクリプトをコピーして貼り付け、EmpBlobTable.json という名前を付けて C:\ADFGetStarted フォルダーに保存します。


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
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitonexternal": {}
       		 	}
    		}
		}

		
     以下の点に注意してください。 
	
	- location (場所) の **type** (種類) が **AzureBlobLocation** (Azure BLOB の場所) に設定されています。
	- **linkedServiceName** (リンクされたサービス名) が **MyBlobStore** に設定されています(このリンクされたサービスは手順 2 で作成しました)。
	- **folderPath** が **adftutorial** コンテナーに設定されています。フォルダー内の BLOB の名前を指定することもできます。BLOB の名前を指定しない場合、コンテナー内のすべての BLOB からのデータが入力データと見なされます。  
	- format (形式) の **type** (種類) が **TextFormat** (テキスト形式) に設定されています。
	- - テキスト ファイル内に 2 つのフィールド (**FirstName** (名) と **LastName** (姓)) があり、コンマで区切られています (列の区切り記号)。	
	- **availability** (可用性) が **hourly** (時間単位) に設定されています (**frequency** (頻度) が **hour** (時間) に設定され、**interval** (間隔) が **1** に設定されています)。Data Factory サービスは、指定された BLOB コンテナー (**adftutorial**) 内のルート フォルダーに対して 1 時間ごとにデータを検索します。

	**入力テーブル**に **fileName** (ファイル名) を指定しない場合、input フォルダー (**folderPath**) 内のすべてのファイルまたは BLOB が入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。例については、[チュートリアル][adf-tutorial]のサンプル ファイルを参照してください。
 
	**出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されたファイルに次の形式で名前が付けられます。Data.<Guid>.txt (例::Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)

	**folderPath** と **fileName** を **SliceStart** 時刻に基づいて動的に設定するには、partitionedBy プロパティを使用します。次の例では、folderPath に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。たとえば、スライスが 2014-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2014/10/20 に設定され、fileName は 08.csv に設定されます。 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	JSON のプロパティの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)](http://go.microsoft.com/fwlink/?LinkId=516971)」を参照してください。

2. **Azure PowerShell** を起動し、以下のコマンドを実行して **AzureResourceManager** モードに切り替えます。**AzureResourceManager** モードでは Azure Data Factory コマンドレットが使用できます。

         switch-azuremode AzureResourceManager
		
	If you haven't already done so, do the following:


	- **Add-AzureAccount** を実行して、Azure プレビュー ポータルへのサインインに使用したものと同じユーザー名とパスワードを入力します。  
	- **Get-AzureSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
	- **Select-AzureSubscription** を実行して、使用するサブスクリプションを選択します。このサブスクリプションは、Azure プレビュー ポータルで使用したものと同じである必要があります。

3. **New-AzureDataFactoryTable** コマンドレットを実行して、**EmpBlobTable.json** ファイルを使用して入力テーブルを作成します。


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	このコマンドレットやその他の Data Factory コマンドレットの詳細については、[Data Factory コマンドレット リファレンス][cmdlet-reference]を参照してください。
 
### 出力テーブルの作成
手順のこの部分では、リンクされたサービス **MyAzureSQLStore** が表す Azure SQL データベース内の SQL テーブルをポイントする **EmpSQLTable** という名前の出力テーブルを作成します。 

1. Azure SQL データベース内のデータを表す Data Factory テーブルの JSON ファイルを作成します。メモ帳を起動し、次の JSON スクリプトをコピーして貼り付け、**EmpSQLTable.json** という名前を付けて **C:\ADFGetStarted** フォルダーに保存します。



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
            		"type": "AzureSQLTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     以下の点に注意してください。 
	
	* location (場所) の **type** (種類) が **AzureSQLTableLocation** (Azure SQL テーブルの場所) に設定されています。
	* **linkedServiceName** (リンクされたサービス名) が **MyAzureSQLStore** に設定されています (このリンクされたサービスは手順 2 で作成しました)。
	* **tablename** (テーブル名) が **emp** に設定されています。
	* データベース内の emp テーブルに 3 つの列 (**ID**、**FirstName** (名)、および **LastName** (姓)) がありますが、ID は識別子の列であるため、ここでは **FirstName** と **LastName** のみを指定する必要があります。
	* **availability** (可用性) が **hourly** (時間単位) に設定されています (frequency (頻度) が hour (時間) に設定され、interval (間隔) が 1 に設定されています)。Data Factory サービスは Azure SQL データベース内の **emp** テーブルに 1 時間ごとに出力データ スライスを生成します。


2. **Azure PowerShell** で、次のコマンドを実行し、Azure SQL データベース内の **emp** テーブルを表す別の Data Factory テーブルを作成します。



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>手順 4.パイプラインを作成して実行する
この手順では、**EmpTableFromBlob** を入力、**EmpSQLTable** を出力として使用し、**コピー アクティビティ**を持つパイプラインを作成します。

1. パイプラインの JSON ファイルを作成します。メモ帳を起動し、次の JSON スクリプトをコピーして貼り付け、**ADFTutorialPipeline.json** という名前を付けて **C:\ADFGetStarted** フォルダーに保存します。


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
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
        		]
      		}
		} 

	以下の点に注意してください。

	- activities セクションに、**type** (種類) が **CopyActivity** (コピー アクティビティ) に設定されたアクティビティが 1 つだけあります。
	- アクティビティの入力は **EmpTableFromBlob** に設定され、アクティビティの出力は **EmpSQLTable** に設定されています。
	- **transformation** (変換) セクションでは、ソースの種類として **BlobSource** (BLOB ソース) が指定され、シンクの種類として **SqlSink** (SQL シンク) が指定されています。

2. **New-AzureDataFactoryPipeline** コマンドレットを実行し、作成した **ADFTutorialPipeline.json** ファイルを使用してパイプラインを作成します。



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. パイプラインを作成したら、データ処理を実行する期間を指定できます。パイプラインの有効期間を指定することで、各 Azure Data Factory テーブルに対して定義された Availability プロパティに基づいてデータ スライスが処理される期間を定義します。次の PowerShell コマンドを実行してパイプラインに有効期間を設定し、Y を入力して確定します。 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] **StartDateTime** の値を現在の日付に置き換え、**EndDateTime** の値を翌日の日付に置き換えます。StartDateTime と EndDateTime はいずれも UTC 時間で、(http://en.wikipedia.org/wiki/ISO_8601)[ISO 形式]である必要があります。次に例を示します。2014-10-14T16:32:41Z。**EndDateTime** は任意ですが、このチュートリアルでは使用します。 
	**EndDateTime** を指定しない場合は、"**StartDateTime + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9/9/9999** を **EndDateTime** として指定します。  
	
	上記の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。

4. **Azure ポータル**の **ADFTutorialDataFactory** の **[DATA FACTORY]** ブレードで、**[ダイアグラム]** をクリックします。

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. 以下のような図が表示されるはずです。(タイトルをダブルクリックすると、ブレードを表すアーティファクトの詳細が表示されます)。

	![Diagram view][image-data-factory-get-started-diagram-blade]

**ご利用ありがとうございます。** これで、Azure Data Factory、リンクされたサービス、テーブル、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。

## <a name="MonitorDataSetsAndPipeline"></a>手順 5.データセットとパイプラインを監視する
このステップでは、Azure ポータルを使用して、Azure Data Factory の状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。監視用のコマンドレットの使用方法については、「[Azure PowerShell を使用した Azure Data Factory の監視と管理][monitor-manage-using-powershell]」を参照してください。

1. まだ開いていない場合は、[Azure ポータル (プレビュー)][azure-preview-portal] に移動します。 
2. **ADFTutorialDataFactory** のブレードが開いていない場合は、**スタート画面**で **ADFTutorialDataFactory** をクリックして開きます。 
3. 作成したテーブルの数と名前、およびパイプラインの名前がブレードに表示されます。

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. **[データセット]** タイルをクリックします。
5. **[データセット]** ブレードで、**[EmpTableFromBlob]** をクリックします。これは、**ADFTutorialPipeline** 用の入力テーブルです。

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. 現在の時刻までのデータ スライスが既に生成されており、**準備完了**になっています。これは、**emp.txt** ファイルが BLOB コンテナー  **adftutorial\input** 内に常に存在しているためです。下部の **[問題のあるスライス]** セクションにスライスが表示されていないことを確認します。
6. **[データセット]** ブレードで、**[EmpSQLTable]** をクリックします。これは、**ADFTutorialPipeline** 用の出力テーブルです。

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. **[EmpSQLTable]** ブレードが次のように表示されます。

	![table blade][image-data-factory-get-started-table-blade]
 
7. 現在の時刻までのデータ スライスが既に生成されており、**準備完了**になっています。下部の **[問題のあるスライス]** セクションにはスライスが表示されていません。
8. **[...] (省略記号)** をクリックし、すべてのスライスを表示します。

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. 一覧のうち任意のデータ スライスをクリックすると、**[データ スライス]** ブレードが表示されます。

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. **[データ スライス]** ブレードの下部にある一覧に、すべてのアクティビティの実行状況が表示されます。**[アクティビティの実行状況]** をクリックすると、**[アクティビティの実行状況の詳細]** ブレードが表示されます。 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. **[X]** をクリックすると、すべてのブレードが閉じられ、**ADFTutorialDataFactory** のホーム ブレードに戻ります。
14. (省略可能) **ADFTutorialDataFactory** のホーム ページで **[パイプライン]** をクリックし、**[パイプライン]** ブレードの **[ADFTutorialPipeline]** をクリックし、入力テーブル (**Consumed**) または出力テーブル (**Produced**) を表示します。
15. **SQL Server Management Studio** を起動し、Azure SQL データベースに接続して、データベース内の **emp** テーブルに行が挿入されていることを確認します。

	![sql query results][image-data-factory-get-started-sql-query-results]


## まとめ 
このチュートリアルでは、Azure Data Factory を作成し、Azure BLOB から Azure SQL データベースにデータをコピーしました。また、Azure プレビュー ポータルを使用して、Data Factory とリンクされたサービスを作成しました。さらに、Azure PowerShell コマンドレットを使用して、テーブルとパイプラインを作成し、パイプラインにスケジュールを設定しました。以下は、このチュートリアルで実行した手順の概要です。  

1.	Azure **Data Factory** を作成します。
2.	データ ストアとコンピューティングを Data Factory に**リンクするサービス** (**リンクされたサービス**と呼びます) を作成します。
3.	パイプラインの入力データと出力データを記述した**テーブル**を作成します。
4.	**パイプライン**を作成します。パイプラインは、入力を処理し、出力を生成する 1 つ以上のアクティビティで構成されます。 
5.	パイプラインの**有効期間**を (データ処理用に) 指定します。有効期間は、データ スライスが生成される期間を定義します。

## 次のステップ

記事 | 説明
------ | ---------------
[チュートリアル:Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する**現実のシナリオ**の実行方法について、**詳細なチュートリアル**が記載されています。
[Azure Data Factory を使用してデータをコピーする - コピー アクティビティ][copy-activity] | この記事には、本チュートリアルで使用した**コピー アクティビティ**の詳細な説明が記載されています。
[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources] | この記事には、**内部設置型の SQL Server データベース**から Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[Data Factory で Pig と Hive を使用する][use-pig-and-hive-with-data-factory] | この記事には、**HDInsight アクティビティ**を使用して **hive/pig** スクリプトを実行し、入力データを処理して出力データを生成する方法を説明したチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、**カスタム アクティビティ**を作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。
[PowerShell コマンドレットを使用した Data Factory の監視と管理][monitor-manage-using-powershell] | この記事では、**Azure PowerShell コマンドレット**を使用して Azure Data Factory の**監視と管理**を実行する方法について説明しています。記事に含まれている例を ADFTutorialDataFactory で試すことができます。
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題の**トラブルシューティング**を行う方法について説明しています。エラーを発生させて (Azure SQL データベースのテーブルを削除する)、ADFTutorialDataFactory でこの記事のチュートリアルを試すことができます。 
[Azure Data Factory Cmdlet Reference (Azure Data Factory コマンドレット リファレンス)][cmdlet-reference] | このリファレンスには、すべての **Data Factory コマンドレット**に関する詳細が記載されています。
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/ja-jp/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ja-jp/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ja-jp/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!--HONumber=35.2-->
