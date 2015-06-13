<properties 
	pageTitle="チュートリアル: を作成して、Azure PowerShell を使用して、Azure データ工場出荷時の監視" 
	description="Azure PowerShell を使用して作成および Azure のデータのファクトリを監視する方法について説明します。" 
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
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# チュートリアル: を作成して、Azure PowerShell を使用して、データの出荷時の監視
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

 [Azure のデータのファクトリを使い始める][adf-get-started] チュートリアルでは、方法を使用して、Azure データ工場出荷時の監視を作成、 [Azure プレビュー ポータル][azure-preview-portal]です。このチュートリアルでは、作成を Azure PowerShell コマンドレットを使用して、Azure のデータのファクトリを監視することができます。このチュートリアルで作成するデータの出荷時に、パイプライン、Azure からデータをコピーする Azure SQL データベースへの blob です。

> [AZURE.NOTE]この記事では、すべてのデータのファクトリ コマンドレットは含まれません。参照してください [データ工場出荷時のコマンドレット リファレンス][cmdlet-reference] データ工場出荷時のコマンドレットの包括的なドキュメントです。

##前提条件
前提条件のチュートリアル トピックに示されているとは別には、コンピューターにインストールされている Azure PowerShell が必要です。ダウンロードしてインストールする必要はありませんが既に場合、 [Azure PowerShell][download-azure-powershell] コンピューターにします。

##このチュートリアルの内容
次の表は、このチュートリアルとその説明の一部として実行する手順を示します。

