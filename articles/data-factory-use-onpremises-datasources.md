<properties 
	pageTitle="パイプラインが内部設置型のデータを扱えるようにする | Azure Data Factory" 
	description="内部設置型のデータ ソースを Azure データ ファクトリに登録し、データをデータ ソース間でコピーする方法について説明します。" 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# パイプラインが内部設置型のデータを扱えるようにする

Azure データ ファクトリ内のパイプラインが内部設置型のデータを扱えるようにするには、Azure 管理ポータルまたは Azure PowerShell を使用し、内部設置型のデータ ソースをリンクされたサービスとしてデータ ファクトリに追加する必要があります。
 
内部設置型のデータ ソースをリンクされたサービスとしてデータ ファクトリに追加できるようにするには、まず Microsoft Data Management Gateway をダウンロードして内部設置型のコンピューターにインストールし、内部設置型のデータ ソースがゲートウェイを使用できるようにリンクされたサービスを構成する必要があります。


## <a href="DMG"></a> Data Management Gateway

**Data Management Gateway** 安全かつ管理された方法でクラウド サービスへの内部設置型のデータ ソースに接続するソフトウェアです。Data Management Gateway を使用すると、以下のことを行えます。

- **ハイブリッドのデータ アクセスのための内部設置型データへの接続** – 内部設置型データを内部設置型のデータを実行してビジネスを管理するときにクラウド サービスを利用するには、クラウド サービスに接続することができます。
- **定義、セキュリティで保護されたデータ プロキシ** – そのゲートウェイがクラウド サービスからのデータ要求を認証し、内部設置型のデータ ソースを保護するため、内部設置型データ ソースを Data Management Gateway で公開されますを定義することができます。
- **完全な管理のために、ゲートウェイを管理** – 完全な監視と管理、および管理のための Data Management Gateway 内のすべてのアクティビティのログ記録が表示されます。
- **データを効率的に移動** – データが転送される断続的なに対する回復力のネットワークの問題を自動再試行ロジックを同時にします。


Data Management Gateway が持つ幅広い内部設置型のデータ接続能力には、以下が含まれます。

- **企業のファイアウォールを与えない** – Data Management Gateway のインストール後に、接続または侵入を必要とする会社のネットワーク インフラストラクチャの変更にファイアウォールを開く必要はありません。 
- **、証明書を資格情報の暗号化** – データ ソースへの接続に使用される資格情報が完全に、ユーザーによって所有の証明書で暗号化されています。証明書を持たなければ、資格情報をプレーン テキストへと復号することは Microsoft を含めだれにもできません。

### Data Management Gateway を使用する上で考慮すること
1.	Data Management Gateway の 1 つのインスタンスの複数の内部設置型のデータ ソースを使用できますが、注意してくださいを **ゲートウェイが、Azure のデータのファクトリに関連付けられている** 別のデータのファクトリでは共有できないとします。
2.	可能 **Data Management Gateway のインスタンスを 1 つだけ** 、コンピューターにインストールします。例えば、内部設置型のデータ ソースにアクセスする必要があるデータ ファクトリが 2 つあるとすると、内部設置型のコンピューター 2 台にゲートウェイをインストールし、各ゲートウェイを別々のデータ ファクトリに結び付ける必要があります。
3.	 **ゲートウェイは、データ ソースと同じコンピューター上に存在する必要はありません**, 、ゲートウェイがデータ ソースへの接続時間を短縮して、データ ソースに近い状態のままですが。ゲートウェイをデータ ソースとリソースの消費しないように、内部設置型のデータ ソースをホストする 1 つの異なるコンピューターで、ゲートウェイをインストールすることをお勧めします。
4.	 **同じ内部設置型のデータ ソースに接続する、異なるコンピューター上の複数のゲートウェイ**です。たとえば、2 つのゲートウェイがデータの 2 つのファクトリを提供している必要がありますが、同じ内部設置型のデータ ソースがデータ ファクトリに登録されています。 
5.	処理する、コンピューターにインストールされているゲートウェイが既にある場合、 **Power BI** シナリオでは、インストールしてください、 **別のゲートウェイを Azure のデータのファクトリの** 別のコンピューターでします。

