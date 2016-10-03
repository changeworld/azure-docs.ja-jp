<properties 
	pageTitle="データの移動 - Data Management Gateway | Microsoft Azure"
	description="オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。Azure Data Factory で Data Management Gateway を使用してデータを移動します。" 
    keywords="データ ゲートウェイ, データ統合, データの移動, ゲートウェイの資格情報"
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
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する
この記事では、Data Factory を使用したオンプレミス データ ストアとクラウド データ ストア間でのデータ統合の概要について説明します。この記事は、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事と、Data Factory の中核となる概念である[データセット](data-factory-create-datasets.md)と[パイプライン](data-factory-create-pipelines.md)に関する他の記事に基づいています。

## Data Management Gateway
オンプレミス データ ストアとの間でデータを移動できるようにするには、オンプレミス コンピューターに Data Management Gateway をインストールする必要があります。ゲートウェイはデータ ストアと同じコンピューターにインストールできるほか、ゲートウェイがデータ ストアに接続できれば別のコンピューターにインストールしてもかまいません。Data Management Gateway の詳細については、「[Data Management Gateway](data-factory-data-management-gateway.md)」をご覧ください。

以下のチュートリアルでは、オンプレミスの SQL Server Database から Azure BLOB にデータを移動するパイプラインを備えたデータ ファクトリを作成する方法を説明します。チュートリアルの一環として、ご使用のコンピューターに Data Management Gateway をインストールして構成します。

## チュートリアル: オンプレミスのデータをクラウドにコピーする
  
## データ ファクトリの作成
この手順では、Azure Portal を使用して **ADFTutorialOnPremDF** という名前の Azure Data Factory インスタンスを作成します。Azure Data Factory のコマンドレットを使用してデータ ファクトリを作成することもできます。

