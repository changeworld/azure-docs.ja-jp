<properties title="Enable your pipelines to work with on-premises data" pageTitle="パイプラインが内部設置型のデータを扱えるようにする | Azure Data Factory" description="Learn how to register an on-premises data source with an Azure data factory and copy data to/from the data source." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# パイプラインが内部設置型のデータを扱えるようにする

Azure データ ファクトリ内のパイプラインが内部設置型のデータを扱えるようにするには、Azure 管理ポータルまたは Azure PowerShell を使用し、内部設置型のデータ ソースをリンクされたサービスとしてデータ ファクトリに追加する必要があります。
 
内部設置型のデータ ソースをリンクされたサービスとしてデータ ファクトリに追加できるようにするには、まず Microsoft Data Management Gateway をダウンロードして内部設置型のコンピューターにインストールし、内部設置型のデータ ソースがゲートウェイを使用できるようにリンクされたサービスを構成する必要があります。
 
> [WACOM.NOTE] 現時点で内部設置型のデータ ソースをサポートしているのは SQL Server のみです。

## <a href="DMG"></a> Data Management Gateway

**Data Management Gateway** は、安全かつ管理された方法で内部設置型のデータをクラウド サービスに接続するソフトウェアです。Data Management Gateway を使用すると、以下のことを行えます。

- **ハイブリッド データ アクセスのために内部設置型のデータに接続する** - 内部設置型のデータで業務を継続しながら、そのデータをクラウド サービスに接続することでクラウド サービスから恩恵が受けられます。
- **安全なデータ プロキシを定義する** - ゲートウェイがクラウド サービスからのデータ要求を認証して内部設置型のデータ ソースを保護できるよう、どの内部設置型のデータ ソースを公開するかを Data Management Gateway で定義できます。
- **徹底したガバナンスのためにゲートウェイを管理する** - 管理とガバナンスのために Data Management Gateway 内部のあらゆるアクティビティを十分に監視し詳細に記録できます。
- **データを効率的に移動** - データ転送は並列で行われ、断続的なネットワークの問題に対する回復が早い自動再試行ロジックを採用しています。


Data Management Gateway が持つ幅広い内部設置型のデータ接続能力には、以下が含まれます。

- **企業のファイアウォールに影響を与えない** - Data Management Gateway は、ファイアウォール接続を始めることも会社のネットワーク インフラに侵入性の変更を要求することもなく、インストール後すぐに機能します。
- **証明書で資格情報を暗号化** - データ ソースへの接続に使用される資格情報はユーザーが完全に所有する証明書で暗号化されます。証明書を持たなければ、資格情報をプレーン テキストへと復号することは Microsoft を含めだれにもできません。

### ゲートウェイのインストール - ベスト プラクティス

1.	ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答できません。そのため、ゲートウェイをインストールする前に、コンピューターの適切な**電源プラン**を構成します。 
2.	Data Management Gateway は、Azure Data Factory サービスを使用して登録される内部設置型のデータ ソースには接続できるはずです。データ ソースと同じコンピューター上にインストールする必要はありませんが、**データ ソースが近くにあれば**、ゲートウェイがデータ ソースに接続するためにかかる時間は短くなります。

### Data Management Gateway を使用する上で考慮すること
1.	Data Management Gateway では 1 つのインスタンスを複数の内部設置型データ ソースに使用できますが、**ゲートウェイは Azure データ ファクトリと 1 対 1 で結び付いており**、他のデータ ファクトリとの共有はできないということを覚えておいてください。
2.	コンピューターには **Data Management Gateway のインスタンスを 1 つだけ**インストールできます。例えば、内部設置型のデータ ソースにアクセスする必要があるデータ ファクトリが 2 つあるとすると、内部設置型のコンピューター 2 台にゲートウェイをインストールし、各ゲートウェイを別々のデータ ファクトリに結び付ける必要があります。
3.	コンピューターに**Power BI** のシナリオを扱うゲートウェイがすでにインストールされている場合は、**Azure Data Factory 用のゲートウェイを**他のコンピューターにインストールしてください。 
 