### ポートとセキュリティの考慮事項 
- Data Management Gateway のインストール プログラムが開きます **8050** と **8051** ゲートウェイ コンピューター上のポートです。これらのポートで使用される、 **資格情報マネージャー** (ClickOnce アプリケーション) をオンプレミスでリンクされているサービスの資格情報を設定して、データ ソースへの接続をテストすることができます。これらのポートは、インターネットから到達できませんし、必要がある必要はありません、企業のファイアウォールで開くこれらします。
- Azure SQL データベースとの間には、内部設置型 SQL Server データベースをデータをコピーするときは、次のことを確認します。
 
	- ゲートウェイ コンピューター上のファイアウォールで TCP 通信の発信を許可する **TCP** ポート **1433年**
	- 構成 [ファイアウォールの設定を Azure の SQL](https://msdn.microsoft.com/library/azure/jj553530.aspx) を追加する、 **ゲートウェイ コンピューターの IP アドレス** を **使用できる IP アドレス**です。

- データのコピーを内部設置型 SQL Server、先にし、ゲートウェイとの SQL Server コンピューターが異なる、ときに、次の操作: [Windows ファイアウォールを構成する](https://msdn.microsoft.com/library/ms175043.aspx) SQL server コンピューターのゲートウェイは、SQL Server インスタンスがリッスンするポートを使用して、データベースにアクセスできるようにします。既定のインスタンスはポート 1433年です。

- 起動する必要があります、 **資格情報マネージャー** できるように、データ ソースの資格情報を設定して、データ ソースへの接続をテストするには、Data Management Gateway に接続できないコンピューターにアプリケーションです。

### ゲートウェイのインストールの前提条件 

1.	サポートされている **オペレーティング システム** バージョンは、Windows 7、Windows 8 または 8.1、Windows Server 2008 R2、Windows Server 2012 です。
2.	推奨される **構成** 、ゲートウェイのマシンが少なくとも 2 GHz、4 コア、8 GB の RAM、80 GB のディスクです。
3.	休止状態になると、ホスト コンピューターの場合、ゲートウェイは同じデータの要求に応答することができません。そのため、構成の適切な **電源プラン** 、ゲートウェイをインストールする前にコンピューターにします。ゲートウェイのインストールは、コンピューターが休止状態に構成されている場合、メッセージを求めます。  


 

## チュートリアル

このチュートリアルでは、内部設置型の SQL Server データベースから、Azure BLOB にデータを移動するパイプラインを備えたデータ ファクトリを作成します。

## 手順 1: Azure のデータのファクトリを作成します。
このステップでという名前の Azure データ ファクトリ インスタンスを作成するには、Azure 管理ポータルを使用する **ADFTutorialOnPremDF**です。Azure Data Factory のコマンドレットを使用してデータ ファクトリを作成することもできます。

1.	ログインした後、 [Azure プレビュー ポータル][azure-preview-portal], 、] をクリックして **新規** 、左下隅にあるから次のように選択します。 **データ分析** で、 **作成** ] をクリックしてブレードで **データ ファクトリ** 上、 **データ分析** ブレードします。

	![新しい DataFactory -> します。][image-data-factory-new-datafactory-menu]
  
6.  **データの新しいファクトリ** ブレード。
	1. 入力 **ADFTutorialOnPremDF** の **名前**です。
	2. をクリックして **リソース グループ名** を選択して **ADFTutorialResourceGroup** (」からチュートリアルを行ってきた場合 [Azure のデータのファクトリを使い始める][adfgetstarted]です。既存のリソース グループを選択するか、新しく作成することができます。新しいリソース グループを作成するには:
		1. クリックして **新しいリソース グループの作成**です。
		2.  **を作成するリソース グループのブレード**, を入力、 **名前** 用をクリックして、リソース グループ、 **[ok]**です。

7. 注意して **スタート ボードに追加** がオンになって、 **データの新しいファクトリ** ブレードします。

	![スタート画面への追加][image-data-factory-add-to-startboard]

8.  **データの新しいファクトリ** ブレードで] をクリックして **作成**です。

	> [AZURE.NOTE]**データの工場出荷時の名前"ADFTutorialOnPremDF"は使用できません。**  
9. 検索では、作成プロセスからの通知、 **通知** 、左側のハブ。クリックして **X** を閉じるには、 **通知** ブレードが開いている場合。

	![通知ハブ][image-data-factory-notifications-hub]