手順 | 説明
-----| -----------
[手順 1: Azure のデータのファクトリを作成します。](#CreateDataFactory) | このステップでは、Azure のデータのファクトリという名前を作成します **ADFTutorialDataFactoryPSH**です。 
[手順 2: 関連付けられているサービスを作成します。](#CreateLinkedServices) | このステップでは 2 つの関連付けられているサービスを作成します。 **StorageLinkedService** と **AzureSqlLinkedService**です。StorageLinkedService、Azure のストレージをリンクし、[AzureSqlLinkedService、ADFTutorialDataFactoryPSH に、Azure SQL データベースをリンクします。
[手順 3: 入力を作成し、出力データセット](#CreateInputAndOutputDataSets) | このステップでは 2 つのデータセットを定義します (* * EmpTableFromBlob * * と **EmpSQLTable**) の入力呼び出し力のテーブルとして使用されている、 **コピー アクティビティ** で次の手順で作成する ADFTutorialPipeline です。
[手順 4: を作成して、パイプラインの実行](#CreateAndRunAPipeline) | このステップでは、パイプラインという名前を作成します **ADFTutorialPipeline** データ ファクトリで: **ADFTutorialDataFactoryPSH**.パイプラインには、 **コピー アクティビティ** 、Azure からのデータをコピーする blob を出力の Azure のデータベース テーブルにします。
[手順 5: データ セットとパイプラインを監視します。](#MonitorDataSetsAndPipeline) | このステップでは、データセットと Azure PowerShell を使用して、このステップでは、パイプラインを監視します。

## <a name="CreateDataFactory"></a>手順 1: Azure のデータのファクトリを作成します。
このステップで、Azure のデータのファクトリという名前の作成に、Azure PowerShell を使用する **ADFTutorialDataFactoryPSH**です。

1. 起動 **Azure PowerShell** と、次のコマンドを実行します。Azure PowerShell には、このチュートリアルの末尾までが開いたままです。終了して再起動した場合は、これらのコマンドを再度実行する必要があります。
	- 実行 **Add-azureaccount** 、ユーザー名と、Azure プレビュー ポータルにサインインに使用するパスワードを入力します。  
	- 実行 **Get-azuresubscription** をこのアカウントのすべてのサブスクリプションを表示します。
	- 実行 **Select-azuresubscription** を使用するサブスクリプションを選択します。このサブスクリプションは、Azure プレビュー ポータルで使用したものと同じである必要があります。 
2. 切り替える **AzureResourceManager** モードとして、Azure のデータのファクトリ コマンドレットはこのモードで使用できます。

		Switch-AzureMode AzureResourceManager 
3. という名前の Azure リソース グループを作成します。 **ADFTutorialResourceGroup** 、次のコマンドを実行します。
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	という名前のリソース グループを使用することを前提とこのチュートリアルの手順も **ADFTutorialResourceGroup**です。異なるリソース グループを使用する場合は、このチュートリアルで ADFTutorialResourceGroup の代わりに使用する必要があります。 
4. 実行、 **新規 AzureDataFactory** という名前のデータのファクトリを作成するコマンドレット: **ADFTutorialDataFactoryPSH**です。  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]Azure Data Factor の名前はグローバルに一意にする必要があります。エラーが発生する場合: **データ工場出荷時の名前"ADFTutorialDataFactoryPSH"は使用できません**, 、(たとえば、yournameADFTutorialDataFactoryPSH) の名前を変更します。このチュートリアルの手順を実行中に ADFTutorialFactoryPSH の代わりに、この名前を使用します。

## <a name="CreateLinkedServices"></a>手順 2: 関連付けられているサービスを作成します。
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。データ ストアには、Azure のストレージ、Azure SQL データベース、または入力データが含まれています。 または、データの工場出荷時のパイプラインの出力データを格納する内部設置型 SQL Server データベースを指定できます。コンピューティング サービスは、入力データを処理し、出力データを生成するサービスです。

このステップでは 2 つの関連付けられているサービスを作成します。 **StorageLinkedService** と **AzureSqlLinkedService**です。Azure のストレージ アカウントにサービスへのリンクをリンクする StorageLinkedService され AzureSqlLinkedService、データの工場出荷時に、Azure SQL データベースをリンクする: **ADFTutorialDataFactoryPSH**です。StorageLinkedService 内の blob コンテナーから AzureSqlLinkedService 内の SQL テーブルにデータをコピーするこのチュートリアルの後半では、パイプラインは作成します。

### Azure ストレージ アカウント用にリンクされたサービスを作成する
1.	という名前の JSON ファイルを作成する **StorageLinkedService.json** で、 **C:\ADFGetStartedPSH** を次の内容です。既に存在しない場合は、ADFGetStartedPSH のフォルダーを作成します。

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	 **Azure PowerShell**, に切り替え、 **ADFGetStartedPSH** フォルダーです。 
3.	使用することができます、 **新規 AzureDataFactoryLinkedService** コマンドレットをリンクされているサービスを作成します。このコマンドレットと、このチュートリアルで使用する他のデータの工場出荷時のコマンドレット値を渡す必要が、 **ResourceGroupName** と **DataFactoryName** パラメーター。使用する代わりに、 **Get AzureDataFactory** コマンドレットを実行する、DataFactory のオブジェクトを取得し、毎回 ResourceGroupName と DataFactoryName 入力することがなく、オブジェクトを渡します。出力を割り当てるには、次のコマンドを実行する、 **Get AzureDataFactory** コマンドレットでは、変数を: **$df**です。 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	これで、実行、 **新規 AzureDataFactoryLinkedService** コマンドレットでは、リンクされているサービスの作成を: **StorageLinkedService**です。

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	おらず、実行する場合、 **Get AzureDataFactory** コマンドレットへの出力の割り当てと **$df** ResourceGroupName と DataFactoryName パラメーターの値を次のように指定する必要が変数です。
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	チュートリアルの途中では、Azure PowerShell を閉じた場合がコマンドレットを実行する Get AzureDataFactory 次回、チュートリアルを完了するには、Azure PowerShell を起動したとき。

### Azure SQL データベース用にリンクされたサービスを作成する
1.	次の内容の AzureSqlLinkedService.json という名前の JSON ファイルを作成します。

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	リンクされているサービスを作成するには、次のコマンドを実行します。 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]いることを確認、 **Azure サービスにアクセスできるように** 、Azure の SQL server で設定がオンにします。確認を有効には、次の手順で行います。
	>
	> <ol>
<li>クリックして <b>参照</b> ハブをクリックして、左に <b>SQL server</b>です。</li>
<li>サーバーを選択してクリックして <b>設定</b> 上、 <b>SQL SERVER</b> ブレードします。</li>
<li> <b>設定</b> ブレードでクリックして <b>ファイアウォール</b>です。</li>
<li> <b>Firewalll 設定</b> ブレードで] をクリックして <b>ON</b> の <b>Azure サービスにアクセスできるように</b>です。</li>
<li>クリックして <b>ACTIVE</b> に切り替えるには、左側のハブ、 <b>データ ファクトリ</b> ブレード上にいます。</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>手順 3: 入力を作成し、テーブルの出力

関連付けられているサービスを作成した前の手順で **StorageLinkedService** と **AzureSqlLinkedService** 、データの工場出荷時に、Azure のストレージ アカウントと Azure SQL データベースをリンクする: **ADFTutorialDataFactoryPSH**です。このステップでは、入力を表し、パイプラインの次の手順で作成するのには、コピー アクティビティのデータを出力するテーブルを作成します。

テーブルとは四角形のデータセットで、現在サポートされている唯一の種類のデータセットです。ポインターを StorageLinkedService の Azure ストレージ内の blob コンテナーをこのチュートリアルでは、入力のテーブルを参照し、[AzureSqlLinkedService をポイントする、Azure SQL データベースには、SQL テーブルに、出力テーブルを参照します。

### Azure BLOB ストレージと Azure SQL データベースをチュートリアル用に準備する
チュートリアルを終了した場合は、この手順をスキップ [Azure のデータのファクトリを使い始める][adf-get-started] 記事です。

このチュートリアルでは、Azure blob ストレージと Azure SQL データベースを準備するには、次の手順を実行する必要があります。
 
* という名前の blob コンテナーを作成する **adftutorial** 、Azure で blob ストレージを **StorageLinkedService** をポイントします。 
* 作成して、テキスト ファイルをアップロード **emp.txt**, に blob として、 **adftutorial** コンテナーです。 
* という名前のテーブルを作成する **emp** 、Azure SQL データベース、Azure sql データベースを **AzureSqlLinkedService** をポイントします。


1. メモ帳を起動する、次のテキストを貼り付け、そのとして保存 **emp.txt** に **C:\ADFGetStartedPSH** 、ハード ドライブ上のフォルダーです。 

        John, Doe
		Jane, Doe
				
2. などのツールを使用して [Azure ストレージ エクスプ ローラー](https://azurestorageexplorer.codeplex.com/) を作成する、 **adftutorial** コンテナーにアップロードして、 **emp.txt** コンテナーにファイルです。

    ![Azure ストレージ エクスプローラー][image-data-factory-get-started-storage-explorer]
3. 作成するには、次の SQL スクリプトを使用して、 **emp** Azure SQL データベース内のテーブルです。  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	SQL Server 2014 のコンピューターにインストールされている必要があるかどうか: からの指示に従います [手順 2: 管理する Azure SQL データベースの SQL Server Management Studio を使用して SQL データベースへの接続][sql-management-studio] 記事では、Azure の SQL server に接続し、SQL スクリプトを実行します。

	Visual Studio 2013 がコンピューターにインストールされている必要があるかどうか: Azure プレビュー ポータルで ([http://portal.azure.com](http://portal.sazure.com))、] をクリックして **参照** ハブで、左クリック **SQL サーバー**, 、データベースを選択し、 **Visual Studio で開いて** ツールバーは、Azure の SQL server に接続し、スクリプトを実行するのです。Azure の SQL server へのアクセスに、クライアントが許可されない場合は、コンピューター (IP アドレス) からのアクセスを許可するには、Azure の SQL server 用のファイアウォールを構成する必要があります。Azure の SQL server のファイアウォールを構成する手順については、上の記事を参照してください。
		
### 入力テーブルの作成 
テーブルとは四角形のデータセットで、スキーマを持っています。このステップではという名前のテーブルを作成します **EmpBlobTable** によって表される、Azure ストレージ内の blob コンテナーを指す、 **StorageLinkedService** サービスにリンクします。この blob コンテナー (* * * * adftutorial)、ファイル内の入力データが含まれています。 **emp.txt**です。

1.	という名前の JSON ファイルを作成する **EmpBlobTable.json** で、 **C:\ADFGetStartedPSH** フォルダーを次の内容。

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
	                "waitonexternal": {}
	                }
	        }
		}
	
	以下の点に注意してください。
	
	- 場所 **型** に設定されている **AzureBlobLocation**です。
	- **linkedServiceName** に設定されている **StorageLinkedService**です。 
	- **folderPath** に設定されている、 **adftutorial** コンテナーです。フォルダー内の BLOB の名前を指定することもできます。BLOB の名前を指定しない場合、コンテナー内のすべての BLOB からのデータが入力データと見なされます。  
	- 形式 **型** に設定されている **TextFormat**
	- 2 つのフィールドは、テキスト ファイル – **FirstName** と **LastName** 、コンマで区切られた – (* * * * columnDelimiter)	
	-  **可用性** に設定されている **1 時間ごと** (* * * * 頻度に設定されている **時間** と **間隔** に設定されている **1** )、データのファクトリのサービスになります入力データの 1 時間ごと、blob コンテナー内のルート フォルダーにあるため、(* * adftutorial * *) を指定します。

	指定しない場合、 **ファイル名** の **入力** **テーブル**, 、入力フォルダーからすべてのファイルと blob (* * * * folderPath) の入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。サンプル ファイルを参照してください、 [チュートリアル][adf-tutorial] 例についてはします。
 
	指定しない場合、 **ファイル名** の **出力テーブル**, 、内のファイルの生成、 **folderPath** 、次の形式で名前が指定: データです。 < Guid > .txt (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt。)。

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

2.	出荷時のデータ テーブルを作成するには、次のコマンドを実行します。

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### 出力テーブルの作成
という名前の出力テーブルを作成する、ステップのこのパートで **EmpSQLTable** SQL テーブルを指す (* * emp * *) で表される Azure SQL データベースで、 **AzureSqlLinkedService** サービスにリンクします。パイプライン入力を blob からのデータのコピー、 **emp** テーブルです。

1.	という名前の JSON ファイルを作成する **EmpSQLTable.json** で、 **C:\ADFGetStartedPSH** フォルダーを次の内容です。
		
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
	* **linkedServiceName** に設定されている **AzureSqlLinkedService**です。
	* **tablename** に設定されている **emp**です。
	* – 3 つの列がある **ID**, 、**FirstName**, と **LastName** –、データベース内には、emp テーブル内のみを指定する必要があるために、ID が id 列が **FirstName** と **LastName** ここです。
	*  **可用性** に設定されている **1 時間ごと** (* * * * 頻度に設定 **時間** と **間隔** に設定 **1**)。データのファクトリのサービスがで 1 時間ごとに、出力データのスライスの生成は、 **emp** 、Azure SQL データベースのテーブルにします。

2.	出荷時のデータ テーブルを作成するには、次のコマンドを実行します。
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>手順 4: を作成して、パイプラインの実行
このステップでパイプラインを作成する、 **コピー アクティビティ** を使用して **EmpTableFromBlob** の入力としてと **EmpSQLTable** 出力として。

1.	という名前の JSON ファイルを作成する **ADFTutorialPipeline.json** で、 **C:\ADFGetStartedPSH** フォルダーを次の内容。 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
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
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	以下の点に注意してください。

	- セクションでは、アクティビティ、アクティビティの 1 つだけが持つ **型** に設定されている **CopyActivity**です。
	- 入力に設定されているアクティビティ **EmpTableFromBlob** に設定されているアクティビティの出力と **EmpSQLTable**です。
	-  **変換** セクションで、 **BlobSource** がソースの種類として指定されていると **SqlSink** は、シンクの種類として指定します。

	> [AZURE.NOTE]値を置き換える、 **開始** 、現在の日付を持つプロパティと **終了** で、次の日の値です。これら両方の開始し、終了の datetimes である必要があります [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)です。例: 2014年-10-14T16:32:41Z です。 **終了** 時刻は省略可能では、このチュートリアルで使用します。値を指定しない場合、 **終了** として計算されます。 プロパティを"* * スタート + 48 時間 * *"です。パイプラインを無期限に実行する次のように指定します。 **9/9/9999** の値として、 **終了** プロパティです。上記の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。
	
	> [JSON のスクリプト参照](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	出荷時のデータ テーブルを作成するには、次のコマンドを実行します。 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**ご利用ありがとうございます。** これで、Azure Data Factory、リンクされたサービス、テーブル、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。

## <a name="MonitorDataSetsAndPipeline"></a>手順 5: データセットとパイプラインを監視します。
この手順では、Azure PowerShell を使用して、Azure のデータのファクトリで何が起こってを監視します。

1.	実行 **Get AzureDataFactory** $df 変数に出力を割り当てるとします。

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	実行 **Get AzureDataFactorySlice** のすべてのスライスの詳細を表示する、 **EmpSQLTable**, 、これは、パイプラインの出力テーブルです。

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]年、月、およびの日付の部分を置き換える、 **StartDateTime** 、現在の年、月、および日付を持つパラメーター。これが一致している、 **開始** JSON のパイプライン内の値です。

	24 個のスライスが、次の日の 12 AM に、現在の日付の午前から 1 時間ごとに 1 つが表示されます。
	
	**最初の項目。**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**最後にスライスします。**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	実行 **Get AzureDataFactoryRun** のアクティビティの実行の詳細を取得するには、 **特定** スライスします。値を変更、 **StartDateTime** と一致するパラメーター、 **開始** 上記の出力からのスライスの時間です。値、 **StartDateTime** である必要があります [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)です。例: 2014年-03-03T22:00:00Z です。

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	次のような出力が表示されます。

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

> [AZURE.NOTE]参照してください [データ工場出荷時のコマンドレット リファレンス][cmdlet-reference] データ工場出荷時のコマンドレットの包括的なドキュメントです。

## 次のステップ

記事 | 説明
------ | ---------------
[Azure のデータのファクトリのコピーのアクティビティにデータのコピー][copy-activity] | この記事の説明の詳細については、 **コピー アクティビティ** このチュートリアルで使用します。 
[内部設置型データを使用するパイプラインを有効にします。][use-onpremises-datasources] | この資料は、データをコピーする方法について説明するチュートリアル、 **内部設置型 SQL Server データベース** Azure blob にします。 
[Pig の使用、およびデータのファクトリでの Hive][use-pig-and-hive-with-data-factory] | この資料は、使用する方法について説明するチュートリアル **HDInsight アクティビティ** を実行する、 **hive と pig** 出力データを生成する入力データを処理するスクリプトです。
[チュートリアル: 移動し、データのファクトリを使用してログ ファイルの処理][adf-tutorial] | この記事では、 **エンド ツー エンド チュートリアル** を実装する方法を示す、 **実際のシナリオ** insights にログ ファイルからデータを変換する Azure のデータのファクトリを使用します。
[データのファクトリでカスタム アクティビティを使用します。][use-custom-activities] | この記事を作成するための手順を使用して、チュートリアルでは、 **カスタム アクティビティ** と、パイプラインで使用します。 
[データの工場出荷時の問題をトラブルシューティングします。][troubleshoot] | この資料で説明する方法 **のトラブルシューティングを行う** Azure のデータのファクトリを発行します。エラーを発生させて (Azure SQL データベースのテーブルを削除する)、ADFTutorialDataFactory でこの記事のチュートリアルを試すことができます。 
[Azure のデータの工場出荷時のコマンドレット リファレンス][cmdlet-reference] | このリファレンス コンテンツがすべての詳細、 **データ ファクトリ コマンドレット**です。
[Azure のデータの工場出荷時の開発者向けリファレンス][developer-reference] | コマンドレット、JSON スクリプト、関数などの包括的な参照の内容を開発者向けリファレンスには. 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: ../storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-create-sql-database]: ../sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database-manage-azure-ssms.md#Step2

<!---HONumber=GIT-SubDir--> 