## チュートリアル

このチュートリアルでは、内部設置型の SQL Server データベースから、Azure BLOB にデータを移動するパイプラインを備えたデータ ファクトリを作成します。 

### 手順 1.Azure データ ファクトリの作成
この手順では、Azure 管理ポータルを使用して **ADFTutorialOnPremDF** という名前の Azure Data Factory インスタンスを作成します。Azure Data Factory のコマンドレットを使用してデータ ファクトリを作成することもできます。 

1.	[Azure プレビュー ポータル][へのログイン後
2.	azure-preview-portal]、左下隅の **[新規]** をクリックし、**[新規]** ブレードの **[データ ファクトリ]** をクリックします。

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	**[Data Factory]** が **[新規]** ブレードに表示されていない場合は、下にスクロールします。  
6. **[新しい Data Factory]** ブレードで以下を実行します。
	1. **[名前]** に「**ADFTutorialOnPremDF**」と入力します。
	2. **[リソース グループ名]** をクリックし、**[ADFTutorialResourceGroup]** を選択します (「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルを終えている場合)。既存のリソース グループを選択するか、新しく作成することができます。新しいリソース グループを作成するには:
		1. **[新しいリソース グループを作成]** をクリックします。
		2. **[リソース グループを作成する] ブレード**で、リソース グループの**名前**を入力し、**[OK]** をクリックします。

7. **[新しいデータ ファクトリ]** ブレードの **[スタート画面に追加]** がオンになっていることに注意してください。

	![Add to Startboard][image-data-factory-add-to-startboard]

8. **[新しいデータ ファクトリ]** ブレードで、**[作成]** をクリックします。
9. 左側の **[通知]** ハブで作成プロセスからの通知を探します。

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. 作成が完了すると、次のような [データ ファクトリ] ブレードが表示されます。

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. 以下のように、**スタート画面**にも表示されるはずです。まだ開いていなければ、クリックして **[データ ファクトリ] ブレード**を開きます。

	![Startboard][image-data-factory-startboard]

### 手順 2.リンクされたサービスの作成 
このステップでは、2 つのリンクされたサービスを作成します。**MyBlobStore** と **OnPremSqlLinkedService** です。**OnPremSqlLinkedService** は内部設置型の SQL Server データベースをリンクし、リンクされたサービス **MyBlobStore** は Azure BLOB ストアを **ADFTutorialDataFactory** にリンクします。このチュートリアルの後半で、内部設置型の SQL Server データベースから Azure BLOB ストアにデータをコピーするパイプラインを作成します。 

### 内部設置型の SQL Server データベースにリンクされたサービスを追加する
1.	**[ADFTutorialOnPremDF]** の **[データ ファクトリ]** ブレードで、**[リンクされたサービス]** をクリックします。 

	![Data Factory Home Page][image-data-factory-home-age]