11. 作成が完了すると、後に表示されます、 **データ ファクトリ** ブレードを次に示すよう。

	![データの工場出荷時のホーム ページ][image-data-factory-datafactory-homepage]

## 手順 2: データの管理ゲートウェイを作成します。
5.	 **データ ファクトリ** のブレードに **ADFTutorialOnPremDF**, 、] をクリックして **関連付けられているサービス**です。 

	![データの工場出荷時のホーム ページ][image-data-factory-home-age]

2.	 **関連付けられているサービス** ブレードで] をクリックして **+ Data gateway**です。

	![関連付けられているサービス - ゲートウェイのボタンを追加します。][image-data-factory-linkedservices-add-gateway-button]

2.  **を作成する** ブレードで入力 **adftutorialgateway** の **名前**, 、] をクリック **[ok]**です。

	![ゲートウェイのブレードを作成します。][image-data-factory-create-gateway-blade]

3. **[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。これにより、ゲートウェイのインストール パッケージのダウンロードと、コンピューターへのインストール、構成、および登録が行われます。

	> [AzURE.NOTE]Internet Explorer や Microsoft ClickOnce 互換性のある web ブラウザーを使用してください。

	![ゲートウェイのブレードを構成します。][image-data-factory-gateway-configure-blade]

	これは、たった 1 つの手順 (クリック 1 回) でゲートウェイのダウンロード、インストール、構成、および登録を行う、最も簡単な方法です。ご覧、 **Microsoft Data Management Gateway 構成マネージャー** 、コンピューターにアプリケーションをインストールします。実行可能ファイルを検索することもできます **ConfigManager.exe** フォルダー内: **C:\Program files \microsoft データ管理 Gateway\1.0\Shared**です。

	ダウンロードおよびこのブレードで、リンクを使用してゲートウェイを手動でインストールおよび登録できるに示すキーを使用して、 **キーで登録** テキスト ボックスです。
	
	参照してください [Data Management Gateway](#DMG) 詳細については、ゲートウェイのベスト プラクティスと重要な考慮事項などに関するセクション。

	>[AZURE.NOTE]コンピューターの管理者で、ローカル インストールして、Data Management Gateway を正常に構成する必要があります。その他のユーザーは、Data Management Gateway Users ローカル Windows グループに追加できます。このグループのメンバーは、Data Management Gateway の構成マネージャー ツールを使用して、ゲートウェイを構成することになります。

4. クリックして、 **通知** 、左側のハブ。表示されるまで待って **Express のセットアップが成功した 'adftutorialgateway' の** でメッセージ、 **通知** ブレードします。

	![Express のセットアップに成功しました][express-setup-succeeded]
5. をクリックして **[ok]** 上、 **作成** ブレードし、 **データの新しいゲートウェイ** ブレードします。
6. 閉じる、 **関連付けられているサービス** ブレード (キーを押して **X** 右上隅にあるボタン) を閉じて再度開きます、 **関連付けられているサービス** ブレード、ゲートウェイの最新の状態を表示します。 
7. 確認して、 **状態** はゲートウェイの **オンライン**です。 

	![ゲートウェイの状態][gateway-status]
5. 起動 **Microsoft Data Management Gateway 構成マネージャー** 、コンピューター上のアプリケーションです。

	![Gateway 構成マネージャー][image-data-factory-gateway-configuration-manager]

6. 値が以下の状態になるまで待ちます。
	1. 場合、サービス **状態** に設定されていない **開始**, 、] をクリックして **サービスの開始** 、サービスを開始し、他のフィールドを更新するは、しばらく待機します。
	2. **ゲートウェイ名** に設定されている **adftutorialgateway**です。
	3. **インスタンス名** に設定されている **adftutorialgateway**です。
	4. **ゲートウェイのキーの状態** に設定されている **登録されている**です。
	5. ステータス バーの下部にあるディスプレイ **Data Management Gateway のクラウド サービスに接続されている** と共に、 **緑のチェック マーク**です。
	
7.  **関連付けられているサービス** ブレードでいることを確認、 **状態** はゲートウェイの **良い**です。
8. 表示されるまで、すべてのブレードを閉じる、 **データ ファクトリ** ホーム ページです。 

## 手順 2: 関連付けられているサービスを作成します。 
このステップでは 2 つの関連付けられているサービスを作成します。 **StorageLinkedService** と **SqlServerLinkedService**です。 **SqlServerLinkedService** 、内部設置型 SQL Server データベースをリンクして、 **StorageLinkedService** リンク サービスするには、Azure の blob ストアのリンク、 **ADFTutorialDataFactory**です。このチュートリアルの後半で、内部設置型の SQL Server データベースから Azure BLOB ストアにデータをコピーするパイプラインを作成します。

### 内部設置型の SQL Server データベースにリンクされたサービスを追加する
1.	 **データ ファクトリ** ブレード、ポイント **作成者と展開** タイルを起動する、 **エディター** データ ファクトリのです。

	![タイルの作成とデプロイ][image-author-deploy-tile]

	> [AZURE.NOTE][データの工場出荷時のエディター][data-factory-editor]
2.	 **エディター**, 、] をクリックして **データ ストアの新しい** ] ボタンをクリックし、ツールバー **内部設置型 SQL server データベース** 、ドロップ ダウン メニューからです。 

	![新しいデータ ストアのボタンのエディター][image-editor-newdatastore-onpremsql-button]
    
