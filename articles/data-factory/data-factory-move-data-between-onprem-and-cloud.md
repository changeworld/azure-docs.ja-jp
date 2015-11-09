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
	ms.date="10/23/2015" 
	ms.author="spelluru"/>

# Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する
最新のデータ統合の課題の 1 つは、オンプレミスとクラウドの間でシームレスにデータを移動することです。Data Factory は、Data Management Gateway によってこの統合をシームレスにします。Data Factory Management Gateway はオンプレミスにインストールできるエージェントで、ハイブリッド パイプラインを可能にします。

この記事では、オンプレミスのデータ ストアとクラウド データ ストアの統合および Data Factory を使用したクラウド処理の概要について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」および Data Factory のコア概念に関するその他の記事が基になっています。次の概要では、パイプライン、アクティビティ、データセット、コピー アクティビティなどの Data Factory の概念は既に理解しているものとします。

Data Gateway には次の機能が備わっています。

1.	同じ Data Factory 内にオンプレミスのデータ ソースとクラウドのデータ ソースをモデル化して、データを移動します。
2.	Data Factory のクラウド ダッシュボードにゲートウェイのステータスを表示して、一元的に監視および管理を行います。
3.	オンプレミスのデータ ソースへの安全なアクセスを管理します。
	1. 企業のファイアウォールを変更する必要はありません。Gateway は、インターネットを開くために送信 HTTP ベースの接続のみを行います。
	2. 証明書でオンプレミスのデータ ストアの資格情報を暗号化します。
4.	データを効率的に移動します。データは並列に転送されます。自動再試行ロジックにより、断続的なネットワークの問題にも対応できます。

## Data Management Gateway を使用する上で考慮すること
1.	Data Management Gateway の 1 つのインスタンスを複数のオンプレミス データ ソースに使用できますが、**1 つの Gateway インスタンスはただ 1 つの Azure Data Factory に関連付けられており**、別の Data Factory と共有することはできないことに注意してください。
2.	1 台のコンピューターには、**Data Management Gateway のインスタンスを 1 つだけ**インストールできます。例えば、オンプレミスのデータ ソースにアクセスする必要があるデータ ファクトリが 2 つあるとすると、オンプレミスのコンピューター 2 台にゲートウェイをインストールし、各ゲートウェイを別々のデータ ファクトリに結び付ける必要があります。
3.	**ゲートウェイはデータ ソースと同じコンピューター上に存在する必要はありませんが**、データ ソースの近くにあると、ゲートウェイがデータ ソースに接続する際の時間が短縮されます。ゲートウェイとデータ ソースの間でリソースの競合が発生しないように、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターにゲートウェイをインストールすることをお勧めします。
4.	**同じオンプレミス データ ソースに接続する異なるコンピューターで複数のゲートウェイを使用**できます。たとえば、2 つのデータ ファクトリを提供する 2 つのゲートウェイがあり、どちらのデータ ファクトリにも同じオンプレミス データ ソースが登録されている場合があります。
5.	**Power BI** のシナリオを提供するゲートウェイがコンピューターに既にインストールされている場合は、**Azure Data Factory 用のゲートウェイ**を別のコンピューターにインストールしてください。
6.	**ExpressRoute を使用する場合でも、Gateway を使用する**必要があります。 
7.	**ExpressRoute** を使用する場合であっても (ファイアウォールの背後にある) オンプレミスのデータ ソースとしてデータ ソースを取り扱い、**Gateway を使用して**サービスとデータ ソースの間の接続を確立する必要があります。 

## ゲートウェイのインストール - 前提条件
1.	サポートされている**オペレーティング システム**のバージョンは、Windows 7、Windows 8/8.1、Windows Server 2008 R2、Windows Server 2012 です。
2.	ゲートウェイ コンピューターの推奨される最小限の**構成**は、2 GHz、4 コア、8 GB の RAM、80 GB のディスクです。
3.	ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答できません。そのため、ゲートウェイをインストールする前に、コンピューターの適切な**電源プラン**を構成します。コンピューターが休止状態に構成されている場合、ゲートウェイのインストール時にメッセージが表示されます。