2.	**[リンクされたサービス]** ブレードで、**[データ ゲートウェイを追加]** をクリックします。

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. **[作成]** ブレードで、**[名前]** に「**adftutorialgateway**」と入力し、**[OK]** をクリックします。 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. **[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。これにより、ゲートウェイのインストール パッケージのダウンロードと、コンピューターへのインストール、構成、および登録が行われます。

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	これは、たった 1 つの手順 (クリック 1 回) でゲートウェイのダウンロード、インストール、構成、および登録を行う、最も簡単な方法です。**Microsoft Data Management Gateway Configuration Manager** アプリケーションがコンピューターにインストールされていることがわかります。また、次のフォルダーには実行可能ファイル **ConfigManager.exe** が作られます:**C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**。

	このブレード内のリンクを使用して手動でゲートウェイのダウンロードとインストールを行い、**[キーで登録]** テキスト ボックスに表示されるキーを使用して登録することも可能です。
	
	ゲートウェイの詳細については、ベスト プラクティスや考慮すべき事項を含む「[Data Management Gateway](#DMG)」セクションを参照してください。 

4. **[新しいデータ ゲートウェイ]** ブレードで **[OK]** をクリックします。
5. コンピューター上で **Microsoft Data Management Gateway Configuration Manager** アプリケーション  を起動します。

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. 値が以下の状態になるまで待ちます。
	1. **[サービスの状態]** が **[開始済み]** になっていない場合、**[サービスを開始]** をクリックしてサービスを開始し、他のフィールドが更新するまでしばらく待ちます。
	2. **[ゲートウェイ名]** は **adftutorialgateway** になります。
	3. **[インスタンス名]** は **adftutorialgateway** になります。
	4. **[ゲートウェイ キーの状態]** は **[登録済み]** になります。
	5. 下部のステータス バーには、**緑色のチェック マーク**と一緒に **"Data Management Gateway クラウド サービスに接続済み"** と表示されます。  

7. **[リンクされたサービス]** ブレードで、ゲートウェイの **[状態]** が **[良好]** であることを確認し、**[データ ストアを追加]** をクリックします。ブレードを更新するため、場合によってはブレードを閉じて再度開く必要があります。 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. **[新しいデータ ストア]** ブレードで、**[名前]** に「**OnPremSqlLinkedService**」と入力します。
9. **[種類 (設定が必要)]** をクリックし、**[SQL Server]** を選択します。 
10. **[新しいデータ ストア]** ブレードで、**[データ ゲートウェイ (必要な設定の構成)]** をクリックします。

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. 前もって作成した **[adftutorialgateway]** を選択します。 
12.	**[新しいデータ ストア]** ブレードで、**[資格情報]** をクリックし、**[資格情報]** ブレードを表示します。

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	**[資格情報]** ブレードで、**[ここをクリックして安全に資格情報を設定する]** をクリックします。これにより以下のポップアップ ダイアログ ボックスが起動します。

	![One Click application install][image-data-factory-oneclick-install]

14. **[実行]** をクリックし、**資格情報マネージャー** アプリケーションをインストールすると、[資格情報の設定] ダイアログ ボックスが表示されます。 
15.	**[資格情報の設定]** ダイアログ ボックスで、サービスが内部設置型の SQL Server データベースへのアクセスに使用できる**ユーザー名**と**パスワード**を入力し、**[OK]** をクリックします。このダイアログ ボックスでは **SQL 認証**のみがサポートされています。ダイアログ ボックスが閉じるまで待ちます。
16.	**[資格情報]** ブレードで **[OK]** をクリックし、**[新しいデータ ストア]** ブレードで **[OK]** をクリックします。[リンクされたサービス] ブレードで追加した、リンクされたサービス **OnPremSqlLinkedService** が表示されるはずです。

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### Azure ストレージ アカウント用のリンクされたサービスを追加する

1.	**[DATA FACTORY]** ブレードで、**[リンクされたサービス]** タイルをクリックして、**[リンクされたサービス]** ブレードを起動します。
2. **[リンクされたサービス]** ブレードで、**[データ ストアを追加]** をクリックします。	
3. **[新しいデータ ストア]** ブレードで、以下を実行します。
	1. データ ストアの **[名前]** を入力します。このチュートリアルでは、**名前**として「**MyBlobStore**」を入力します。
	2. データ ストアの **[説明]** (オプション) を入力します。
	3. **[種類]** をクリックします。
	4. **[Azure ストレージ アカウント]** を選択し、**[OK]** をクリックします。
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. **[新しいデータ ストア]** ブレードに戻ると、以下のように表示されます。

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5. Azure ストレージ アカウントの **[アカウント名]** と **[アカウント キー]** を入力し、**[OK]** をクリックします。

  
6. **[新しいデータ ストア]** ブレードで **[OK]** をクリックすると、**[リンクされたサービス]** ブレードの **[データ ストア]** 一覧に **MyBlobStore** が表示されます。(左側にある) **[通知]** ハブにメッセージがないか確認します。

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## 手順 3.入力データセットと出力データセットを作成する
このステップでは、コピー操作 (内部設置型の SQL Server データベース => Azure BLOB ストレージ) の入力および出力データを表す入出力データ セットを作成します。Azure ポータルでは、データ セットおよびパイプラインの作成はまだサポートされていないため、Azure PowerShell コマンドレットを使用してテーブルとパイプラインを作成します。データセットまたはテーブル (四角形のデータセット) を作成する前に、以下を実施する必要があります (一覧の後に詳細な手順があります)。

- リンクされたサービスとしてデータ ファクトリに追加した SQL Server Database 内に **"emp"** という名前のテーブルを作成し、サンプル エントリをいくつかテーブルに挿入します。
- - 「[Azure Data Factory を使ってみる][adfgetstarted]」の記事でチュートリアルを終えていなければ、リンクされたサービスとしてデータ ファクトリに追加した Azure BLOB ストレージ アカウント内に「**adftutorial**」という名前の BLOB コンテナーを作成します。

### チュートリアル用に内部設置型の SQL Server を用意します。

1. 内部設置型の SQL Server のリンクされたサービス用に指定したデータベース (**OnPremSqlLinkedService**) で、以下の SQL スクリプトを使用してデータベース内に **emp** テーブルを作成します。


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. テーブルにサンプルをいくつか挿入します。 


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### 入力テーブルの作成

1.	SQL Server データベース内の **emp** テーブルからのデータを表す Data Factory テーブル用に JSON ファイルを作成します。**メモ帳** を起動して次の JSON スクリプトをコピーし、**EmpOnPremSQLTable.json** として C:\ADFGetStarted\**OnPrem** フォルダーに保存します。このフォルダーが存在しない場合は **C:\ADFGetStarted** フォルダー内に **OnPrem** サブフォルダ―を作成します。 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	以下の点に注意してください。 
	
	- location の **type** は **OnPremisesSqlServerTableLocation** に設定します。
	- **tableName** は **emp** に設定します。
	- **linkedServiceName** (リンクされたサービス名) が **OnPremSqlLinkedService** に設定されています (このリンクされたサービスは手順 2 で作成しました)。
	- Azure Data Factory 内の他のパイプラインによっては生成されない入力テーブルのために、JSON 内で **waitOnExternal** セクションを指定する必要があります。これは、入力データが Azure Data Factory サービスの外部で生成されることを意味します。   

	JSON プロパティの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)][json-script-reference]」を参照してください。