1.	[Azure Portal](https://portal.azure.com) にログインしたら、左下隅にある **[新規]** をクリックします。**[作成]** ブレードで **[データ分析]** を選択し、**[データ分析]** ブレードで **[Data Factory]** をクリックします。

	![[新規] -> [DataFactory]](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. **[新しいデータ ファクトリ]** ブレードで以下の手順を実行します。
	1. **[名前]** に「**ADFTutorialOnPremDF**」と入力します。
	2. **[リソース グループ名]** をクリックし、**ADFTutorialResourceGroup** を選択します。既存のリソース グループを選択するか、リソース グループを作成することができます。リソース グループを作成するには:
		1. **[新しいリソース グループを作成]** をクリックします。
		2. **[リソース グループを作成] ブレード**で、リソース グループの**名前**を入力し、**[OK]** をクリックします。

7. **[新しいデータ ファクトリ]** ブレードの **[スタート画面に追加]** がオンになっていることを確認してください。

	![スタート画面への追加](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. **[新しいデータ ファクトリ]** ブレードで、**[作成]** をクリックします。

	Azure Data Factory の名前はグローバルに一意にする必要があります。"**データ ファクトリ名 "ADFTutorialOnPremDF" は使用できません**" というエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialOnPremDF など) 作成し直してください。このチュートリアルでは以降の手順の実行中に、この名前を ADFTutorialOnPremDF の代わりに使用します。

9. 作成プロセスの通知を確認するには、次の図のように、タイトル バーの **[通知]** ボタンをクリックします。通知ウィンドウを閉じるには、もう一度クリックします。

	![[通知] ハブ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. 作成が完了すると、次の図に示すような **[Data Factory]** ブレードが表示されます。

	![Data Factory ホーム ページ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## ゲートウェイの作成
5. **[Data Factory]** ブレードで、**[作成とデプロイ]** タイルをクリックして、Data Factory の**エディター**を起動します。

	![[作成とデプロイ] タイル](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
6.	Data Factory エディターのツールバーの **[...] \(省略記号)** をクリックし、**[新しいデータ ゲートウェイ]** をクリックします。

	![ツールバーでのデータ ゲートウェイの新規作成](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. **[作成]** ブレードで、**名前**に「**adftutorialgateway**」と入力し、**[OK]** をクリックします。

	![[ゲートウェイの作成] ブレード](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. **[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。この操作により、ゲートウェイのインストール パッケージのダウンロードと、コンピューターへのインストール、構成、および登録が行われます。

	> [AZURE.NOTE] 
	Internet Explorer または Microsoft の ClickOnce と互換性のある Web ブラウザーを使用します。
	> 
	> Chrome を使用する場合は、[Chrome Web ストア](https://chrome.google.com/webstore/)に移動し、"ClickOnce" キーワードで検索して、ClickOnce 拡張機能のいずれかを選択してインストールします。
	>  
	> Firefox についても、同じ操作を実行します (アドインをインストール)。ツール バーの **[メニューを開く]** ボタン (右上隅にある **3 本の横線**) をクリックし、**[アドオン]** をクリックします。"ClickOnce" キーワードで検索し、ClickOnce 拡張機能のいずれかを選択してインストールします。

	![ゲートウェイ - 構成 ブレード](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	これは、たった 1 つの手順 (クリック 1 回) でゲートウェイのダウンロード、インストール、構成、および登録を行う、最も簡単な方法です。**Microsoft Data Management Gateway 構成マネージャー** アプリケーションがコンピューターにインストールされていることがわかります。**C:\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared** フォルダーで実行可能ファイル **ConfigManager.exe** を検索することもできます。

	このブレード内のリンクを使用してゲートウェイのダウンロードとインストールを手動で行い、**[新しいキー]** ボックスに表示されるキーを使用して登録することもできます。
	
	ゲートウェイの詳細については、「[Data Management Gateway](data-factory-data-management-gateway.md)」をご覧ください。

	>[AZURE.NOTE] Data Management Gateway を正常にインストールして構成するには、ローカル コンピューターの管理者である必要があります。他のユーザーをローカル Windows グループの Data Management Gateway Users に追加できます。このグループのメンバーは、Data Management Gateway 構成マネージャー ツールを使用して、ゲートウェイを構成できます。

5. 数分待ってから、コンピューターで **[Data Management Gateway Configuration Manager]** アプリケーションを起動します。**[検索]** ウィンドウに、このユーティリティにアクセスする **Data Management Gateway** を入力します。**C:\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared** フォルダーで実行可能ファイル **ConfigManager.exe** を検索することもできます。

	![Gateway Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. 次の状態になるまで待機します。
	2. **[ゲートウェイ名]** が **[adftutorialgateway]** になります。
	4. 下部のステータス バーに、**緑色のチェック マーク**と共に "**Connected to the cloud service (クラウド サービスに接続済み)**" と表示されます。

	**[ホーム]** タブでは、次の操作を行うこともできます。- [登録] ボタンを使用して、Azure Portal からキーを使ってゲートウェイを**登録**する。- ゲートウェイ コンピューターで実行されている Data Management Gateway Host Service を**停止**する。- 1 日の特定の時刻にインストールするように**更新のスケジュールを設定**する。- ゲートウェイの**最終更新**日時を表示する。

8. **[設定]** タブに切り替えます。**[証明書]** セクションに示されている証明書は、ポータルで指定したオンプレミス データ ストアの資格情報の暗号化/暗号化解除に使用されます。独自の証明書を使用するには、**[変更]** をクリックします (省略可能)。既定では、ゲートウェイは Data Factory サービスによって自動生成される証明書を使用します。

	![ゲートウェイ証明書の構成](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

	[設定] タブでは、次の操作を行うこともできます。- ゲートウェイで使用されている証明書を表示またはエクスポートする。- ゲートウェイで使用される HTTPS エンドポイントを変更する。
9. (省略可能) **[診断]** タブに切り替え、ゲートウェイで発生した問題のトラブルシューティングに使用できる詳細なログ記録を有効にする場合は、**[詳細なログ記録]** をオンにします。ログ情報については、**[アプリケーションとサービス ログ]**、**[Data Management Gateway]** ノードの順に進み、**[イベント ビューアー]** を参照してください。

	![[診断] タブ](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	**[診断]** タブでは、次の操作を行うこともできます。
	
	- ゲートウェイを使用して、オンプレミスのデータ ソースに **[接続のテスト]** セクションを使用します。
	- **[ログの表示]** をクリックして、[イベント ビューアー] ウィンドウの Data Management Gateway のログを確認します。
	- **[ログを送信]** をクリックして過去 7 日間のログの zip ファイルを Microsoft にアップロードし、問題のトラブルシューティングを容易にします。
10. Azure Portal の **[構成]** ブレードと **[新しいデータ ゲートウェイ]** ブレードで **[OK]** をクリックします。
6. 左側のツリービューの **[データ ゲートウェイ]** に **[adftutorialgateway]** と表示されます。クリックすると、関連する JSON が表示されます。
	

## リンクされたサービスの作成 
この手順では、**AzureStorageLinkedService** と **SqlServerLinkedService** の 2 つのリンクされたサービスを作成します。**SqlServerLinkedService** はオンプレミスの SQL Server Database をリンクし、**AzureStorageLinkedService** リンク サービスは Azure BLOB ストアを Data Factory にリンクします。このチュートリアルの後半で、オンプレミスの SQL Server Database から Azure BLOB ストアにデータをコピーするパイプラインを作成します。

#### オンプレミスの SQL Server Database にリンクされたサービスを追加する
1.	**Data Factory エディター**のツール バーにある **[新しいデータ ストア]** クリックし、**[SQL Server]** を選択します。

	![SQL Server のリンクされているサービス](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
3.	**JSON エディター**で次の操作を実行します。
	1. **[gatewayName]** に **[adftutorialgateway]** を指定します。
	2. Windows 認証を使用している場合:
		1. **[ConnectionString]** で、**[統合セキュリティ]** を **true** に設定し、データベースの**サーバー名**と**データベース名**を指定して、**ユーザー ID** と**パスワード**を削除します。
		3. **[userName]** と **[password]** の各プロパティにユーザー名とパスワードを指定します。
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication. Example: <domain>\<user>",
            		"password": "<Specify password for the user account>"
        		}

	4. SQL 認証を使用している場合:
		1. データベースの **[connectionString]** にデータベースの**サーバー名**、**データベース名**、**ユーザー ID**、**パスワード**を指定します。
		2. 末尾 2 つの JSON プロパティである **[userName]** と **[password]** を JSON から削除します。
		3. gatewayName** プロパティ**の値を指定する行末の** `,` (コンマ) を削除します。

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	
		資格情報は、Data Factory サービスが所有する証明書を使用して**暗号化**されます。Data Management Gateway に関連付けられている証明書を使用する場合は、「[Set credentials securely (資格情報を安全に設定する)](#set-credentials-and-security)」を参照してください。
    
2.	コマンド バーの **[デプロイ]** をクリックして、SQL Server リンク サービスをデプロイします。

#### Azure ストレージ アカウント用のリンクされたサービスを追加する
 
1. **Data Factory エディター**で、コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure ストレージ]** をクリックします。
2. Azure ストレージ アカウントの名前を **[アカウント名]** に入力します。
3. Azure ストレージ アカウントのキーを **[アカウント キー]** に入力します。
4. **[デプロイ]** をクリックして **AzureStorageLinkedService** をデプロイします。
   
 
## データセットを作成する
このステップでは、コピー操作 (オンプレミスの SQL Server Database => Azure BLOB ストレージ) の入力および出力データを表す入出力データ セットを作成します。データセットまたはテーブル (四角形のデータセット) を作成する前に、以下を実施する必要があります (一覧の後に詳細な手順があります)。

- リンク サービスとしてデータ ファクトリに追加した SQL Server Database 内に "**emp**" という名前のテーブルを作成し、このテーブルにサンプル エントリをいくつか挿入します。
- Data Factory にリンクされたサービスとして追加した Azure BLOB ストレージ アカウントに **adftutorial** という名前の BLOB コンテナーを作成します。

### チュートリアル用にオンプレミスの SQL Server を用意します。

1. オンプレミスの SQL Server リンク サービス (**SqlServerLinkedService**) 用に指定したデータベースで、次の SQL スクリプトを使用して、データベースに **emp** テーブルを作成します。


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

1. **Data Factory エディター**で、コマンド バーの **[新しいデータセット]** をクリックし、**[SQL Server テーブル]** をクリックします。
2.	右側のウィンドウの JSON を次のテキストに置き換えます。

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	以下の点に注意してください。
	
	- **type** は **SqlServerTable** に設定されています。
	- **tableName** を **emp** に設定します。
	- **linkedServiceName** を **SqlServerLinkedService** (手順 2. で作成したリンク サービス) に設定します。
	- Azure Data Factory の別のパイプラインでは生成されない入力テーブルの場合、**external** を **true** に設定する必要があります。これは、入力データが Azure Data Factory サービスの外部で生成されることを意味します。必要に応じて、**Policy** セクションの **externalData** 要素を使用して外部データ ポリシーを指定できます、

	JSON プロパティの詳細については、[JSON スクリプト リファレンス][json-script-reference]を参照してください。

2. コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。タイトル バーに "**テーブルは正常にデプロイされました**" というメッセージが表示されていることを確認します。


### 出力テーブルの作成

1.	**Data Factory エディター**で、コマンド バーの **[新しいデータセット]** をクリックし、**[Azure BLOB ストレージ]** をクリックします。
2.	右側のウィンドウの JSON を次のテキストに置き換えます。

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	以下の点に注意してください。
	
	- **type** は **AzureBlob** に設定されています。
	- **linkedServiceName** を **AzureStorageLinkedService** (手順 2. で作成したリンクされたサービス) に設定します。
	- **folderPath** を **adftutorial/outfromonpremdf** に設定します。outfromonpremdf は adftutorial コンテナー内のフォルダーです。**adftutorial** コンテナーを作成します (既に存在していない場合)。
	- **availability** が **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。Data Factory サービスは、Azure SQL Database 内の **emp** テーブルに 1 時間ごとに出力データ スライスを生成します。

	**入力テーブル**に **fileName** を指定しない場合、入力フォルダー (**folderPath**) のすべてのファイルまたは BLOB が入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。例については、[チュートリアル][adf-tutorial]のサンプル ファイルを参照してください。
 
	**output table** に **fileName** を指定しない場合、**folderPath** に生成されるファイルには Data.<Guid>.txt という形式で名前が付けられます (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)。

	**folderPath** と **fileName** を **SliceStart** の時刻に基づいて動的に設定するには、partitionedBy プロパティを使用します。次の例では、folderPath に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。たとえば、スライスが 2014-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2014/10/20 に設定され、fileName は 08.csv に設定されます。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	JSON プロパティの詳細については、[JSON スクリプト リファレンス][json-script-reference]を参照してください。

2.	コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。タイトル バーに "**テーブルは正常にデプロイされました**" というメッセージが表示されていることを確認します。
  

## パイプラインの作成
この手順では、**EmpOnPremSQLTable** を入力として使用し、**OutputBlobTable** を出力として使用する**コピー アクティビティ**を 1 つ使用する**パイプライン**を作成します。

1.	**[Data Factory]** ブレードで、**[作成とデプロイ]** タイルをクリックして、Data Factory の**エディター**を起動します。

	![[作成とデプロイ] タイル](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2.	コマンド バーの **[新しいパイプライン]** をクリックします。このボタンが表示されない場合は、**[...] \(省略記号)** をクリックしてコマンド バーを展開します。
2.	右側のウィンドウの JSON を次のテキストに置き換えます。
	
			{
				"name": "ADFTutorialPipelineOnPrem",
				"properties": {
		    	"description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    	"activities": [
		      	{
		        	"name": "CopyFromSQLtoBlob",
			        "description": "Copy data from on-prem SQL server to blob",
		        	"type": "Copy",
		        	"inputs": [
		          	{
		            	"name": "EmpOnPremSQLTable"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "OutputBlobTable"
			          }
			        ],
			        "typeProperties": {
			          "source": {
			            "type": "SqlSource",
			            "sqlReaderQuery": "select * from emp"
			          },
			          "sink": {
			            "type": "BlobSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2016-07-05T00:00:00Z",
			    "end": "2016-07-06T00:00:00Z",
			    "isPaused": false
			  }
			}

	以下の点に注意してください。
 
	- activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。
	- アクティビティの**入力**を **EmpOnPremSQLTable** に設定し、**出力**を **OutputBlobTable** に設定します。
	- **transformation** セクションでは、**ソースの種類**として **SqlSource** を指定し、**シンクの種類**として **BlobSink** を指定します。
	- **SqlSource** の **sqlReaderQuery** プロパティに、SQL クエリ `select * from emp` を指定します。

	**start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。start と end の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。**end** の時刻は省略可能ですが、このチュートリアルでは使用します。
	
	**end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9/9/9999** を **end** プロパティの値として指定します。
	
	Azure Data Factory テーブルごとに定義された **Availability** プロパティに基づいて、データ スライスを処理する期間を定義します。
	
	この例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。

	
2. コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。タイトル バーに "**パイプラインは正常にデプロイされました**" というメッセージが表示されていることを確認します。
5. **[X]** をクリックして **[エディター]** ブレードを閉じます。もう一度 **[X]** をクリックして、[ADFTutorialDataFactory] ブレードをツール バーおよびツリー ビューと共に閉じます。**"保存されていない編集は破棄されます"** というメッセージが表示されたら、**[OK]** をクリックします。
6. **ADFTutorialOnPremDF** の **[DATA FACTORY]** ブレードに戻ります。

**ご利用ありがとうございます。** これで、Azure Data Factory、リンクされたサービス、テーブル、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。

#### ダイアグラム ビューでの Data Factory の表示 
1. **Azure Portal** の **ADFTutorialOnPremDF** データ ファクトリのホーム ページで、**[ダイアグラム]** タイルをクリックします。

	![ダイアグラム リンク](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. 以下のような図が表示されるはずです。

	![ダイアグラム ビュー](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	パイプラインとテーブルは、拡大、縮小、100% に拡大、ウィンドウのサイズに合わせて大きさを変更、自動的に配置などの表示が可能です。また、系列情報を表示 (選択した項目の上位項目や下位項目を強調表示) することもできます。オブジェクト (入力/出力テーブルまたはパイプライン) をダブルクリックすると、そのオブジェクトのプロパティを表示できます。

## パイプラインを監視する
この手順では、Azure ポータルを使用して、Azure データ ファクトリの状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。監視の詳細については、「[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」という記事を参照してください。

1. **Azure Portal** に移動します (閉じていた場合)。
2. **ADFTutorialOnPremDF** のブレードが開いていない場合は、**スタート画面**で **ADFTutorialOnPremDF** をクリックして開きます。
3. このブレードには、作成したテーブルとパイプラインの**数**と**名前**が表示されます。

	![Data Factory ホーム ページ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. **[データセット]** タイルをクリックします。
5. **[データセット]** ブレードで、**[EmpOnPremSQLTable]** をクリックします。

	![EmpOnPremSQLTable slices](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. 現在の時刻までのデータ スライスが既に生成されており、**[準備完了]** になっています。これは、SQL Server Database に挿入したデータが、現在まで残っているためです。下部の **[問題のあるスライス]** セクションにスライスが表示されていないことを確認します。
	
	**[最近更新したスライス]** と **[最近失敗したスライス]** の一覧は、どちらも **[最終更新時刻]** で並べ替えられます。次の状況では、スライスの更新時刻が変更されます。

	**Set-AzureRmDataFactorySliceStatus** を使用したり、スライスの **[スライス]** ブレードで **[実行]** をクリックしたりすることで、スライスの状態を手動で更新した場合。
	
	スライスの実行 (実行の開始、実行の終了と失敗、実行の終了と成功など) により、スライスの状態が変わります。

	一覧のタイトルをクリックするか、**[...] \(省略記号)** をクリックすると、さらに多くのスライスが一覧表示されます。スライスをフィルター処理するには、ツール バーの **[フィルター]** をクリックします。

	代わりに、スライスの開始時刻と終了時刻で並べ替えられたデータ スライスを表示するには、**[データ スライス (スライスの時刻別)]** タイルをクリックします。
7. **[データセット]** ブレードで、**[OutputBlobTable]** をクリックします。

	![OputputBlobTable slices](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
8. 現在の時刻までのスライスが生成されており、**[準備完了]** 状態であることを確認します。現在の時刻までのスライスの状態が **[準備完了]** になるまで待ちます。
9. 一覧の任意のデータ スライスをクリックすると、**[データ スライス]** ブレードが表示されます。

	![Data Slice Blade](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	スライスが **[準備完了]** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、**[準備完了でない上位スライス]** の一覧に表示されます。

10. 下部にある一覧の **[アクティビティの実行]** をクリックして、**[アクティビティの実行の詳細]** を表示します。

	![Activity Run Details blade](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

11. **[X]** をクリックしてすべてのブレードを閉じ、
12. **ADFTutorialOnPremDF** のホーム ブレードに戻ります。
14. (省略可能) **[パイプライン]** をクリックし、**[ADFTutorialOnPremDF]** をクリックして、入力テーブル (**Consumed**) または出力テーブル (**Produced**) をドリル スルーします。
15. **Azure Storage Explorer** などのツールを使用して、出力を確認します。

	![Azure ストレージ エクスプローラー](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## 次のステップ

- Data Management Gateway の詳細については、「[Data Management Gateway](data-factory-data-management-gateway.md)」をご覧ください。
- コピー アクティビティを使用して、ソース データ ストアからシンク データ ストアにデータを移動する方法については、[Azure BLOB から Azure SQL へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。

<!---HONumber=AcomDC_0914_2016-->