コピー アクティビティは特定の頻度で実行するので、コンピューターのリソース (CPU、メモリ) の使用も同じピークとアイドルのパターンに従います。リソース使用率はまた、移動されるデータの量に大きく依存します。複数のコピー ジョブが進行中のとき、ピーク時にはリソース使用率が上昇するのがわかります。上記は最小限の構成ですが、データ移動の特定の負荷に応じて、最小構成より多くのリソースを構成することを常にお勧めします。

## インストール
Data Management Gateway は、Microsoft ダウンロード センターから MSI セットアップ パッケージをダウンロードしてインストールできます。MSI は、すべての設定を保持しながら、既存の Data Management Gateway を最新のバージョンにアップグレードするためにも使用できます。以下の手順に従って、Azure ポータルから MSI パッケージへのリンクを見つけることができます。

### インストールのベスト プラクティス:
1.	コンピューターが休止状態にならないように、ゲートウェイ用のホスト コンピューターの電源プランを構成します。ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答できません。
2.	ゲートウェイに関連付けられている証明書をバックアップする必要があります。

### インストールのトラブルシューティング:
ファイアウォールまたはプロキシ サーバーを使用している場合、Data Management Gateway が Microsoft クラウド サービスに接続できないときは追加の手順が必要になることがあります。

#### イベント ビューアーでの Gateway ログの確認:

Gaetway 構成マネージャー アプリケーションでは、「切断」または「接続」のようにゲートウェイのステータスが表示されます。

さらに詳細な情報は、Windows イベント ログのゲートウェイ ログで確認できます。Windows の **[イベント ビューアー]**、**[アプリケーションとサービス ログ]**、**[Data Management Gateway]** を使用してログを確認できます。ゲートウェイ関連の問題のトラブルシューティングでは、イベント ビューアーでエラー レベルのイベントを調べてください。


#### ファイアウォール関連の問題で発生する可能性がある症状:

1. ゲートウェイを登録しようとすると次のエラーが発生します。「ゲートウェイのキーを登録できませんでした。ゲートウェイ キーの登録を再試行する前に、Data Management Gateway が接続状態で、Data Management Gateway Host Service が起動していることを確認してください。」
2. 構成マネージャーを開くと、ステータスは「切断」または「接続」と表示されます。[イベント ビューアー]、[アプリケーションとサービス ログ]、[Data Management Gateway] で Windows イベント ログを表示すると、「リモート サーバーに接続できません」や「Data Management Gateway のコンポーネントが応答しなくなり、自動的に再起動されます。コンポーネント名: Gateway」といったエラー メッセージが表示されます。

これらの原因はファイアウォールまたはプロキシ サーバーの不適切な構成であり、Data Management Gateway がクラウド サービスに接続して認証するのをブロックしています。

スコープ内にある可能性のある 2 つのファイアウォールは、組織の中央ルーターで実行している企業ファイアウォールと、ゲートウェイがインストールされているローカル コンピューター上のデーモンとして構成されている Windows ファイアウォールです。次に、いくつか考慮事項を示します。

- 企業ファイアウォールの受信ポリシーを変更する必要はありません。
- 企業ファイアウォールと Windows ファイアウォールはどちらも、TCP ポート 80、440、9305 ～ 9354 での送信ルールを有効にする必要があります。これらは、クラウド サービスと Data Management Gateway の間の接続を確立するために Microsoft Azure Service Bus で使用されます。

MSI のセットアップでは、ゲートウェイ コンピューターに対する受信ポートの Windows ファイアウォール ルールが自動的に構成されます (前の「ポートとセキュリティに関する考慮事項」を参照)。

ただし、セットアップでは、上記の送信ポートがローカル コンピューターと企業ファイアウォールにおいて既定で許可されるものと仮定しています。そうでない場合は、これらの送信ポートを有効にする必要があります。サードパーティ製のファイアウォールで Windows ファイアウォールを置き換えた場合は、これらのポートを手動で開く必要がある場合があります。