3.	右側のウィンドウで、内部設置型のリンクされている SQL Server サービスを作成するためには、JSON テンプレートが表示されます。 ![オンプレミスの SQL リンクのサービスの設定][image-editor-newdatastore-onpremsql-settings]

4.	JSON のウィンドウで次を操作します。
	1.	 **GatewayName** プロパティ、入力 **adftutorialgateway** 、二重引用符の内側にあるすべてのテキストを置換します。  
	2.	使用している場合は、 **SQL 認証**: 
		1.	 **ConnectionString** プロパティ、置き換える **< servername >**, 、**< databasename >**, 、**< ユーザー名 >**, 、および **< パスワード >** 、内部設置型 SQL server、データベース、ユーザー アカウントおよびパスワードの名前を持つ。	
		2.	最後の 2 つのプロパティを削除する (* * * * ユーザー名と **パスワード**)、JSON からファイルし、削除、 **コンマ (,)** 、残りの JSON スクリプトからの最後の行の最後に文字です。
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	使用している場合は、 **Windows 認証**:
		1.  **ConnectionString** プロパティ、置き換える **< servername >** と **< databasename >** 、内部設置型 SQL server とデータベースの名前を持つ。設定 **統合セキュリティ** に **True**です。削除 **ID** と **パスワード** 接続文字列から。
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. クリックして **展開** 、SqlServerLinkedService を展開するには、ツールバーのです。メッセージが表示されることを確認 **リンク サービス作成が正常に** 、タイトル バーにします。表示することも必要があります、 **SqlServerLinkedService** 、左側のツリー ビューでします。
		   
	![SqlServerLinkedService が正常に展開][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]クリックして、リンクされている SQL Server サービスを作成することもできます **データ ストアの新しい** のツール バー ボタン、 **関連付けられているサービス** ブレードします。この方法を選択する場合、ポータルにアクセスするコンピューターで実行されている資格情報マネージャーの ClickOnce アプリケーションを使用して、データ ソースの資格情報を設定します。ゲートウェイ コンピューターとは異なるコンピューターからポータルにアクセスする場合は、資格情報マネージャー アプリケーションがゲートウェイ コンピューターに接続できることを確認する必要があります。アプリケーションは、ゲートウェイ コンピューターにアクセスできない場合、これは許可されません、データ ソースの資格情報を設定して、データ ソースへの接続をテストします。

#### Azure ストレージ アカウント用のリンクされたサービスを追加する
 
1.  **エディター**, 、] をクリックして **データ ストアの新しい** ] ボタンをクリックし、ツールバー **Azure ストレージ** 、ドロップ ダウン メニューからです。右側のウィンドウで、リンクされている Azure のストレージ サービスを作成するためには、JSON テンプレートが表示されます。 

	![新しいデータ ストアのボタンのエディター][image-editor-newdatastore-button]
    
