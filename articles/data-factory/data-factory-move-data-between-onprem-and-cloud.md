<properties 
	pageTitle="Azure Data Factory を使用してオンプレミスとクラウドの間でデータを移動する" 
	description="Data Management Gateway と Azure Data Factory を使用してオンプレミスとクラウドの間でデータを移動する方法を説明します。" 
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
	ms.date="05/18/2016" 
	ms.author="spelluru"/>

# Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する
Azure Data Factory を使用すると、[サポートされているオンプレミスのデータ ストアとクラウド データ ストア](data-factory-data-movement-activities.md#supported-data-stores)との間で、**Data Management Gateway** を使ってデータを簡単に移動できます。Data Management Gateway は、オンプレミスのコンピューターにインストールすることによって、オンプレミスのデータ ストアとクラウドのデータ ストアとの間でデータをコピーすることができるクライアント エージェントです。ゲートウェイは、データ ソースと同じコンピューターにインストールできるほか、データ ソースにさえアクセスできれば異なるコンピューターにインストールしてもかまいません。

## Data Management Gateway でのデータ フロー
データ パイプラインで[コピー アクティビティ](data-factory-data-movement-activities.md)を使用してオンプレミスのデータを処理のためにクラウドに取り込むとき、またはクラウドの結果データをオンプレミスのデータ ストアにエクスポートして戻すときは、コピー アクティビティは内部的にゲートウェイを使用してオンプレミスのデータ ソースからクラウドに、またはその逆に、データを転送します。データ ゲートウェイでのコピーのデータ フローと手順の概要を次に示します。![ゲートウェイを使用したデータ フロー](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	データの開発者は、[Azure ポータル](https://portal.azure.com)または [PowerShell コマンドレット](https://msdn.microsoft.com/library/dn820234.aspx)を使用して Azure Data Factory 用の新しい**ゲートウェイ**を作成します。この手順には、論理ゲートウェイをバックエンドに作成し、物理ゲートウェイをオンプレミスのコンピューターにインストールして、キーを使ってそれらを関連付ける作業が含まれます。  
2.	データ開発者が、オンプレミス データ ストア用に**リンクされたサービス**を新しく定義し、そのデータ ストアで使用するゲートウェイの名前を指定します。データ開発者は、リンクされたサービスをセットアップする過程で、オンプレミスのデータ ストアにアクセスするための資格情報を指定します。  
3.	ゲートウェイは、資格情報をクラウドに保存する前に、(開発者によって提供された) ゲートウェイと関連付けられた証明書で**資格情報を暗号化**します。
4.	Data Factory サービスは、共有 Azure Service Bus キューを使用する制御チャネルを介して、ジョブのスケジューリングと管理のためにゲートウェイと通信します。コピー アクティビティ ジョブを開始する必要がある場合、Data Factory はリクエストと資格情報をキューに入れます。ゲートウェイは、キューをポーリングした後でジョブを開始します。
5.	ゲートウェイは、同じ証明書で**資格情報の暗号化を解除**し、その資格情報を使ってオンプレミスのデータ ストアに接続します。
6.	ゲートウェイは、データ パイプラインでのコピー アクティビティの構成方法に応じて、オンプレミスのストアからクラウドのストレージに、またはクラウドのストレージからオンプレミスのデータ ストアに、**データをコピー**します。注: この手順では、ゲートウェイは、セキュリティで保護された (HTTPS) チャネルを使用して、クラウド ベースのストレージ サービス (例: Azure BLOB、Azure SQL など) と直接通信します。 

この記事では、Data Management Gateway の使用について詳しく取り上げます。まず、ゲートウェイを使用してオンプレミスの SQL Server データベースから Azure BLOB にデータを移動する簡単なチュートリアルを見てみましょう。

## チュートリアル: Data Management Gateway の使用 
このチュートリアルでは、オンプレミスの SQL Server Database から、Azure BLOB にデータを移動するパイプラインを備えたデータ ファクトリを作成します。

### 手順 1. Azure Data Factory を作成する
この手順では、Azure ポータルを使用して **ADFTutorialOnPremDF** という名前の Azure Data Factory インスタンスを作成します。Azure Data Factory のコマンドレットを使用してデータ ファクトリを作成することもできます。

1.	[Azure ポータル](https://portal.azure.com)にログインしたら、左下隅にある **[新規]** をクリックします。**[作成]** ブレードで **[データ分析]** を選択し、**[データ分析]** ブレードで **[Data Factory]** をクリックします。

	![[新規] -> [DataFactory]](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. **[新しいデータ ファクトリ]** ブレードで以下の手順を実行します。
	1. **[名前]** に「**ADFTutorialOnPremDF**」と入力します。
	2. **[リソース グループ名]** をクリックし、**ADFTutorialResourceGroup** を選択します。既存のリソース グループを選択するか、新しく作成することができます。新しいリソース グループを作成するには:
		1. **[新しいリソース グループを作成]** をクリックします。
		2. **[リソース グループを作成] ブレード**で、リソース グループの**名前**を入力し、**[OK]** をクリックします。

7. **[新しいデータ ファクトリ]** ブレードの **[スタート画面に追加]** がオンになっていることに注意してください。

	![スタート画面への追加](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. **[新しいデータ ファクトリ]** ブレードで、**[作成]** をクリックします。

	Azure Data Factory の名前はグローバルに一意にする必要があります。"**データ ファクトリ名 "ADFTutorialOnPremDF" は使用できません**" というエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialOnPremDF など) 作成し直してください。このチュートリアルでは以降の手順の実行中に、この名前を ADFTutorialOnPremDF の代わりに使用します。

9. 作成プロセスの通知を確認するには、次の図のように、タイトル バーの **[通知]** ボタンをクリックします。通知ウィンドウを閉じるには、もう一度クリックします。

	![[通知] ハブ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. 作成が完了すると、次に示すような **[Data Factory]** ブレードが表示されます。

	![Data Factory ホーム ページ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### 手順 2: Data Management Gateway を作成する
5. **[Data Factory]** ブレードで、**[作成とデプロイ]** タイルをクリックして、Data Factory の**エディター**を起動します。

	![タイルの作成とデプロイ](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	Data Factory エディターのツールバーの **[...] \(省略記号)** をクリックし、**[新しいデータ ゲートウェイ]** をクリックします。 

	![ツールバーでのデータ ゲートウェイの新規作成](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. **[作成]** ブレードで、**名前**に「**adftutorialgateway**」と入力し、**[OK]** をクリックします。 	

	![[ゲートウェイの作成] ブレード](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. **[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。これにより、ゲートウェイのインストール パッケージのダウンロードと、コンピューターへのインストール、構成、および登録が行われます。

	> [AZURE.NOTE] 
	Internet Explorer または Microsoft の ClickOnce と互換性のある Web ブラウザーを使用してください。
	> 
	> Chrome を使用する場合は、[Chrome Web ストア](https://chrome.google.com/webstore/)に移動し、"ClickOnce" キーワードで検索して、ClickOnce 拡張子のいずれかを選択してインストールします。
	>  
	> Firefox についても、同じ操作を実行する必要があります (アドインをインストール)。ツール バーの **[メニューを開く]** ボタン (右上隅にある **3 本の横線**) をクリックして、**[アドオン]** をクリックし、"ClickOnce" キーワードを使用して検索し、ClickOnce の拡張機能のいずれかを選択してインストールします。

	![ゲートウェイ - 構成 ブレード](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	これは、たった 1 つの手順 (クリック 1 回) でゲートウェイのダウンロード、インストール、構成、および登録を行う、最も簡単な方法です。**Microsoft Data Management Gateway 構成マネージャー** アプリケーションがコンピューターにインストールされていることがわかります。実行可能ファイル **ConfigManager.exe** は **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** フォルダーにあります。

	このブレード内のリンクを使用してゲートウェイのダウンロードとインストールを手動で行い、**[キーで登録]** ボックスに表示されるキーを使用して登録することもできます。
	
	ベスト プラクティスや重要な考慮事項など、ゲートウェイの詳細については、この記事の最初の方のセクションを参照してください。

	>[AZURE.NOTE] Data Management Gateway を正常にインストールして構成するには、ローカル コンピューターの管理者である必要があります。他のユーザーをローカル Windows グループの Data Management Gateway Users に追加できます。このグループのメンバーは、Data Management Gateway 構成マネージャー ツールを使用して、ゲートウェイを構成できます。

5. 数分待ってから、コンピューターで **[Data Management Gateway Configuration Manager]** アプリケーションを起動します。**[検索]** ウィンドウに、このユーティリティにアクセスする **Data Management Gateway** を入力します。**C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** フォルダーの実行可能ファイル **ConfigManager.exe** を検索することもできます。

	![Gateway Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. 値が以下の状態になるまで待ちます。
	1. **[状態]** が **[開始]** に設定されます。
	2. **[ゲートウェイ名]** が **[adftutorialgateway]** になります。
	3. **[インスタンス名]** が **[adftutorialgateway]** になります。
	4. **[登録]** が **[登録済み]** に設定されます。
	5. 下部のステータス バーには、**緑色のチェック マーク**と共に "**Data Management Gateway クラウド サービスに接続済み**" と表示されます。

8. **[証明書]** タブに切り替えます。このタブで指定された証明書は、ポータルで指定したオンプレミス データ ストアの資格情報の暗号化/暗号解除に使用されます。代わりに独自の証明書を使用する場合は、**[変更]** をクリックします。既定では、ゲートウェイは Data Factory サービスによって自動生成される証明書を使用します。

	![ゲートウェイ証明書の構成](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. (省略可能) **[診断]** タブに切り替えて、ゲートウェイで発生した問題をトラブルシューティングするのに使用できる詳細ログを有効にする場合は、**[トラブルシューティングのための詳細ログを有効にする]** オプションをオンにします。ログ情報については、**[アプリケーションとサービス ログ]**、**[Data Management Gateway]** ノードの順に進み、**[イベント ビューアー]** を参照してください。 

	![[診断] タブ](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	このページを使用すると、ゲートウェイを使用してオンプレミスのデータ ソースへの**接続をテストする**こともできます。
10. Azure ポータルの **[構成]** ブレードと **[新しいデータ ゲートウェイ]** ブレードで **[OK]** をクリックします。
6. 左側のツリービューの **[データ ゲートウェイ]** に **[adftutorialgateway]** と表示されます。クリックすると、関連する JSON が表示されます。 
	

### 手順 3: リンクされたサービスを作成する 
この手順では、**AzureStorageLinkedService** と **SqlServerLinkedService** の 2 つのリンク サービスを作成します。**SqlServerLinkedService** はオンプレミスの SQL Server Database をリンクし、**AzureStorageLinkedService** リンク サービスは Azure BLOB ストアをデータ ファクトリにリンクします。このチュートリアルの後半で、オンプレミスの SQL Server Database から Azure BLOB ストアにデータをコピーするパイプラインを作成します。

#### オンプレミスの SQL Server Database にリンクされたサービスを追加する
1.	**Data Factory エディター**のツール バーにある **[新しいデータ ストア]** クリックし、**[SQL Server]** を選択します。 

	![SQL Server のリンクされているサービス](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.	**JSON エディター**で次の操作を実行します。 
	1. **[gatewayName]** に **[adftutorialgateway]** を指定します。	
	2. Windows 認証を使用している場合:
		1. **[connectionString]**: 
			1. **[統合セキュリティ]** を **[true]** に設定します。
			2. データベースの**サーバー名**と**データベース名**を指定します。 
			2. **[ユーザー ID]** と **[パスワード]** を削除します。 
		3. **[userName]** と **[password]** の各プロパティにユーザー名とパスワードを指定します。  
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication. Example: <domain>\<user>",
            		"password": "<Specify password for the user account>"
        		}
                
            > [AZURE.NOTE] Windows 認証を使用している場合 (IntegratedSecurity=true)、ユーザー名とパスワードの指定は任意です。これらのプロパティが指定されなかった場合、Data Management Gateway は、ゲートウェイ コンピューターにログインしているユーザーの資格情報を使用してデータベースにアクセスします。異なる資格情報をゲートウェイからデータベースへのアクセスに使用する場合は、ユーザー名とパスワードを明示的に指定してください。

	4. SQL 認証を使用している場合:
		1. データベースの **[connectionString]** にデータベースの**サーバー名**、**データベース名**、**ユーザー ID**、**パスワード**を指定します。       
		2. 末尾 2 つの JSON プロパティである **[userName]** と **[password]** を JSON から削除します。
		3. **gatewayName** プロパティの値を指定する行末の **, (コンマ)** を削除します。 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	
		資格情報は、Data Factory サービスで所有する証明書によって**暗号化**されます。Data Management Gateway に関連付けられている証明書を使用する場合は、「[Set credentials securely (資格情報を安全に設定する)](#set-credentials-and-security)」を参照してください。
    
2.	コマンド バーの **[デプロイ]** をクリックして、SQL Server リンク サービスをデプロイします。

#### Azure ストレージ アカウント用のリンクされたサービスを追加する
 
1. **Data Factory エディター**で、コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure ストレージ]** をクリックします。
2. Azure ストレージ アカウントの名前を **[アカウント名]** に入力します。
3. Azure ストレージ アカウントのキーを **[アカウント キー]** に入力します。
4. **[デプロイ]** をクリックして **AzureStorageLinkedService** をデプロイします。
   
 
### 手順 4: 入力データセットと出力データセットを作成する
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
	- **linkedServiceName** を **AzureStorageLinkedService** (手順 2. で作成したリンク サービス) に設定します。
	- **folderPath** を **adftutorial/outfromonpremdf** に設定します。outfromonpremdf は adftutorial コンテナー内のフォルダーです。必要な操作は **adftutorial** コンテナーの作成だけです。
	- **availability** が **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。Data Factory サービスは、Azure SQL Database 内の **emp** テーブルに 1 時間ごとに出力データ スライスを生成します。 

	**入力テーブル**に **fileName** を指定しない場合、入力フォルダー (**folderPath**) のすべてのファイルまたは BLOB が入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。例については、[チュートリアル][adf-tutorial]のサンプル ファイルを参照してください。
 
	**出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されるファイルには Data.<Guid>.txt という形式で名前が付けられます (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

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
  

### 手順 5: パイプラインを作成して実行する
この手順では、**EmpOnPremSQLTable** を入力として使用し、**OutputBlobTable** を出力として使用する**コピー アクティビティ**を 1 つ使用する**パイプライン**を作成します。

1.	**[Data Factory]** ブレードで、**[作成とデプロイ]** タイルをクリックして、Data Factory の**エディター**を起動します。

	![タイルの作成とデプロイ](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
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
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	以下の点に注意してください。
 
	- activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。
	- アクティビティの**入力**を **EmpOnPremSQLTable** に設定し、**出力**を **OutputBlobTable** に設定します。
	- **transformation** セクションでは、**ソースの種類**として **SqlSource** を指定し、**シンクの種類**として **BlobSink** を指定します。
	- **SqlSource** の **sqlReaderQuery** プロパティに、SQL クエリ "**select * from emp**" を指定します。

	**start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。start と end の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。**end** の時刻は省略可能ですが、このチュートリアルでは使用します。
	
	**end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9/9/9999** を **end** プロパティの値として指定します。
	
	Azure Data Factory テーブルごとに定義された **Availability** プロパティに基づいて、データ スライスを処理する期間を定義します。
	
	上記の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。
	
2. コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。タイトル バーに "**パイプラインは正常にデプロイされました**" というメッセージが表示されていることを確認します。
5. **[X]** をクリックして **[エディター]** ブレードを閉じます。もう一度 **[X]** をクリックして、[ADFTutorialDataFactory] ブレードをツール バーおよびツリー ビューと共に閉じます。**"保存されていない編集は破棄されます"** というメッセージが表示されたら、**[OK]** をクリックします。
6. **ADFTutorialOnPremDF** の **[DATA FACTORY]** ブレードに戻ります。

**ご利用ありがとうございます。** これで、Azure Data Factory、リンクされたサービス、テーブル、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。

#### ダイアグラム ビューでの Data Factory の表示 
1. **Azure ポータル**の **ADFTutorialOnPremDF** データ ファクトリのホーム ページで、**[ダイアグラム]** タイルをクリックします。

	![ダイアグラム リンク](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. 以下のような図が表示されるはずです。

	![ダイアグラム ビュー](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	パイプラインとテーブルは、拡大、縮小、100% に拡大、ウィンドウのサイズに合わせて大きさを変更、自動的に配置などの表示が可能です。また、系列情報を表示 (選択した項目の上位項目や下位項目を強調表示) することもできます。オブジェクト (入力/出力テーブルまたはパイプライン) をダブルクリックすると、そのオブジェクトのプロパティを表示できます。

### 手順 6: データセットとパイプラインを監視する
このステップでは、Azure ポータルを使用して、Azure Data Factory の状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。監視の詳細については、「[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」という記事を参照してください。

1. **Azure ポータル**に移動します (閉じていた場合)。
2. **ADFTutorialOnPremDF** のブレードが開いていない場合は、**スタート画面**で **ADFTutorialOnPremDF** をクリックして開きます。
3. このブレードには、作成したテーブルとパイプラインの**数**と**名前**が表示されます。

	![Data Factory ホーム ページ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. **[データセット]** タイルをクリックします。
5. **[データセット]** ブレードで、**[EmpOnPremSQLTable]** をクリックします。

	![EmpOnPremSQLTable スライス](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. 現在の時刻までのデータ スライスが既に生成されており、**[準備完了]** になっています。これは、SQL Server Database に挿入したデータが、現在まで残っているためです。下部の **[問題のあるスライス]** セクションにスライスが表示されていないことを確認します。


	**[最近更新したスライス]** と **[最近失敗したスライス]** の一覧は、どちらも **[最終更新時刻]** で並べ替えられます。次の状況では、スライスの更新時刻が変更されます。
    

	-  **Set-AzureRmDataFactorySliceStatus** を使用したり、スライスの **[スライス]** ブレードで **[実行]** をクリックしたりすることで、スライスの状態を手動で更新した場合。
	-  スライスの実行 (実行の開始、実行の終了と失敗、実行の終了と成功など) により、スライスの状態が変わります。
 
	一覧のタイトルをクリックするか、**[...] \(省略記号)** をクリックすると、さらに多くのスライスが一覧表示されます。スライスをフィルター処理するには、ツール バーの **[フィルター]** をクリックします。
	
	代わりに、スライスの開始時刻と終了時刻で並べ替えられたデータ スライスを表示するには、**[データ スライス (スライスの時刻別)]** タイルをクリックします。

7. **[データセット]** ブレードで、**[OutputBlobTable]** をクリックします。

	![OputputBlobTable スライス][image-data-factory-output-blobtable-slices]
8. 現在の時刻までのスライスが生成されており、**[準備完了]** 状態であることを確認します。現在の時刻までのスライスの状態が **[準備完了]** になるまで待ちます。
9. 一覧の任意のデータ スライスをクリックすると、**[データ スライス]** ブレードが表示されます。

	![[データ スライス] ブレード](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	スライスが **[準備完了]** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、**[準備完了でない上位スライス]** の一覧に表示されます。

10. 下部にある一覧の **[アクティビティの実行]** をクリックして、**[アクティビティの実行の詳細]** を表示します。

	![[アクティビティの実行の詳細] ブレード][image-data-factory-activity-run-details]

11. **[X]** をクリックしてすべてのブレードを閉じ、
12. **ADFTutorialOnPremDF** のホーム ブレードに戻ります。
14. (省略可能) **[パイプライン]** をクリックし、**[ADFTutorialOnPremDF]** をクリックして、入力テーブル (**Consumed**) または出力テーブル (**Produced**) をドリル スルーします。
15. **Azure Storage Explorer** などのツールを使用して、出力を確認します。

	![Azure Storage エクスプローラー](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

以降のセクションでは、Data Management Gateway について詳しく取り上げます。

## Data Management Gateway を使用する上で考慮すること
1.	Data Management Gateway の 1 つのインスタンスを複数のオンプレミス データ ソースに使用できますが、**1 つの Gateway インスタンスはただ 1 つの Azure Data Factory に関連付けられており**、別の Data Factory と共有することはできないことに注意してください。
2.	1 台のコンピューターには、**Data Management Gateway のインスタンスを 1 つだけ**インストールできます。例えば、オンプレミスのデータ ソースにアクセスする必要があるデータ ファクトリが 2 つあるとすると、オンプレミスのコンピューター 2 台にゲートウェイをインストールし、各ゲートウェイを別々のデータ ファクトリに結び付ける必要があります。
3.	**ゲートウェイはデータ ソースと同じコンピューター上に存在する必要はありませんが**、データ ソースの近くにあると、ゲートウェイがデータ ソースに接続する際の時間が短縮されます。ゲートウェイとデータ ソースの間でリソースの競合が発生しないように、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターにゲートウェイをインストールすることをお勧めします。
4.	**同じオンプレミス データ ソースに接続する異なるコンピューターで複数のゲートウェイを使用**できます。たとえば、2 つのデータ ファクトリを提供する 2 つのゲートウェイがあり、どちらのデータ ファクトリにも同じオンプレミス データ ソースが登録されている場合があります。
5.	**Power BI** のシナリオを提供するゲートウェイがコンピューターに既にインストールされている場合は、**Azure Data Factory 用のゲートウェイ**を別のコンピューターにインストールしてください。
6.	**ExpressRoute を使用する場合でも、Gateway を使用する**必要があります。 
7.	**ExpressRoute** を使用する場合であっても (ファイアウォールの背後にある) オンプレミスのデータ ソースとしてデータ ソースを取り扱い、**Gateway を使用して**サービスとデータ ソースの間の接続を確立する必要があります。 

## Data Management Gateway のインストール

### ゲートウェイのインストール - 前提条件
1.	サポートされている**オペレーティング システム**のバージョンは、Windows 7、Windows 8/8.1、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 です。ドメイン コントローラーへの Data Management Gateway のインストールは現在サポートされていません。
2.	ゲートウェイ コンピューターの推奨される最小限の**構成**は、2 GHz、4 コア、8 GB の RAM、80 GB のディスクです。
3.	ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答できません。そのため、ゲートウェイをインストールする前に、コンピューターの適切な**電源プラン**を構成します。コンピューターが休止状態に構成されている場合、ゲートウェイのインストール時にメッセージが表示されます。

コピー アクティビティは特定の頻度で実行するので、コンピューターのリソース (CPU、メモリ) の使用も同じピークとアイドルのパターンに従います。リソース使用率はまた、移動されるデータの量に大きく依存します。複数のコピー ジョブが進行中のとき、ピーク時にはリソース使用率が上昇するのがわかります。上記は最小限の構成ですが、データ移動の特定の負荷に応じて、最小構成より多くのリソースを構成することを常にお勧めします。

### インストール
上のチュートリアルでは、Data Management Gateway の MSI ファイルをダウンロードして実行する方法を見てきました。

Data Management Gateway は、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から MSI セットアップ パッケージをダウンロードしてインストールすることもできます。

MSI は、すべての設定を保持しながら、既存の Data Management Gateway を最新のバージョンにアップグレードするためにも使用できます。

### インストールのベスト プラクティス:
1.	コンピューターが休止状態にならないように、ゲートウェイ用のホスト コンピューターの電源プランを構成します。ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答できません。
2.	ゲートウェイに関連付けられている証明書をバックアップする必要があります。

## Data Management Gateway の更新
既定では、Data Management Gateway の新しいバージョンが利用可能になると、自動的に更新されます。ゲートウェイは、すべてのスケジュールされたタスクが完了するまで、更新されません。更新操作が完了するまで、ゲートウェイは追加のタスクを処理しません。更新が失敗した場合、ゲートウェイは古いバージョンにロールバックします。

更新時刻のスケジュールは、ポータルの [ゲートウェイ プロパティ] ブレード、Data Management Gateway 構成マネージャーのホーム ページ、およびシステム トレイ通知メッセージに表示されます。今すぐ更新をインストールするオプションと、スケジュールされた時刻にゲートウェイが自動的に更新されるまで待つオプションがあります。たとえば、次のスクリーンショットは Data Management Gateway 構成マネージャーに表示される通知メッセージと、すぐに更新をインストールする場合にクリックする [更新] ボタンを示しています。

![DMG 構成マネージャーの更新](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-config-manager.png)

システム トレイの通知メッセージは、次のようになります。

![システム トレイのメッセージ](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-tray-message.png)

システム トレイに更新操作 (手動または自動) の状態が表示されます。次に Data Management Gateway 構成マネージャーを開くと、通知バーにゲートウェイが更新されたことを示すメッセージと、[最新情報のトピック](data-factory-gateway-release-notes.md)へのリンクが表示されます。

Data Management Gateway 構成マネージャーの [更新] タブには、更新スケジュールと、ゲートウェイが最後にインストール/更新された時刻が表示されます。自動更新が無効になっている場合はそれに関するメッセージが表示されますが、タブでこの機能を有効にすることはできません。コマンドレットを使用して機能を有効にする必要があります。
  

## システム トレイ アイコン/通知
次の図は、表示されるトレイ アイコンの一部を示します。

![システム トレイのアイコン](./media/data-factory-move-data-between-onprem-and-cloud/gateway-tray-icons.png)

システム トレイ アイコン/通知メッセージの上にカーソルを移動すると、ゲートウェイ/更新操作の状態に関する詳細がポップアップ ウィンドウに表示されます。

## 自動更新機能を無効/有効にするには
次の手順で、自動更新機能を無効/有効にすることができます。

1. ゲートウェイ コンピューターで管理者として Windows **PowerShell** を起動します (**[管理者として実行]**)。 
2. C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript フォルダーに切り替えます。
3. 自動更新機能をオフ (無効) にするには、次のコマンドを実行します。   

		.\GatewayAutoUpdateToggle.ps1  -off

4. オンに戻すには、次のコマンドを実行します。
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## ポートとセキュリティに関する考慮事項
考慮する必要がある 2 つのファイアウォールがあります。組織の中央ルーターで実行している**企業ファイアウォール**と、ゲートウェイがインストールされているローカル コンピューター上のデーモンとして構成されている **Windows ファイアウォール**です。

![ファイアウォール](./media/data-factory-move-data-between-onprem-and-cloud/firewalls.png)

### クラウド サービスとゲートウェイの接続
Azure Data Factory とその他のクラウド サービスとのゲートウェイの接続を維持するには、**TCP** ポート **80** と **443** の送信規則が構成されていることを確認する必要があります。任意でポート **9350** から **9354**、および **5671** を有効にします。これらのポートは Microsoft Azure Service Bus が Azure Data Factory と Data Management Gateway の間の接続を確立し、その間の通信のパフォーマンスを向上させるために使用することができます。

企業ファイアウォール レベルで、次のドメインと送信ポートを構成する必要があります。

| ドメイン名 | ポート | 説明 |
| ------ | --------- | ------------ |
| **.servicebus.windows.net | 443, 80 | TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) | | *.servicebus.windows.net | 9350-9354, 5671 | TCP 経由での任意の Service Bus Relay| | *.core.windows.net | 443 | HTTPS | | *.clouddatahub.net | 443 | HTTPS | | graph.windows.net | 443 | HTTPS | | login.windows.net | 443 | HTTPS | 

Windows のファイアウォール レベルでは、通常これらの送信ポートが有効になっています。有効でない場合は、ゲートウェイ コンピューターに応じたドメインとポートを構成することができます。

### 資格情報の設定
Azure ポータルにオンプレミスでリンクされたサービスをセットアップする場合、ゲートウェイに資格情報をリレーするために、**[資格情報の設定]** アプリケーションが受信ポート **8050** を使用します (記事の後半で説明します)。既定では、ゲートウェイのセットアップ中に、Data Management Gateway のインストールによってこのポートがゲートウェイ コンピューターで開きます。
 
サードパーティ製のファイアウォールを使用する場合は、ポート 8050 を手動で開くことができます。ゲートウェイのセットアップ中にファイアウォールの問題が発生した場合は、次のコマンドを使用して、ファイアウォールを構成せずにゲートウェイをインストールすることができます。

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

ゲートウェイ コンピューター上でポート 8050 を開かず、オンプレミスでリンクされたサービスをセットアップする場合は、**[資格情報の設定]** アプリケーション以外のメカニズムを使用して、データ ストア資格情報を構成する必要があります。たとえば、[New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell コマンドレットを使用できます。データ ストア資格情報を設定する方法については、「[資格情報とセキュリティの設定](#set-credentials-and-securityy)」セクションを参照してください。

**ソース データ ストアからシンク データ ストアにデータをコピーするには:**

ファイアウォール ルールが企業ファイアウォール上で、Windows ファイアウォールがゲートウェイ コンピューター上で適切に有効化されていること、およびデータ ストア自体が適切に有効化されていることを確認する必要があります。これにより、ゲートウェイはソースとシンクの両方に正常に接続されます。コピー操作に関連するデータ ストアごとにルールを有効にする必要があります。

たとえば、**Azure SQL Database シンクまたは Azure SQL Data Warehouse シンクにオンプレミスのデータ ストア**からコピーするために、送信 **TCP** 通信を Windows ファイアウォールと企業ファイアウォールの両方に対してポート **1433** 上で許可する必要があり、許可された IP アドレスの一覧にゲートウェイ コンピューターの IP アドレスを追加するために、Azure SQL サーバーのファイアウォール設定を構成する必要があります。

### プロキシ サーバーに関する考慮事項
既定では、Data Management Gateway は、Internet Explorer からプロキシ設定を利用し、アクセスするために既定の資格情報を使用します。このケースに合わない場合は、ゲートウェイが Azure Data Factory に接続できることを確認するために、次に示すように **[プロキシ サーバー設定]** をさらに構成できます。

1.	ファイル エクスプ ローラーで、Data Management Gateway をインストールした後に、"C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config" の安全なコピーを作成し、元のファイルをバックアップします。
2.	管理者として Notepad.exe を起動し、テキスト ファイル "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config" を開きます。次のように system.net に対して既定のタグが表示されます。

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	その後、プロキシ サーバーの詳細 (例: その親タグ内のプロキシ アドレス) を追加できます。例:

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	追加のプロパティは、プロキシ タグ内で scriptLocation のように必要な設定を指定することが許可されます。構文の[プロキシ要素 (ネットワーク設定)](https://msdn.microsoft.com/library/sa91de1e.aspx) を参照してください。

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. 構成ファイルを元の場所に保存し、 Data Management Gateway サービスを再起動し、変更を取得します。これを行うには、**[スタート]**、**[Services.msc]** から、または **[Data Management Gateway 構成マネージャー]**、**[サービスの停止]** ボタンの順にクリックしてから、**[サービスの開始]** をクリックします。サービスが開始しない場合は、不正確な XML タグ構文が編集済みのアプリケーション構成ファイルに追加されている可能性があります。

上記の点に加えて、Microsoft Azure が会社のホワイトリストにあることを確認する必要もあります。Microsoft Azure の有効な IP アドレスの一覧は、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=41653)からダウンロードできます。

### ファイアウォールとプロキシ サーバー関連の問題で発生する可能性がある症状:
次のようなエラーが発生した場合は、ファイアウォールまたはプロキシ サーバーの不適切な構成により、Data Management Gateway が自身を認証するための Azure Data Factory への接続からブロックされる可能性があります。ファイアウォールとプロキシ サーバーが正しく構成されていることを確認するには、前のセクションを参照してください。

1.	ゲートウェイを登録しようとすると次のエラーが発生します。「ゲートウェイのキーを登録できませんでした。ゲートウェイ キーの登録を再試行する前に、Data Management Gateway が接続状態で、Data Management Gateway Host Service が起動していることを確認してください。」
2.	構成マネージャーを開くと、ステータスは「切断」または「接続」と表示されます。[イベント ビューアー]、[アプリケーションとサービス ログ]、[Data Management Gateway] で Windows イベント ログを表示すると、「リモート サーバーに接続できません」や「Data Management Gateway のコンポーネントが応答しなくなり、自動的に再起動されます。コンポーネント名: Gateway」といったエラー メッセージが表示されます。

## ゲートウェイの問題のトラブルシューティング


- 詳細な情報は、Windows イベント ログのゲートウェイ ログで確認できます。Windows の **[イベント ビューアー]**、**[アプリケーションとサービス ログ]**、**[Data Management Gateway]** を使用してログを確認できます。ゲートウェイ関連の問題のトラブルシューティングでは、イベント ビューアーでエラー レベルのイベントを調べてください。
- **証明書を変更**した後でゲートウェイの動作が停止した場合は、Microsoft Data Management Gateway 構成マネージャー ツールまたは [サービス] コントロール パネル アプレットを使って **Data Management Gateway サービス**を再起動 (停止してから起動) します。エラーが表示された場合は、Data Management Gateway サービスのユーザーが証明書マネージャー (certmgr.msc) で証明書にアクセスするための明示的なアクセス許可を付与する必要がある場合があります。サービスの既定のユーザー アカウントは、**NT Service\\DIAHostService** です。 
- データ ストア接続またはドライバーに関係するエラーがある場合は、ゲートウェイ コンピューター上で **Data Management Gateway 構成マネージャー**を起動し、**[診断]** タブをクリックして、**[このゲートウェイを使用してオンプレミスのデータ ソースへの接続をテストします]** グループの各フィールドで適切な値を選択または入力します。**[接続テスト]** をクリックし、接続情報と資格情報を使用して、ゲートウェイ コンピューターからオンプレミスのデータ ソースに接続できることを確認します。ドライバーのインストール後も接続テストが失敗する場合は、最新の変更を認識できるようにゲートウェイを再起動します。  

	![接続テスト](./media/data-factory-move-data-between-onprem-and-cloud/TestConnection.png)
		

## コンピューターから別のコンピューターへのゲートウェイの移動
このセクションでは、1 台のコンピューターから別のコンピューターにゲートウェイ クライアントを移動する手順を説明します。

2. ポータルで **Data Factory ホーム ページ**に移動し、**[リンクされたサービス]** タイルをクリックします。 

	![データ ゲートウェイ リンク](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. **[リンクされたサービス]** ブレードの **[データ ゲートウェイ]** セクションでゲートウェイを選択します。
	
	![ゲートウェイが選択された状態の [リンクされたサービス] ブレード](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. **[データ ゲートウェイ]** ブレードで **[データ ゲートウェイをダウンロードしてインストールする]** をクリックします。
	
	![ダウンロード ゲートウェイ リンク](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. **[構成]** ブレードで **[データ ゲートウェイをダウンロードしてインストールする]** をクリックし、指示に従ってコンピューターにデータ ゲートウェイをインストールします。 

	![[構成] ブレード](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. **Microsoft Data Management Gateway Configuration Manager** を開いたままにします。 
 
	![構成マネージャー](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)	
7. ポータルの **[構成]** ブレードでコマンド バーの **[キーの再作成]** をクリックし、警告メッセージで **[はい]** をクリックします。キー文字列の横にある **[コピー]** ボタンをクリックして、キーをクリップボードにコピーします。古いコンピューターのゲートウェイは、キーが再作成されるとすぐに機能を停止することに注意してください。  
	
	![キーの再作成](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
	 
8. **キー**をコンピューター上の **Data Management Gateway Configuration Manager** の **[ゲートウェイの登録]** ページにあるテキスト ボックスに貼り付けます。(省略可能) **[ゲートウェイのキーを表示する]** チェック ボックスをオンにしてキー文字列を表示します。
 
	![キーのコピーと登録](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. **[登録]** をクリックして、ゲートウェイをクラウド サービスに登録します。
10. **[証明書の指定]** ページで **[参照]** をクリックして古いゲートウェイで使用されていたのと同じ証明書を選択し、**[パスワード]** を入力して **[完了]** をクリックします。 
 
	![証明書の指定](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

	古いゲートウェイから証明書をエクスポートするには、次の手順に従います。古いコンピューターで Data Management Gateway Configuration Manager を起動し、**[証明書]** タブに切り替え、**[エクスポート]** をクリックし、指示に従います。 
10. ゲートウェイの登録が成功したら、Gateway Configuration Manager のホーム ページで、**[登録]** が **[登録済み]** に、**[状態]** が **[開始]** に設定されていることを確認する必要があります。 

## 資格情報とセキュリティの設定
Data Factory エディターで資格情報を暗号化するには、次の操作を行います。

1. ツリー ビューの既存の**リンクされたサービス**をクリックしてその JSON 定義を参照するか、または Data Management Gateway を必要とするリンクされたサービスを新規に作成します (例: SQL Server または Oracle)。 
2. JSON エディターで、**gatewayName** プロパティに、ゲートウェイの名前を入力します。 
3. **connectionString** の **Data Source** プロパティにサーバー名を入力します。
4. **connectionString** の **Initial Catalog** プロパティにデータベース名を入力します。    
5. コマンド バーの **[暗号化]** をクリックします。**[資格情報の設定]** ダイアログ ボックスが表示されます。![[資格情報の設定] ダイアログ](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png)
6. **[資格情報の設定]** ダイアログ ボックスで、次の手順を実行します。  
	1.	Data Factory サービスがデータベースへの接続に使用する**認証**を選択します。 
	2.	**[ユーザー名]** の設定に、データベースへのアクセス権を持つユーザーの名前を入力します。 
	3.	**[パスワード]** の設定に、ユーザーのパスワードを入力します。  
	4.	**[OK]** をクリックして、資格情報を暗号化し、ダイアログ ボックスを閉じます。 
5.	これで、**connectionString** 内に **encryptedCredential** プロパティが表示されます。		
		
			{
	    		"name": "SqlServerLinkedService",
		    	"properties": {
		        	"type": "OnPremisesSqlServer",
			        "description": "",
		    	    "typeProperties": {
		    	        "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
		            	"gatewayName": "adftutorialgateway"
		        	}
		    	}
			}

ゲートウェイ コンピューターとは異なるコンピューターからポータルにアクセスする場合は、資格情報マネージャー アプリケーションがゲートウェイ コンピューターに接続できることを確認する必要があります。アプリケーションがゲートウェイ コンピューターにアクセスできない場合、データ ソースの資格情報を設定したり、データ ソースへの接続をテストしたりすることはできません。

Microsoft Azure ポータルから起動した **[資格情報の設定]** アプリケーションを使用してオンプレミスのデータ ソースの資格情報を設定すると、ポータルは、ユーザーがゲートウェイ コンピューターの **Data Management Gateway Configuration Manager** の **[証明書]** タブで指定した証明書で資格情報を暗号化します。

API を使用して資格情報を暗号化した方がよい場合は、[New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell コマンドレットを使用して資格情報を暗号化できます。コマンドレットはゲートウェイに構成されている証明書を使用して資格情報を暗号化します。このコマンドレットによって返される資格情報を、[New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) コマンドレットで使用する JSON ファイルの **connectionString** の **EncryptedCredential** 要素や、ポータルの Data Factory エディターで JSON スニペットに追加できます。

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Data Factory エディターを使用して資格情報を設定するもう 1 つの方法があります。エディターを使用して SQL Server のリンクされたサービスを作成し、プレーン テキストで資格情報を入力した場合、資格情報は、ゲートウェイが使用するように構成されている証明書ではなく、Data Factory が所有する証明書で暗号化されます。この方法は、場合によっては少し高速ですが、セキュリティは低下します。したがって、この方法は開発/テストの目的のみに使用することをお勧めします。


## Azure PowerShell を使用した Data Management Gateway の作成と登録 
このセクションでは、Azure PowerShell コマンドレットを使用して、ゲートウェイを作成し登録する方法について説明します。

1. 管理者モードで **Azure PowerShell** を起動します。 
2. 次のコマンドを実行して Azure 資格情報を入力することにより、Azure アカウントにログインします。 

	Login-AzureRmAccount
2. **New-AzureRmDataFactoryGateway** コマンドレットを使用して、次のように論理ゲートウェイを作成します。

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**コマンドと出力の例**:


		PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded
		Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI

	
4. Azure PowerShell で **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** フォルダーに移動します。次のコマンドに示すように、ローカル変数 **$Key** に関連付けられた **RegisterGateway.ps1** スクリプトを実行して、コンピューターにインストールされているクライアント エージェントを、前に作成した論理ゲートウェイに登録します。

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	IsRegisterOnRemoteMachine パラメーターを使用することで、リモート マシンにゲートウェイを登録できます。例:
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. **Get-AzureRmDataFactoryGateway** コマンドレットを使用して、データ ファクトリ内のゲートウェイの一覧を取得できます。**[状態]** に **[オンライン]** と表示されている場合、ゲートウェイをいつでも使用できることを意味します。

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

**Remove-AzureRmDataFactoryGateway** コマンドレットを使用して、ゲートウェイを削除できます。また、**Set-AzureRmDataFactoryGateway** コマンドレットを使用して、ゲートウェイの記述を更新できます。これらのコマンドレットの文法やその他の詳細については「Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)」を参照してください。

## PowerShell を使用したゲートウェイの一覧表示

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

## PowerShell を使用したゲートウェイの削除
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 

<!---HONumber=AcomDC_0601_2016-->