会社でプロキシ サーバーを使用している場合は、Microsoft Azure をホワイト リストに追加する必要があります。Microsoft Azure の有効な IP アドレスの一覧は、[Microsoft ダウンロード センター](http://msdn.microsoft.com/library/windowsazure/dn175718.aspx)からダウンロードできます。

## Data Gateway の使用手順
このチュートリアルでは、オンプレミスの SQL Server Database から、Azure BLOB にデータを移動するパイプラインを備えたデータ ファクトリを作成します。

### 手順 1. Azure Data Factory を作成する
この手順では、Microsoft Azure 管理ポータルを使用して **ADFTutorialOnPremDF** という名前の Azure Data Factory インスタンスを作成します。Azure Data Factory のコマンドレットを使用してデータ ファクトリを作成することもできます。

1.	[Azure プレビュー ポータル](https://portal.azure.com)にログインしたら、左下隅にある **[新規]** をクリックします。**[作成]** ブレードで **[データ分析]** を選択し、**[データ分析]** ブレードで **[Data Factory]** をクリックします。

	![New->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
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

	![NOTIFICATIONS hub](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. 作成が完了すると、次に示すような **[Data Factory]** ブレードが表示されます。

	![Data Factory Home Page](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### 手順 2. Data Management Gateway を作成する
5. **[Data Factory]** ブレードで、**[作成とデプロイ]** タイルをクリックして、Data Factory の**エディター**を起動します。

	![タイルの作成とデプロイ](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	Data Factory エディターのツールバーの **[...] (省略記号)** をクリックし、**[新しいデータ ゲートウェイ]** をクリックします。 

	![ツールバーでのデータ ゲートウェイの新規作成](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. **[作成]** ブレードで、**名前**に「**adftutorialgateway**」と入力し、**[OK]** をクリックします。 	

	![Create Gateway blade](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. **[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。これにより、ゲートウェイのインストール パッケージのダウンロードと、コンピューターへのインストール、構成、および登録が行われます。

	> [AZURE.NOTE]Internet Explorer または Microsoft の ClickOnce と互換性のある Web ブラウザーを使用してください。

	![Gateway - Configure blade](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	これは、たった 1 つの手順 (クリック 1 回) でゲートウェイのダウンロード、インストール、構成、および登録を行う、最も簡単な方法です。**Microsoft Data Management Gateway 構成マネージャー** アプリケーションがコンピューターにインストールされていることがわかります。実行可能ファイル **ConfigManager.exe** は **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** フォルダーにあります。

	このブレード内のリンクを使用してゲートウェイのダウンロードとインストールを手動で行い、**[キーで登録]** ボックスに表示されるキーを使用して登録することもできます。
	
	ベスト プラクティスや重要な考慮事項など、ゲートウェイの詳細については、この記事の最初の方のセクションを参照してください。

	>[AZURE.NOTE]Data Management Gateway を正常にインストールして構成するには、ローカル コンピューターの管理者である必要があります。他のユーザーをローカル Windows グループの Data Management Gateway Users に追加できます。このグループのメンバーは、Data Management Gateway 構成マネージャー ツールを使用して、ゲートウェイを構成できます。

5. 数分待ってから、コンピューターで **Data Management Gateway Configuration Manager** アプリケーションを起動します。**[検索]** ウィンドウで、「**Data Management Gateway**」と入力してこのユーティリティにアクセスします。**C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** フォルダーの実行可能ファイル **ConfigManager.exe** を検索することもできます。

	![Gateway Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. 値が以下の状態になるまで待ちます。
	1. **[状態]** が **[開始]** に設定されます。
	2. **[ゲートウェイ名]** が **[adftutorialgateway]** になります。
	3. **[インスタンス名]** が **[adftutorialgateway]** になります。
	4. **[登録]** が **[登録済み]** に設定されます。
	5. 下部のステータス バーには、**緑色のチェック マーク**と共に "**Data Management Gateway クラウド サービスに接続済み**" と表示されます。

8. **[証明書]** に切り替えます。このタブで指定された証明書は、ポータルで指定したオンプレミス データ ストアの資格情報の暗号化/暗号解除に使用されます。代わりに独自の証明書を使用する場合は、**[変更]** をクリックします。既定では、ゲートウェイは Data Factory サービスによって自動生成される証明書を使用します。

	![ゲートウェイ証明書の構成](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. Azure ポータルの **[構成]** ブレードと **[新しいデータ ゲートウェイ]** ブレードで **[OK]** をクリックします。
6. 左側のツリー ビューの **[データ ゲートウェイ]** に **[adftutorialgateway]** と表示されます。クリックすると、関連する JSON が表示されます。 
	

### 手順 3. リンクされたサービスを作成する 
この手順では、**StorageLinkedService** と **SqlServerLinkedService** の 2 つのリンク サービスを作成します。**SqlServerLinkedService** はオンプレミスの SQL Server データベースをリンクし、**StorageLinkedService** リンク サービスは Azure BLOB ストアをデータ ファクトリにリンクします。このチュートリアルの後半で、オンプレミスの SQL Server Database から Azure BLOB ストアにデータをコピーするパイプラインを作成します。

#### オンプレミスの SQL Server Database にリンクされたサービスを追加する
1.	**Data Factory エディター**のツール バーにある **[新しいデータ ストア]** をクリックし、**[SQL Server]** を選択します。 

	![SQL Server のリンクされているサービス](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.	**JSON エディター**で次の操作を実行します。 
	1. **gatewayName** に **adftutorialgateway** を指定します。	
	2. Windows 認証を使用している場合:
		1. **connectionString**: 
			1. **Integrated Security** を **true** に設定します。
			2. データベースの**サーバー名**と**データベース名**を指定します。 
			2. **User ID** と **Password** を削除します。 
		3. **userName** と **password** の各プロパティにユーザー名とパスワードを指定します。
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication>",
            		"password": "<Specify password for the user account>"
        		}

	4. SQL 認証を使用している場合:
		1. **connectionString** にデータベースの**サーバー名**、**データベース名**、**ユーザー ID**、**パスワード**を指定します。       
		2. 末尾 2 つの JSON プロパティである **userName** と **password** を JSON から削除します。
		3. **gatewayName** プロパティの値を指定する行末の **, (コンマ)** を削除します。 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	   
2.	コマンド バーの **[デプロイ]** をクリックして、SQL Server リンク サービスをデプロイします。

#### Azure ストレージ アカウント用のリンクされたサービスを追加する
 
1. **Data Factory エディター**で、コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure ストレージ]** をクリックします。
2. Azure ストレージ アカウントの名前を **[アカウント名]** に入力します。
3. Azure ストレージ アカウントのキーを **[アカウント キー]** に入力します。
4. **[デプロイ]** をクリックして **StorageLinkedService** をデプロイします。
   
 
### 手順 4. 入力データセットと出力データセットを作成する
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
	- Azure Data Factory の別のパイプラインでは生成されない入力テーブルの場合、**external** を **true** に設定する必要があります。これは、入力データが Azure Data Factory サービスの外部で生成されることを意味します。必要に応じて、**Policy** セクションの **externalData** 要素を使用して外部データ ポリシーを指定できます。    

	JSON プロパティの詳細については、[JSON スクリプト リファレンス][json-script-reference]を参照してください。

2. コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。タイトル バーに "**テーブルは正常にデプロイされました**" というメッセージが表示されていることを確認します。


### 出力テーブルの作成

1.	**Data Factory エディター**で、コマンド バーの **[新しいデータセット]** をクリックし、**[Azure BLOB ストレージ]** をクリックします。
2.	右側のウィンドウの JSON を次のテキストに置き換えます。 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
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
	- **linkedServiceName** を **StorageLinkedService** (手順 2. で作成したリンク サービス) に設定します。
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
  

### 手順 5. パイプラインを作成して実行する
この手順では、**EmpOnPremSQLTable** を入力として使用し、**OutputBlobTable** を出力として使用する**コピー アクティビティ**を 1 つ使用する**パイプライン**を作成します。

1.	**[Data Factory]** ブレードで、**[作成とデプロイ]** タイルをクリックして、Data Factory の**エディター**を起動します。

	![タイルの作成とデプロイ](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.	コマンド バーの **[新しいパイプライン]** をクリックします。このボタンが表示されない場合は、**[...] (省略記号)** をクリックしてコマンド バーを展開します。
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
1. **Azure プレビュー ポータル**の **ADFTutorialOnPremDF** データ ファクトリのホーム ページで、**[ダイアグラム]** タイルをクリックします。

	![Diagram Link](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. 以下のような図が表示されるはずです。

	![Diagram View](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	パイプラインとテーブルは、拡大、縮小、100% に拡大、ウィンドウのサイズに合わせて大きさを変更、自動的に配置などの表示が可能です。また、系列情報を表示 (選択した項目の上位項目や下位項目を強調表示) することもできます。オブジェクト (入力/出力テーブルまたはパイプライン) をダブルクリックすると、そのオブジェクトのプロパティを表示できます。

### 手順 6. データセットとパイプラインを監視する
このステップでは、Azure ポータルを使用して、Azure Data Factory の状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。監視の詳細については、「[パイプラインの監視と管理](monitor-manage-pipelines.md)」を参照してください。

1. **Azure プレビュー ポータル**に移動します (閉じていた場合)。
2. **ADFTutorialOnPremDF** のブレードが開いていない場合は、**スタート画面**で **ADFTutorialOnPremDF** をクリックして開きます。
3. このブレードには、作成したテーブルとパイプラインの**数**と**名前**が表示されます。

	![Data Factory Home Page](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. **[データセット]** タイルをクリックします。
5. **[データセット]** ブレードで、**[EmpOnPremSQLTable]** をクリックします。

	![EmpOnPremSQLTable slices](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. 現在の時刻までのデータ スライスが既に生成されており、**[準備完了]** になっています。これは、SQL Server Database に挿入したデータが、現在まで残っているためです。下部の **[問題のあるスライス]** セクションにスライスが表示されていないことを確認します。


	**[最近更新したスライス]** と **[最近失敗したスライス]** の一覧は、どちらも **[最終更新時刻]** で並べ替えられます。次の状況では、スライスの更新時刻が変更されます。
    

	-  **Set-AzureDataFactorySliceStatus** を使用したり、スライスの **[スライス]** ブレードで **[実行]** をクリックしたりすることで、スライスの状態を手動で更新した場合。
	-  スライスの実行 (実行の開始、実行の終了と失敗、実行の終了と成功など) により、スライスの状態が変わります。
 
	一覧のタイトルをクリックするか、**[...] (省略記号)** をクリックすると、さらに多くのスライスが一覧表示されます。スライスをフィルター処理するには、ツール バーの **[フィルター]** をクリックします。
	
	代わりに、スライスの開始時刻と終了時刻で並べ替えられたデータ スライスを表示するには、**[データ スライス (スライスの時刻別)]** タイルをクリックします。

7. **[データセット]** ブレードで、**[OutputBlobTable]** をクリックします。

	![OputputBlobTable スライス][image-data-factory-output-blobtable-slices]
8. 現在の時刻までのスライスが生成されており、**[準備完了]** 状態であることを確認します。現在の時刻までのスライスの状態が **[準備完了]** になるまで待ちます。
9. 一覧の任意のデータ スライスをクリックすると、**[データ スライス]** ブレードが表示されます。

	![Data Slice Blade](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	スライスが **[準備完了]** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、**[準備完了でない上位スライス]** の一覧に表示されます。

10. 下部にある一覧の **[アクティビティの実行]** をクリックして、**[アクティビティの実行の詳細]** を表示します。

	![[アクティビティの実行の詳細]ブレード][image-data-factory-activity-run-details]

11. **[X]** をクリックしてすべてのブレードを閉じ、
12. **ADFTutorialOnPremDF** のホーム ブレードに戻ります。
14. (省略可能) **[パイプライン]** をクリックし、**[ADFTutorialOnPremDF]** をクリックして、入力テーブル (**Consumed**) または出力テーブル (**Produced**) をドリル スルーします。
15. **Azure ストレージ エクスプローラー**などのツールを使用して、出力を確認します。

	![Azure ストレージ エクスプローラー](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## コンピューターから別のコンピューターへのゲートウェイの移動
このセクションでは、1 台のコンピューターから別のコンピューターにゲートウェイ クライアントを移動する手順を説明します。

2. ポータルで **Data Factory ホーム ページ**に移動し、**[リンクされたサービス]** タイルをクリックします。 

	![Data Gateways Link](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. **[リンクされたサービス]** ブレードの **[データ ゲートウェイ]** セクションでゲートウェイを選択します。
	
	![Linked Services blade with gateway selected](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. **[データ ゲートウェイ]** ブレードで **[データ ゲートウェイをダウンロードしてインストールする]** をクリックします。
	
	![Download gateway link](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. **[構成]** ブレードで **[データ ゲートウェイをダウンロードしてインストールする]** をクリックし、指示に従ってコンピューターにデータ ゲートウェイをインストールします。 

	![Configure blade](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. **Microsoft Data Management Gateway Configuration Manager** を開いたままにします。 
 
	![Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)	
7. ポータルの **[構成]** ブレードでコマンド バーの **[キーの再作成]** をクリックし、警告メッセージで **[はい]** をクリックします。キー文字列の横にある **[コピー]** をクリックして、キーをクリップボードにコピーします。古いコンピューターのゲートウェイは、キーが再作成されるとすぐに機能を停止することに注意してください。  
	
	![Recreate key](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
	 
8. **キー**をコンピューター上の **Data Management Gateway Configuration Manager** の **[ゲートウェイの登録]** ページにあるテキスト ボックスに貼り付けます。(省略可能) **[ゲートウェイのキーを表示する]** チェック ボックスをオンにしてキー文字列を表示します。
 
	![Copy key and Register](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. **[登録]** をクリックして、ゲートウェイをクラウド サービスに登録します。
10. **[証明書の指定]** ページで **[参照]** をクリックして古いゲートウェイで使用されていたのと同じ証明書を選択し、**[パスワード]** を入力して **[完了]** をクリックします。 
 
	![Specify Certificate](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

	古いゲートウェイから証明書をエクスポートするには、次の手順に従います。古いコンピューターで Data Management Gateway Configuration Manager を起動し、**[証明書]** タブに切り替え、**[エクスポート]** をクリックし、指示に従います。 
10. ゲートウェイの登録が成功したら、Gateway Configuration Manager のホーム ページで、**[登録]** が **[登録済み]** に、**[状態]** が **[開始]** に設定されていることを確認する必要があります。 

## 資格情報とセキュリティの設定

SQL Server リンク サービスを作成するには、Data Factory エディターではなくリンクされたサービス ブレードを使用することができます。
 
3.	Data Factory ホーム ページの **[リンクされたサービス]** タイルをクリックします。 
4.	**[リンクされたサービス]** ブレードで、コマンド バーの **[新しいデータ ストア]** をクリックします。 
4.	**名前**として「**SqlServerLinkedService**」と入力します。 
2.	**[種類]** の横の矢印をクリックして、**[SQL Server]** を選択します。

	![新しいデータ ストアの作成](./media/data-factory-move-data-between-onprem-and-cloud/new-data-store.png)
3.	**[種類]** 設定の下でさらに設定する必要があります。
4.	**[データ ゲートウェイ]** 設定で、作成したゲートウェイを選択します。 

	![SQL Server の設定](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-settings.png)
4.	**[サーバー]** 設定にデータベース サーバーの名前を入力します。
5.	**[データベース]** 設定にデータベースの名前を入力します。
6.	**[資格情報]** の隣の矢印をクリックします。

	![[資格情報] ブレード](./media/data-factory-move-data-between-onprem-and-cloud/credentials-dialog.png)
7.	**[資格情報]** ブレードで、**[ここをクリックして資格情報を設定する]** をクリックします。
8.	**[資格情報の設定]** ダイアログ ボックスで、次の手順を実行します。

	![[資格情報の設定] ダイアログ](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png) 1.Data Factory サービスがデータベースへの接続に使用する**認証**を選択します。2.**[ユーザー名]** 設定に、データベースへのアクセス権を持つユーザーの名前を入力します。3.**[パスワード]** 設定に、ユーザーのパスワードを入力します。4.**[OK]** をクリックしてダイアログ ボックスを閉じます。 
4. **[OK]** をクリックして **[資格情報]** ブレードを閉じます。 
5. **[新しいデータ ストア]** ブレードで、**[OK]** をクリックします。 	
6. [リンクされたサービス] ブレードで **SqlServerLinkedService** のステータスが [オンライン] に設定されていることを確認します。![SQL Server のリンクされているサービスの状態](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

ゲートウェイ コンピューターとは異なるコンピューターからポータルにアクセスする場合は、資格情報マネージャー アプリケーションがゲートウェイ コンピューターに接続できることを確認する必要があります。アプリケーションがゲートウェイ コンピューターにアクセスできない場合、データ ソースの資格情報を設定したり、データ ソースへの接続をテストしたりすることはできません。

Microsoft Azure ポータルから起動した [資格情報の設定] アプリケーションを使用してオンプレミスのデータ ソースの資格情報を設定すると、ポータルは、ユーザーがゲートウェイ コンピューターの Data Management Gateway Configuration Manager の [証明書] タブで指定した証明書で資格情報を暗号化します。

API を使用して資格情報を暗号化した方がよい場合は、[New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/azure/dn834940.aspx) PowerShell コマンドレットを使用して資格情報を暗号化できます。コマンドレットはゲートウェイに構成されている証明書を使用して資格情報を暗号化します。このコマンドレットによって返される暗号化済みの資格情報を、[New-AzureDataFactoryLinkedService](https://msdn.microsoft.com/library/azure/dn820246.aspx) コマンドレットで使用する JSON ファイルの connectionString の EncryptedCredential 要素や、ポータルの Data Factory エディターで JSON スニペットに、追加できます。

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**注:** "資格情報の設定" アプリケーションを使用すると、上記のようにリンクされたサービスに暗号化された資格情報が自動的に設定されます。

Data Factory エディターを使用して資格情報を設定するもう 1 つの方法があります。エディターを使用して SQL Server のリンクされたサービスを作成し、プレーン テキストで資格情報を入力した場合、資格情報は、ゲートウェイが使用するように構成されている証明書ではなく、Data Factory が所有する証明書で暗号化されます。この方法は、場合によっては少し高速ですが、セキュリティは低下します。したがって、この方法は開発/テストの目的のみに使用することをお勧めします。


## Azure PowerShell を使用したゲートウェイの作成と登録 
このセクションでは、Azure PowerShell コマンドレットを使用して、ゲートウェイを作成し登録する方法について説明します。

1. 管理者モードで **Azure PowerShell** を起動します。 
2. Azure Data Factory コマンドレットは、**AzureResourceManager** モードで利用できます。次のコマンドを実行して、**AzureResourceManager** モードに切り替えます。     

        switch-azuremode AzureResourceManager


2. **New-AzureDataFactoryGateway** コマンドレットを使用して、次のように論理ゲートウェイを作成します。

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**コマンドと出力の例**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. **New-AzureDataFactoryGatewayKey** コマンドレットを使用して、新しく作成したゲートウェイの登録キーを生成し、このキーをローカル変数 **$Key** に格納します。

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**コマンドの出力例:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Azure PowerShell で **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** フォルダーに移動します。次のコマンドに示すように、ローカル変数 **$Key** に関連付けられた **RegisterGateway.ps1** スクリプトを実行して、コンピューターにインストールされているクライアント エージェントを、前に作成した論理ゲートウェイに登録します。

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. **Get-AzureDataFactoryGateway** コマンドレットを使用して、データ ファクトリ内のゲートウェイの一覧を取得できます。**[状態]** に **[オンライン]** と表示されている場合、ゲートウェイをいつでも使用できることを意味します。

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

**Remove-AzureDataFactoryGateway** コマンドレットを使用して、ゲートウェイを削除できます。また、**Set-AzureDataFactoryGateway** コマンドレットを使用して、ゲートウェイの記述を更新できます。これらのコマンドレットの文法やその他の詳細については「Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)」を参照してください。


## Data Management Gateway を使用したコピーのデータ フロー
データ パイプラインでコピー アクティビティを使用してオンプレミスのデータを処理のためにクラウドに取り込むとき、またはクラウドの結果データをオンプレミスのデータ ストアにエクスポートして戻すときは、コピー アクティビティは内部的にゲートウェイを使用してオンプレミスのデータ ソースからクラウドに、またはその逆に、データを転送します。

データ ゲートウェイでのコピーのデータ フローと手順の概要を次に示します。![ゲートウェイを使用したデータ フロー](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	データの開発者は、[Azure ポータル](http://portal.azure.com)または [PowerShell コマンドレット](https://msdn.microsoft.com/library/dn820234.aspx)を使用して Azure Data Factory 用の新しいゲートウェイを作成します。 
2.	データ開発者は、[リンクされたサービス] パネルを使用して、オンプレミスのデータ ストアとゲートウェイのための新しいリンクされたサービスを定義します。リンクされたサービスの設定の一部として、データ開発者は、手順で示したように資格情報の設定アプリケーションを使用して認証の種類と資格情報を指定します。資格情報の設定アプリケーションのダイアログは、データ ストアと通信して接続をテストし、ゲートウェイと通信して資格情報を保存します。
3.	ゲートウェイは、資格情報をクラウドに保存する前に、(開発者によって提供された) ゲートウェイと関連付けられた証明書で資格情報を暗号化します。
4.	Data Factory 移動サービスは、共有 Azure Service Bus キューを使用する制御チャネルを介して、ジョブのスケジューリングと管理のためにゲートウェイと通信します。コピー アクティビティ ジョブを開始する必要がある場合、Data Factory はリクエストと資格情報をキューに入れます。ゲートウェイは、キューをポーリングした後でジョブを開始します。
5.	ゲートウェイは、同じ証明書で資格情報を復号化し、適切な認証の種類を使用してオンプレミスのデータ ストアに接続します。
6.	ゲートウェイは、データ パイプラインでのコピー アクティビティの構成方法に応じて、オンプレミスのストアからクラウドのストレージに、またはクラウドのストレージからオンプレミスのデータ ストアに、データをコピーします。注: この手順では、ゲートウェイは、セキュリティで保護された (HTTPS) チャネルを使用して、クラウド ベースのストレージ サービス (例: Azure BLOB、Azure SQL など) と直接通信します。

### ポートとセキュリティに関する考慮事項

1. ステップ バイ ステップ チュートリアルで前述したように、Data Factory でオンプレミスのデータ ストアに資格情報を設定するには複数の方法があります。ポートの考慮事項は、これらのオプションによって異なります。	

	- **資格情報の設定**アプリの使用: Data Management Gateway インストール プログラムは既定で、ゲートウェイ コンピューターのローカル Windows ファイアウォールでポート **8050** と **8051** を開きます。資格情報の設定アプリケーションはこれらのポートを使用して、ゲートウェイに資格情報を中継します。これらのポートは、ローカル Windows ファイアウォール上のコンピューターに対してのみ開かれます。これらのポートにはインターネットからは到達できないので、企業全体のファイアウォールで開く必要はありません。
	2.	[New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) PowerShell コマンドレットの使用: a.PowerShell コマンドを使用して資格情報を暗号化しており、結果としてゲートウェイのインストールで Windows ファイアウォールのゲートウェイ コンピューターの受信ポートを開きたくない場合は、インストールの間に次のコマンドを使用して行うことができます。
	
			msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
3.	**資格情報の設定**アプリケーションを使用する場合は、データ ソースの資格情報を設定し、データ ソースへの接続をテストできるようにするために、Data Management Gateway に接続できるコンピューターで資格情報の設定アプリケーションを起動する必要があります。
4.	オンプレミスの SQL Server と Azure SQL Database の間でデータをコピーするときは、次のことを確認してください。	
	- 	ゲートウェイ コンピューターのファイアウォールが、**TCP** ポート **1433** での送信 TCP 通信を許可している
	- 	[Azure SQL ファイアウォール設定](https://msdn.microsoft.com/library/azure/jj553530.aspx)を構成して、**許可する IP アドレス**に**ゲートウェイ コンピューターの IP アドレス**を追加している
5.	オンプレミスの SQL Server から任意のコピー先にデータをコピーするときに、ゲートウェイ コンピューターと SQL Server コンピューターが異なる場合は、SQL Server インスタンスがリッスンしているポートを介してゲートウェイがデータベースにアクセスできるように、SQL Server コンピューターで [Windows ファイアウォールを構成](https://msdn.microsoft.com/library/ms175043.aspx)します。既定のインスタンスの場合、ポート 1433 です。

## フィードバックの送信
この記事に関するフィードバックをお待ちしています。少しのお時間をとって、[電子メール](mailto:adfdocfeedback@microsoft.com?subject=data-factory-move-data-between-onprem-and-cloud.md)でフィードバックをお寄せください。

<!---HONumber=Nov15_HO1-->