6. 置き換える **< accountname >** と **< accountkey >** アカウント名とアカウント キーの値を Azure ストレージ アカウントをします。

	![Blob ストレージのエディター JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]参照してください [JSON スクリプト参照](http://go.microsoft.com/fwlink/?LinkId=516971) JSON のプロパティに関する詳細です。

6. クリックして **展開** 、StorageLinkedService を展開するには、ツールバーのです。メッセージが表示されることを確認 **リンク サービス作成が正常に** 、タイトル バーにします。

	![エディターの Blob ストレージを展開します。][image-editor-blob-storage-deploy]

 
## 手順 3: 入力を作成し、出力データセット
このステップでは、コピー操作 (内部設置型の SQL Server データベース => Azure BLOB ストレージ) の入力および出力データを表す入出力データ セットを作成します。データセットまたはテーブル (四角形のデータセット) を作成する前に、以下を実施する必要があります (一覧の後に詳細な手順があります)。

- という名前のテーブルを作成する **emp** 、SQL Server データベースに追加したリンクをサービスとしてデータをテーブルにエントリの例のファクトリおよび insert のいくつか。
- - チュートリアルを終了していない場合は、 [Azure のデータのファクトリを使い始める][adfgetstarted] 記事という名前の blob コンテナーを作成、 **adftutorial** データ ファクトリにリンクされているサービスとして追加したストレージ アカウントの blob、Azure でします。

### チュートリアル用に内部設置型の SQL Server を用意します。

1. SQL Server のサービスのリンク、内部設置型の指定したデータベースで (* * * * SqlServerLinkedService) を作成するには、次の SQL スクリプトを使用して、 **emp** 、データベース内のテーブルです。


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

1.	 **データ工場出荷時のエディター**, 、クリックして **新しいデータセット** コマンド バーをクリック **内部設置型 SQL**です。 
2.	右側のウィンドウで、JSON を次のテキストに置き換えます。    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
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
	
	- 場所 **型** に設定されている **OnPremisesSqlServerTableLocation**です。
	- **tableName** に設定されている **emp**です。
	- **linkedServiceName** に設定されている **SqlServerLinkedService** (このリンク サービス手順 2. で作成した)。
	- 指定する必要がありますが Azure のデータのファクトリで別のパイプラインによって生成されない、入力テーブルで **waitOnExternal** 、JSON でセクションです。これは、入力データが Azure Data Factory サービスの外部で生成されることを意味します。   

	参照してください [JSON スクリプト参照][json-script-reference] JSON のプロパティに関する詳細です。

2. クリックして **展開** 、データセットを展開するには、コマンド バー (テーブルでは、四角形のデータセット)。というタイトル バーにメッセージを表示することを確認 **テーブルが正常に展開されている**です。


### 出力テーブルの作成

1.	 **データ工場出荷時のエディター**, 、クリックして **新しいデータセット** コマンド バーをクリック **Azure Blob ストレージ**です。
2.	右側のウィンドウで、JSON を次のテキストに置き換えます。 

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
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	以下の点に注意してください。
	
	- 場所 **型** に設定されている **AzureBlobLocation**です。
	- **linkedServiceName** に設定されている **StorageLinkedService** (このリンク サービス手順 2. で作成した)。
	- **folderPath** に設定されている **adftutorial/outfromonpremdf** outfromonpremdf は adftutorial コンテナー内のフォルダーの場所です。作成する必要が、 **adftutorial** コンテナーです。
	-  **可用性** に設定されている **1 時間ごと** (* * * * 頻度に設定 **時間** と **間隔** に設定 **1**)。データのファクトリのサービスがで 1 時間ごとに、出力データのスライスの生成は、 **emp** Azure SQL データベースのテーブルです。 

	指定しない場合、 **ファイル名** の **入力テーブル**, 、入力フォルダーからすべてのファイルと blob (* * * * folderPath) の入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。サンプル ファイルを参照してください、 [チュートリアル][adf-tutorial] 例についてはします。
 
	**出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されるファイルには Data.<Guid>.txt という形式で名前が付けられます (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

	設定する **folderPath** と **ファイル名** に基づいて動的に、 **SliceStart** 時間に partitionedBy プロパティを使用します。次の例では、folderPath に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。たとえば、スライスが 2014-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2014/10/20 に設定され、fileName は 08.csv に設定されます。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	参照してください [JSON スクリプト参照][json-script-reference] JSON のプロパティに関する詳細です。

2.	クリックして **展開** 、データセットを展開するには、コマンド バー (テーブルでは、四角形のデータセット)。というタイトル バーにメッセージを表示することを確認 **テーブルが正常に展開されている**です。
  

## 手順 4: を作成して、パイプラインの実行
このステップで作成する、 **パイプライン** いずれかで **コピー アクティビティ** を使用して **EmpOnPremSQLTable** の入力としてと **OutputBlobTable** 出力として。

1.	クリックして **新しいパイプライン** コマンド バーでします。ボタンが表示されない場合は、クリックして **しています.(省略記号)** コマンド バーを展開します。
2.	右側のウィンドウで、JSON を次のテキストに置き換えます。   


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
	        	],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	以下の点に注意してください。
 
	- セクションでは、アクティビティ、アクティビティのみが持つ **型** に設定されている **CopyActivity**です。
	- **入力** に設定されているアクティビティの **EmpOnPremSQLTable** と **出力** に設定されているアクティビティの **OutputBlobTable**です。
	-  **変換** セクション、 **[SqlSource** として指定されて、 **ソースの種類** と **BlobSink **として指定されて、 **タイプのシンク**. - SQL クエリ **選択 * emp から** が指定されている、 **sqlReaderQuery** プロパティの **[SqlSource**です。

	> [AZURE.NOTE]値を置き換える、 **開始** 、現在の日付を持つプロパティと **終了** で、次の日の値です。これら両方の開始し、終了の datetimes である必要があります [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)です。例: 2014年-10-14T16:32:41Z です。 **終了** 時刻は省略可能では、このチュートリアルで使用します。値を指定しない場合、 **終了** として計算されます。 プロパティを"* * スタート + 48 時間 * *"です。パイプラインを無期限に実行する次のように指定します。 **9/9/9999** の値として、 **終了** プロパティです。に基づいて、データ スライスは処理時間を定義する、 **可用性** 各 Azure データ工場出荷時のテーブルに対して定義されたプロパティ。上記の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。
	
2. クリックして **展開** 、データセットを展開するには、コマンド バー (テーブルでは、四角形のデータセット)。というタイトル バーにメッセージを表示することを確認 **パイプラインが正常に展開されている**です。
5. これで、閉じる、 **エディター** ] をクリックしてブレード **X**です。クリックして **X** 、ツールバーとツリー ビューでは、ADFTutorialDataFactory ブレードを閉じるには、もう一度です。表示する場合は **、保存されていない編集が破棄されます** メッセージで、クリックして **[ok]**です。
6. する必要があります、 **データ ファクトリ** ブレードで、 **ADFTutorialOnPremDF**です。

**ご利用ありがとうございます。** これで、Azure Data Factory、リンクされたサービス、テーブル、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。

### ダイアグラム ビューで、データのファクトリを表示します。 
1.  **Azure プレビュー ポータル**, 、] をクリックして **ダイアグラム** タイルのホーム ページで、 **ADFTutorialOnPremDF** データ ファクトリ。。

	![ダイアグラムのリンク][image-data-factory-diagram-link]

2. 以下のような図が表示されるはずです。

	![ダイアグラム ビュー][image-data-factory-diagram-view]

	ズーム イン、ズーム アウト、ズームを 100% ズームに合わせて自動的に、パイプラインと、テーブルに配置および系列の情報を表示する (上位および下位の項目を選択したアイテムの強調表示) することができます。(入力/出力テーブルまたはパイプライン) のプロパティを表示するオブジェクト上の二重 blick ことができます。

## 手順 5: データセットとパイプラインを監視します。
このステップでは、Azure ポータルを使用して、Azure Data Factory の状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。詳細監視のコマンドレットの使用については、次を参照してください。 [モニターと管理 Azure のデータを使用してファクトリ PowerShell][monitor-manage-powershell]です。

1. 移動 **Azure プレビュー ポータル** (閉じていない) 場合
2. 場合、ブレードの **ADFTutorialOnPremDF** が開いていないをクリックして開きます **ADFTutorialOnPremDF** 上、 **スタート ボード**です。
3. 表示されます、 **カウント** と **名前** テーブルとパイプラインのこのブレード上に作成したのです。

	![データの工場出荷時のホーム ページ][image-data-factory-homepage-2]
4. ここでは、クリックして **データセット** 並べて表示します。
5.  **データセット** ブレードをクリックして、 **EmpOnPremSQLTable**です。

	![EmpOnPremSQLTable スライス][image-data-factory-onprem-sqltable-slices]

6. 現在の時刻までのデータ スライスを既に生成されているし、していることを確認 **準備ができて**です。これは、SQL Server データベースに挿入したデータが、現在まで残っているためです。あるスライスなしに表示することを確認、 **問題スライス** 下部のセクションです。


	どちらも **スライスを最近更新された** と **最近スライスが失敗しました** リストはによって並べ替えられます、 **最終更新時刻**です。次の状況では、スライスの更新時間が変更されます。
    

	-  スライスのステータスは、手動で更新するなどを使用して、 **セット AzureDataFactorySliceStatus** (または) をクリックして **実行** 上、 **スライス** スライスのブレードします。
	-  スライスは、実行のための状態を変更 (など、実行の開始、実行を終了し、失敗した、実行が成功したが終了したなど)。
 
	リストまたはのタイトルをクリックして **しています.(省略記号)** より大規模な一覧のスライスを参照してください。クリックして **フィルター** をスライス、フィルター処理するには、ツールバーのです。
	
	代わりに、スライスの開始時刻から終了時刻によって並べ替えられたデータのスライスを表示する] をクリックして **(スライスの時刻) でのデータ スライス** 並べて表示します。