2. The Azure Data Factory コマンドレットは **AzureResourceManager** モードで利用できます。**Azure PowerShell** を起動し、次のコマンドを実行して **AzureResourceManager** モードに切り替えます。     

        switch-azuremode AzureResourceManager

	コンピューターにインストール済みではない場合、[Azure PowerShell][azure-powershell-install] をダウンロードします。
3. **New-AzureDataFactoryTable** コマンドレットを実行して、**EmpOnPremSQLTable.json** ファイルを使用して入力テーブルを作成します。

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	異なるリソース グループを使用している場合は、コマンドを更新します。

### 出力テーブルの作成

1.	Data Factory テーブル用の JSON ファイルを作成します。これは次の手順で作成するパイプラインの出力として使用します。メモ帳を起動し、次の JSON スクリプトをコピーして貼り付け、**OutputBlobTable.jsonn** という名前を付けて **C:\ADFGetStarted\OnPrem** フォルダーに保存します。

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	以下の点に注意してください。 
	
	- location の **type** は **AzureBlobLocation** に設定します。
	- **linkedServiceName** (リンクされたサービス名) が **MyBlobStore** に設定されています (このリンクされたサービスは手順 2 で作成しました)。
	- **folderPath** は **adftutorial/outfromonpremdf** に設定します。outfromonpremdf は adftutorial コンテナー内のフォルダーです。必要な操作は **adftutorial** コンテナーの作成のみです。
	- **availability** は **1 時間単位** に設定します (**frequency** を **hour** に設定し、**interval** を **1** に設定します)。  Data Factory サービスは、1 時間に 1 回、Azure SQL Database 内の **emp** テーブルに出力データ スライスを生成します。 

	**入力テーブル**に **fileName** (ファイル名) を指定しない場合、input フォルダー (**folderPath**) 内のすべてのファイルまたは BLOB が入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。例については、[チュートリアル][adf-tutorial]のサンプル ファイルを参照してください。
 
	**output テーブル**に **fileName** を指定しない場合、**folderPath** に生成されたファイルに次の形式で名前が付けられます。Data.<Guid>.txt (例: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

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

 

	JSON プロパティの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)][json-script-reference]」を参照してください。