7. ここで、 **データセット** ブレードをクリックして **OutputBlobTable**です。

	![OputputBlobTable スライス][image-data-factory-output-blobtable-slices]
8. 現在の時刻までのスライスが生成されることを確認および **準備ができて**です。設定されている現在の時刻までのスライスの状態になるまでの待機 **準備ができて**です。
9. 一覧からいずれかのデータ スライスをクリックして表示、 **データ スライス** ブレードします。

	![データ スライス ブレード][image-data-factory-dataslice-blade]

	スライスではない場合、 **準備ができて** 状態、準備ができていないし、現在のスライスでの実行をブロックしているアップ ストリームのスライスにわかり、 **準備ができていない上流のスライス** ] ボックスの一覧です。

10. クリックして、 **アクティビティ実行** 、ウィンドウを下にある一覧から **アクティビティ実行の詳細**です。

	![アクティビティの実行の詳細] ブレード][image-data-factory-activity-run-details]

11. クリックして **X** のホームのブレードに戻るまで、すべてのブレードを閉じる、 **ADFTutorialOnPremDF**です。
14. (省略可能)をクリックして **パイプライン**, 、] をクリックして **ADFTutorialOnPremDF**, 、入力テーブルをドリルスルーして (* * * * 消費済み) または出力テーブル (* * Produced * *)。
15. などのツールを使用して **Azure ストレージ エクスプ ローラー** 出力を確認します。

	![Azure ストレージ エクスプローラー][image-data-factory-stroage-explorer]


## 作成して Azure PowerShell を使用してゲートウェイを登録します。 
このセクションでは、Azure PowerShell コマンドレットを使用して、ゲートウェイを作成し登録する方法について説明します。

1. 起動 **Azure PowerShell** 管理者モードでします。 
2. Azure のデータのファクトリのコマンドレットは、 **AzureResourceManager** モード。切り替えるには、次のコマンドを実行する、 **AzureResourceManager** モード。     

        switch-azuremode AzureResourceManager


2. 使用して、 **新規 AzureDataFactoryGateway** コマンドレットを次のように、論理のゲートウェイを作成します。

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**コマンドの例と出力**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. 使用して、 **新規 AzureDataFactoryGatewayKey** コマンドレットでは、新しく作成されたゲートウェイの登録キーを生成し、キーをローカル変数に格納を **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**コマンドの出力の例:**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. フォルダーに切り替え、Azure PowerShell で: **C:\Program files \microsoft データ管理 Gateway\1.0\PowerShellScript** と実行 **RegisterGateway.ps1** スクリプトは、ローカル変数に関連付けられている **$Key** 前に作成する論理のゲートウェイを使用してコンピューター上にインストールされている、クライアント エージェントを登録する次のコマンドに示すようにします。

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. 使用することができます、 **Get AzureDataFactoryGateway** をコマンドレットで、データのファクトリでゲートウェイの一覧を取得します。ときに、 **状態** を示しています **オンライン**, 、ゲートウェイがすぐに使用できることを意味します。

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

使用して、ゲートウェイを削除する、 **削除 AzureDataFactoryGateway** を使用して、ゲートウェイのコマンドレットと更新プログラムの説明、 **セット AzureDataFactoryGateway** コマンドレットです。これらのコマンドレットの文法やその他の詳細については「Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)」を参照してください。



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
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

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=GIT-SubDir-->