2.	**Azure PowerShell** で、次のコマンドを実行し、Azure SQL データベース内のテーブルを表す別の Data Factory テーブルを作成します。

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## 手順 4.パイプラインを作成して実行する
この手順では、**EmpOnPremSQLTable** を入力、**OutputBlobTable** を出力として使用し、**コピー アクティビティ**を持つ**パイプライン**を作成します。

1.	パイプラインの JSON ファイルを作成します。メモ帳を起動し、次の JSON スクリプトをコピーして貼り付け、**ADFTutorialPipelineOnPrem.json** という名前を付けて **C:\ADFGetStarted\OnPrem** フォルダーに保存します。
	 

        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
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
	- ****アクティビティの入力は **EmpOnPremSQLTable** に設定され、アクティビティの出力は ******OutputBlobTable** に設定されています。
	- **transformation** (変換) セクションでは、ソースの種類として **SqlSource** (SQL ソース) が指定され、シンクの種類として ******BlobSink ** (BLOB シンク) が指定されています。****
	- SQL クエリ **select * from emp** は **SqlSource** の **sqlReaderQuery** プロパティについて指定したものです。

2. **New-AzureDataFactoryPipeline** コマンドレットを実行し、作成した **ADFTutorialPipeline.json** ファイルを使用してパイプラインを作成します。

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. パイプラインの作成が済めば、データ処理が発生する期間を指定できます。パイプラインの有効期間を指定することによって、Azure Data Factory テーブルごとに定義されている **Availability** プロパティに基づき、データ スライスが処理される期間を定義することになります。  以下の PowerShell コマンドを実行してパイプラインの有効期間を設定し、「Y」を入力して確認します。 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] **StartDateTime** の値を現在の日付に置き換え、**EndDateTime** の値を翌日の日付に置き換えます。StartDateTime と EndDateTime はいずれも UTC 時間で、[ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります。次に例を示します。2014-10-14T16:32:41Z.EndDateTime は省略可能ですが、このチュートリアルでは使用します。
	> **EndDateTime** を指定しない場合は、"**StartDateTime + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9/9/9999** を **EndDateTime** として指定します。

	上記の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。
4. **Azure プレビュー ポータル**で、**ADFTutorialOnPremDF** データ ファクトリのホーム ページで **[ダイアグラム]** タイルをクリックします。 :

	![Diagram Link][image-data-factory-diagram-link]

5. 以下のような図が表示されるはずです。

	![Diagram View][image-data-factory-diagram-view]

	**ご利用ありがとうございます。**Azure データ ファクトリ、リンクされたサービス、テーブル、および 	パイプラインを作成し、ワークフローを開始しました。

## 手順 5.データセットとパイプラインの監視
このステップでは、Azure ポータルを使用して、Azure Data Factory の状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。監視用のコマンドレットの使用方法については、「[Azure PowerShell を使用した Azure Data Factory の監視と管理][monitor-manage-powershell]」を参照してください。

1. **Azure プレビュー ポータル**に移動します (閉じていた場合)。
2. **ADFTutorialOnPremDF** のブレードが開いていない場合は、**スタート画面**で **ADFTutorialOnPremDF** をクリックして開きます。
3. 作成したテーブルの**数**と**名前**、およびパイプラインの名前がブレードに表示されます。

	![Data Factory Home Page][image-data-factory-homepage-2]
4. **[データセット]** タイルをクリックします。
5. **[データセット]** ブレードで、**[EmpOnPremSQLTable]** をクリックします。

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. 現在の時刻までのデータ スライスが既に生成されており、**準備完了**になっています。これは、SQL Server データベースに挿入したデータが、現在まで残っているためです。下部の **[問題のあるスライス]** セクションにスライスが表示されていないことを確認します。
7. **[データセット]** ブレードで、**[OutputBlobTable]** をクリックします。

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. 現在の時刻までのスライスが生成済みで、**[準備完了]** 状態であることを確認します。
9. 一覧のうち任意のデータ スライスをクリックすると、**[データ スライス]** ブレードが表示されます。

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. 下部にある一覧から **[アクティビティの実行]** をクリックし、**[アクティビティの実行の詳細]** を表示します。

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. **[X]** をクリックすると、すべてのブレードが閉じられ、**ADFTutorialOnPremDF** のホーム ブレードに戻ります。
14. (省略可能) **[パイプライン]** をクリックし、**[ADFTutorialOnPremDF]** をクリックして、入力テーブル (**使用** または出力テーブル (**生成**) のドリル スルーを行います。
15. 出力を確認するには、**Azure ストレージ エクスプローラー**などのツールを使用します。

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Azure PowerShell コマンドレットを使用してゲートウェイの作成と登録を行う
このセクションでは、Azure PowerShell コマンドレットを使用して、ゲートウェイを作成し登録する方法について説明します。 

1. 管理者モードで **Azure PowerShell** を起動します。 
2. The Azure Data Factory コマンドレットは **AzureResourceManager** モードで利用できます。次のコマンドを実行して **AzureResourceManager** モードに切り替えます。     

        switch-azuremode AzureResourceManager


2. **New-AzureDataFactoryGateway** コマンドレットを使用して、以下のように論理ゲートウェイを作成します。

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**コマンドと出力の例**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. **New-AzureDataFactoryGatewayKey** コマンドレットを使用して、新しく作成したゲートウェイの登録キーを生成し、それをローカル変数 **$Key** に格納します:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**コマンドの出力例:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Azure PowerShell で **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript\** フォルダーに移動し、以下のコマンドに示すとおりローカル変数 **$Key** に関連付けられた **RegisterGateway.ps1** スクリプトを実行し、コンピューターにインストールしたクライアント エージェントを前もって作成した論理ゲートウェイに登録します。

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. **Get-AzureDataFactoryGateway** コマンドレットを使用すれば、データ ファクトリ内のゲートウェイの一覧を取得できます。**[状態]** に**[オンライン]** と表示されれば、ゲートウェイが使用できるという意味です。

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

**Remove-AzureDataFactoryGateway** コマンドレットを使用してゲートウェイを削除し、**Set-AzureDataFactoryGateway** コマンドレットを使用してゲートウェイについての説明を更新できます。これらのコマンドレットの文法やその他の詳細については「Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)」を参照してください。  




## 関連項目

記事 | 説明
------ | ---------------
[Azure Data Factory を使ってみる][adf-getstarted] | この記事には、Azure BLOB から Azure SQL データベースにデータをコピーする Azure Data Factory サンプルの作成方法についての詳細なチュートリアルが記載されています。
[Data Factory で Pig と Hive を使用する][use-pig-and-hive-with-data-factory] | この記事のチュートリアルでは、HDInsight Activity を使用して Hive/Pig スクリプトを実行し、入力データを処理して出力データを生成する方法が学べます。 
[チュートリアル: Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。 
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。  
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/


[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png
