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
	ms.date="07/10/2015" 
	ms.author="spelluru"/>

# パイプラインが内部設置型のデータを扱えるようにする

Azure データ ファクトリ内のパイプラインが内部設置型のデータを扱えるようにするには、Azure 管理ポータルまたは Azure PowerShell を使用し、内部設置型のデータ ソースをリンクされたサービスとしてデータ ファクトリに追加する必要があります。
 
内部設置型のデータ ソースをリンクされたサービスとしてデータ ファクトリに追加できるようにするには、まず Microsoft Data Management Gateway をダウンロードして内部設置型のコンピューターにインストールし、内部設置型のデータ ソースがゲートウェイを使用できるようにリンクされたサービスを構成する必要があります。


## <a href="DMG"></a>Data Management Gateway

**Data Management Gateway** は、管理された安全な方法でオンプレミス データ ソースをクラウド サービスに接続するソフトウェアです。Data Management Gateway を使用すると、以下のことを行えます。

- **ハイブリッド データ アクセスのためにオンプレミス データに接続する** - オンプレミス データをクラウド サービスに接続することで、オンプレミス データを使用して業務を継続しながら、クラウド サービスのメリットが得られます。
- **安全なデータ プロキシを定義する** - ゲートウェイがクラウド サービスからのデータ要求を認証し、オンプレミス データ ソースを保護できるように、Data Management Gateway を使用して公開するオンプレミス データ ソースを定義できます。
- **徹底したガバナンスを実現するためにゲートウェイを管理する** - 管理とガバナンスを実現するために、Data Management Gateway 内であらゆるアクティビティを完全に監視し、ログに記録できます。
- **データを効率的に移動する** - データは並列に転送されます。自動再試行ロジックにより、断続的なネットワークの問題にも対応できます。


Data Management Gateway が持つ幅広い内部設置型のデータ接続能力には、以下が含まれます。

- **企業のファイアウォールに影響を及ぼさない** - ファイアウォール接続を開いたり、企業のネットワーク インフラストラクチャに影響する変更を行ったりする必要なく、Data Management Gateway はインストール後すぐに機能します。 
- **証明書を使用して資格情報を暗号化する** - データ ソースへの接続に使用される資格情報は、ユーザーが完全に所有する証明書を使用して暗号化されます。証明書を持たなければ、資格情報をプレーン テキストへと復号することは Microsoft を含めだれにもできません。

### Data Management Gateway を使用する上で考慮すること
1.	Data Management Gateway の 1 つのインスタンスを複数のオンプレミス データ ソースに使用できますが、**ゲートウェイは Azure Data Factory に関連付けられており**、別のデータ ファクトリと共有することはできないことに注意してください。
2.	コンピューターには、**Data Management Gateway のインスタンスを 1 つだけ**インストールできます。例えば、内部設置型のデータ ソースにアクセスする必要があるデータ ファクトリが 2 つあるとすると、内部設置型のコンピューター 2 台にゲートウェイをインストールし、各ゲートウェイを別々のデータ ファクトリに結び付ける必要があります。
3.	**ゲートウェイはデータ ソースと同じコンピューター上に存在する必要はありませんが**、データ ソースの近くにあると、ゲートウェイがデータ ソースに接続する際の時間が短縮されます。ゲートウェイとデータ ソースの間でリソースの競合が発生しないように、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターにゲートウェイをインストールすることをお勧めします。
4.	**同じオンプレミス データ ソースに接続する異なるコンピューターで複数のゲートウェイを使用**できます。たとえば、2 つのデータ ファクトリを提供する 2 つのゲートウェイがあり、どちらのデータ ファクトリにも同じオンプレミス データ ソースが登録されている場合があります。 
5.	**Power BI** のシナリオを提供するゲートウェイがコンピューターに既にインストールされている場合は、**Azure Data Factory 用のゲートウェイ**を別のコンピューターにインストールしてください。

### ポートとセキュリティに関する考慮事項 
- Data Management Gateway インストール プログラムは、ゲートウェイ コンピューターのポート **8050** と **8051** を開きます。これらのポートは、**資格情報マネージャー** (ClickOnce アプリケーション) が使用します。資格情報マネージャーにより、オンプレミスのリンク サービスの資格情報を設定し、データ ソースへの接続をテストできます。これらのポートにはインターネットからは到達できないので、企業のファイアウォールで開く必要はありません。
- オンプレミスの SQL Server データベースと Azure SQL Database の間でデータをコピーするときは、次のことを確認してください。
 
	- ゲートウェイ コンピューターのファイアウォールが、**TCP** ポート **1433** での送信 TCP 通信を許可している
	- [Azure SQL ファイアウォール設定](https://msdn.microsoft.com/library/azure/jj553530.aspx)を構成して、**許可する IP アドレス**に**ゲートウェイ コンピューターの IP アドレス**を追加している

- オンプレミスの SQL Server から任意のコピー先にデータをコピーするときに、ゲートウェイ コンピューターと SQL Server コンピューターが異なる場合は、SQL Server インスタンスがリッスンしているポートを介してゲートウェイがデータベースにアクセスできるように、SQL Server コンピューターで [Windows ファイアウォールを構成](https://msdn.microsoft.com/library/ms175043.aspx)します。既定のインスタンスの場合、ポート 1433 です。

- データ ソースの資格情報を設定し、データ ソースへの接続をテストできるようにするために、Data Management Gateway に接続できるコンピューターで **資格情報マネージャー** アプリケーションを起動する必要があります。

### ゲートウェイのインストール - 前提条件 

1.	サポートされている**オペレーティング システム**のバージョンは、Windows 7、Windows 8/8.1、Windows Server 2008 R2、Windows Server 2012 です。
2.	ゲートウェイ コンピューターの推奨される最小限の**構成**は、2 GHz、4 コア、8 GB の RAM、80 GB のディスクです。
3.	ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答できません。そのため、ゲートウェイをインストールする前に、コンピューターの適切な**電源プラン**を構成します。コンピューターが休止状態に構成されている場合、ゲートウェイのインストール時にメッセージが表示されます。  

Data Management Gateway では、データのシリアル化と逆シリアル化、データがホストされているコンピューター上のデータの同期が行われます。また、データのコピー時にデータの型変換も実行されます。コピー操作中に、ゲートウェイでソースのデータがメモリ バッファーに読み取られ、それと同時に、別のライター スレッドでバッファーがシンクに書き込まれます。ピーク時には、ゲートウェイ ホスト コンピューターで同時に複数のコピー操作ジョブが実行される可能性があるので、メモリと CPU リソースの使用量がアイドル時よりもはるかに多くなります。そのため、Data Management Gateway を実行するホスト コンピューターでは、上記で推奨されている最小限のコンピューター構成よりも多くのリソースが必要になる場合や、アイドル時に必要なリソースが少なくなる場合があります。


## チュートリアル

このチュートリアルでは、内部設置型の SQL Server データベースから、Azure BLOB にデータを移動するパイプラインを備えたデータ ファクトリを作成します。

## 手順 1. Azure Data Factory を作成する
この手順では、Azure 管理ポータルを使用して **ADFTutorialOnPremDF** という名前の Azure Data Factory インスタンスを作成します。Azure Data Factory のコマンドレットを使用してデータ ファクトリを作成することもできます。

1.	[Azure プレビュー ポータル][azure-preview-portal]にログインしたら、左下隅にある **[新規]** をクリックします。**[作成]** ブレードで **[データ分析]** を選択し、**[データ分析]** ブレードで **[Data Factory]** をクリックします。

	![New->DataFactory][image-data-factory-new-datafactory-menu]
  
6. **[新しいデータ ファクトリ]** ブレードで以下の手順を実行します。
	1. **[名前]** に「**ADFTutorialOnPremDF**」と入力します。
	2. **[リソース グループ名]** をクリックし、**[ADFTutorialResourceGroup]** を選択します (「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルを終えている場合)。既存のリソース グループを選択するか、新しく作成することができます。新しいリソース グループを作成するには:
		1. **[新しいリソース グループを作成]** をクリックします。
		2. **[リソース グループを作成] ブレード**で、リソース グループの**名前**を入力し、**[OK]** をクリックします。

7. **[新しいデータ ファクトリ]** ブレードの **[スタート画面に追加]** がオンになっていることに注意してください。

	![スタート画面への追加][image-data-factory-add-to-startboard]

8. **[新しいデータ ファクトリ]** ブレードで、**[作成]** をクリックします。

	Azure Data Factor の名前はグローバルに一意にする必要があります。"**データ ファクトリ名 "ADFTutorialOnPremDF" は使用できません**" というエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialOnPremDF など) 作成し直してください。このチュートリアルでは以降の手順の実行中に、この名前を ADFTutorialOnPremDF の代わりに使用します。

9. 左側の **[通知]** ハブで作成プロセスの通知を探します。**[通知]** ブレードが開いている場合は、**[X]** をクリックして閉じます。

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. 作成が完了すると、次に示すような **[Data Factory]** ブレードが表示されます。

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

## 手順 2. Data Management Gateway を作成する
5.	**ADFTutorialOnPremDF** の **[DATA FACTORY]** ブレードで、**[リンクされたサービス]** をクリックします。 

	![Data Factory Home Page][image-data-factory-home-age]

2.	**[リンクされたサービス]** ブレードで、**[データ ゲートウェイを追加]** をクリックします。

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. **[作成]** ブレードで、**名前**に「**adftutorialgateway**」と入力し、**[OK]** をクリックします。

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. **[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。これにより、ゲートウェイのインストール パッケージのダウンロードと、コンピューターへのインストール、構成、および登録が行われます。

	> [AZURE.NOTE]Internet Explorer または Microsoft の ClickOnce と互換性のある Web ブラウザーを使用してください。

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	これは、たった 1 つの手順 (クリック 1 回) でゲートウェイのダウンロード、インストール、構成、および登録を行う、最も簡単な方法です。**Microsoft Data Management Gateway 構成マネージャー** アプリケーションがコンピューターにインストールされていることがわかります。実行可能ファイル **ConfigManager.exe** は **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared** フォルダーにあります。

	このブレード内のリンクを使用してゲートウェイのダウンロードとインストールを手動で行い、**[キーで登録]** ボックスに表示されるキーを使用して登録することもできます。
	
	ベスト プラクティスや重要な考慮事項など、ゲートウェイの詳細については、「[Data Management Gateway](#DMG)」をご覧ください。

	>[AZURE.NOTE]Data Management Gateway を正常にインストールして構成するには、ローカル コンピューターの管理者である必要があります。他のユーザーをローカル Windows グループの Data Management Gateway Users に追加できます。このグループのメンバーは、Data Management Gateway 構成マネージャー ツールを使用して、ゲートウェイを構成できます。

4. 左側にある **[通知]** ハブをクリックします。**[通知]** ブレードに "**'adftutorialgateway' の簡単セットアップが成功しました**" というメッセージが表示されるまで待ちます。

	![Express setup succeeded][express-setup-succeeded]
5. **[作成]** ブレードで **[OK]** をクリックし、**[新しいデータ ゲートウェイ]** ブレードで [OK] をクリックします。
6. (右上隅にある **[X]** をクリックして) **[リンクされたサービス]** ブレードを閉じ、**[リンクされたサービス]** ブレードを再度開いてゲートウェイの最新の状態を確認します。 
7. ゲートウェイの**状態**が **[オンライン]** であることを確認します。 

	![Gateway status][gateway-status]
5. コンピューターで **Microsoft Data Management Gateway 構成マネージャー** アプリケーションを起動します。

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. 値が以下の状態になるまで待ちます。
	1. **[サービスの状態]** が **[開始済み]** になっていない場合は、**[サービスの開始]** をクリックしてサービスを開始し、他のフィールドが更新されるまでしばらく待ちます。
	2. **[ゲートウェイ名]** が **[adftutorialgateway]** になります。
	3. **[インスタンス名]** が **[adftutorialgateway]** になります。
	4. **[ゲートウェイ キーの状態]** が **[登録済み]** になります。
	5. 下部のステータス バーには、**緑色のチェック マーク**と共に "**Data Management Gateway クラウド サービスに接続済み**" と表示されます。
	
7. **[リンクされたサービス]** ブレードで、ゲートウェイの**状態**が **[良好]** であることを確認します。
8. すべてのブレードを閉じて、**Data Factory** のホーム ページに戻ります。 

## 手順 2. リンク サービスを作成する 
この手順では、**StorageLinkedService** と **SqlServerLinkedService** の 2 つのリンク サービスを作成します。**SqlServerLinkedService** はオンプレミスの SQL Server データベースをリンクし、**StorageLinkedService** は Azure BLOB ストアを **ADFTutorialDataFactory** にリンクします。このチュートリアルの後半で、内部設置型の SQL Server データベースから Azure BLOB ストアにデータをコピーするパイプラインを作成します。

### 内部設置型の SQL Server データベースにリンクされたサービスを追加する
1.	**[DATA FACTORY]** ブレードで **[作成とデプロイ]** タイルをクリックして、Data Factory **エディター**を起動します。

	![タイルの作成とデプロイ][image-author-deploy-tile]

	Data Factory エディターの詳細については、トピック「[Data Factory エディター][data-factory-editor]」を参照してください。

2.	**エディター**のツール バーで **[新しいデータ ストア]** をクリックし、ドロップダウン メニューから **[オンプレミスの SQL Server データベース]** を選択します。

	![Editor New data store button][image-editor-newdatastore-onpremsql-button]
    
3.	右側のウィンドウに、オンプレミスの SQL Server リンク サービスを作成するための JSON テンプレートが表示されます。![On-prem SQL Linked Service - settings][image-editor-newdatastore-onpremsql-settings]

4.	JSON ウィンドウで次の手順を実行します。
	1.	**gatewayName** プロパティで、「**adftutorialgateway**」と入力して、二重引用符で囲まれたすべてのテキストを置き換えます。  
	2.	**SQL 認証**を使用している場合は、次の手順を実行します。 
		1.	**connectionString** プロパティで、**<servername>**、**<databasename>**、**<username>**、**<password>** をオンプレミスの SQL Server 名、データベース名、ユーザー アカウント名、パスワードにそれぞれ置き換えます。インスタンス名を指定するには、エスケープ文字 \ を使用します。たとえば、**server\instancename** と指定します。 	
		2.	JSON ファイルから最後の 2 つのプロパティ (**username** と **password**) を削除し、残りの JSON スクリプトの最後の行の末尾にある**コンマ (,)** を削除します。
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	**Windows 認証**を使用している場合は、次の手順を実行します。
		1. **connectionString** プロパティで、**<servername>** と **<databasename>** をオンプレミスの SQL Server 名とデータベース名に置き換えます。**Integrated Security** を **True** に設定します。接続文字列から **ID** と **Password** を削除します。
			
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
		
6. ツール バーの **[デプロイ]** をクリックして、SqlServerLinkedService をデプロイします。タイトル バーに **"リンクされたサービスが正常に作成されました"** というメッセージが表示されていることを確認します。左側のツリー ビューにも **SqlServerLinkedService** が表示されます。
		   
	![SqlServerLinkedService deployment successful][image-editor-sql-linked-service-successful]
	
  
**[リンクされたサービス]** ブレードの **[新しいデータ ストア]** ツール バー ボタンをクリックして、SQL Server リンク サービスを作成することもできます。この方法を使用する場合、ポータルにアクセスするコンピューターで実行される資格情報マネージャー ClickOnce アプリケーションを使用して、データ ソースの資格情報を設定します。ゲートウェイ コンピューターとは異なるコンピューターからポータルにアクセスする場合は、資格情報マネージャー アプリケーションがゲートウェイ コンピューターに接続できることを確認する必要があります。アプリケーションがゲートウェイ コンピューターにアクセスできない場合、データ ソースの資格情報を設定したり、データ ソースへの接続をテストしたりすることはできません。

#### Azure ストレージ アカウント用のリンクされたサービスを追加する
 
1. **エディター**のツール バーで **[新しいデータ ストア]** ボタンをクリックし、ドロップダウン メニューから **[Azure Storage]** を選択します。Azure Storage のリンクされたサービスを作成するための JSON テンプレートが右側のウィンドウに表示されます。 

	![Editor New data store button][image-editor-newdatastore-button]
    
6. **<accountname>** と **<accountkey>** を、Azure ストレージ アカウントのアカウント名とアカウント キーに置き換えます。

	![BLOB ストレージ JSON の編集][image-editor-blob-storage-json]
	
	JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](http://go.microsoft.com/fwlink/?LinkId=516971)を参照してください。

6. ツール バーの **[デプロイ]** をクリックして、StorageLinkedService をデプロイします。タイトル バーに **"リンクされたサービスが正常に作成されました"** というメッセージが表示されていることを確認します。

	![Editor Blob Storage Deploy][image-editor-blob-storage-deploy]

 
## 手順 3. 入力データセットと出力データセットを作成する
このステップでは、コピー操作 (内部設置型の SQL Server データベース => Azure BLOB ストレージ) の入力および出力データを表す入出力データ セットを作成します。データセットまたはテーブル (四角形のデータセット) を作成する前に、以下を実施する必要があります (一覧の後に詳細な手順があります)。

- リンク サービスとしてデータ ファクトリに追加した SQL Server Database 内に "**emp**" という名前のテーブルを作成し、このテーブルにサンプル エントリをいくつか挿入します。
- - 「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルを終えていない場合は、リンク サービスとしてデータ ファクトリに追加した Azure BLOB ストレージ アカウント内に、"**adftutorial**" という名前の BLOB コンテナーを作成します。

### チュートリアル用に内部設置型の SQL Server を用意します。

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

1.	**Data Factory エディター**で、コマンド バーの **[新しいデータセット]** をクリックし、**[オンプレミスの SQL]** をクリックします。 
2.	右側のウィンドウの JSON を次のテキストに置き換えます。    

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
	
	- location の **type** を **OnPremisesSqlServerTableLocation** に設定します。
	- **tableName** を **emp** に設定します。
	- **linkedServiceName** を **SqlServerLinkedService** (手順 2. で作成したリンク サービス) に設定します。
	- Azure Data Factory の別のパイプラインでは生成されない入力テーブルの場合、JSON 内で **waitOnExternal** セクションを指定する必要があります。これは、入力データが Azure Data Factory サービスの外部で生成されることを意味します。   

	JSON のプロパティの詳細については、[JSON スクリプティング リファレンス][json-script-reference]を参照してください。

2. コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。タイトル バーに "**テーブルは正常にデプロイされました**" というメッセージが表示されていることを確認します。


### 出力テーブルの作成

1.	**Data Factory エディター**で、コマンド バーの **[新しいデータセット]** をクリックし、**[Azure BLOB ストレージ]** をクリックします。
2.	右側のウィンドウの JSON を次のテキストに置き換えます。 

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
	
	- location の **type** を **AzureBlobLocation** に設定します。
	- **linkedServiceName** を **StorageLinkedService** (手順 2. で作成したリンク サービス) に設定します。
	- **folderPath** を **adftutorial/outfromonpremdf** に設定します。outfromonpremdf は adftutorial コンテナー内のフォルダーです。必要な操作は **adftutorial** コンテナーの作成だけです。
	- **availability** が **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。Data Factory サービスは、Azure SQL Database 内の **emp** テーブルに 1 時間ごとに出力データ スライスを生成します。 

	**入力テーブル**の **fileName** を指定しない場合、入力フォルダー (**folderPath**) 内のすべてのファイル/BLOB が入力と見なされます。JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。例については、[チュートリアル][adf-tutorial]のサンプル ファイルをご覧ください。
 
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

 

	JSON のプロパティの詳細については、[JSON スクリプティング リファレンス][json-script-reference]を参照してください。

2.	コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。タイトル バーに "**テーブルは正常にデプロイされました**" というメッセージが表示されていることを確認します。
  

## 手順 4. パイプラインを作成して実行する
この手順では、**EmpOnPremSQLTable** を入力として使用し、**OutputBlobTable** を出力として使用する**コピー アクティビティ**を 1 つ使用する**パイプライン**を作成します。

1.	コマンド バーの **[新しいパイプライン]** をクリックします。このボタンが表示されない場合は、**[...] (省略記号)** をクリックしてコマンド バーを展開します。
2.	右側のウィンドウの JSON を次のテキストに置き換えます。   


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
 
	- activities セクションには、**type** が **CopyActivity** に設定されたアクティビティが 1 つだけあります。
	- アクティビティの**入力**を **EmpOnPremSQLTable** に設定し、**出力**を **OutputBlobTable** に設定します。
	- **transformation** セクションでは、**ソースの種類**として **SqlSource** を指定し、**シンクの種類**として **BlobSink を**指定します。- **SqlSource** の **sqlReaderQuery** プロパティに、SQL クエリ "**select * from emp**" を指定します。

	**start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。start と end の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。**end** の時刻は省略可能ですが、このチュートリアルでは使用します。
	
	**end** プロパティの値を指定しない場合、"**start + 48 hours**" として計算されます。パイプラインを無期限に実行する場合は、**9/9/9999** を **end** プロパティの値として指定します。
	
	Azure Data Factory テーブルごとに定義された **Availability** プロパティに基づいて、データ スライスを処理する期間を定義します。
	
	上記の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。
	
2. コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。タイトル バーに "**パイプラインは正常にデプロイされました**" というメッセージが表示されていることを確認します。
5. **[X]** をクリックして **[エディター]** ブレードを閉じます。もう一度 **[X]** をクリックして、[ADFTutorialDataFactory] ブレードをツール バーおよびツリー ビューと共に閉じます。**"保存されていない編集は破棄されます"** というメッセージが表示されたら、**[OK]** をクリックします。
6. **ADFTutorialOnPremDF** の **[DATA FACTORY]** ブレードに戻ります。

**ご利用ありがとうございます。** これで、Azure Data Factory、リンクされたサービス、テーブル、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。

### ダイアグラム ビューでの Data Factory の表示 
1. **Azure プレビュー ポータル**の **ADFTutorialOnPremDF** データ ファクトリのホーム ページで、**[ダイアグラム]** タイルをクリックします。

	![Diagram Link][image-data-factory-diagram-link]

2. 以下のような図が表示されるはずです。

	![Diagram View][image-data-factory-diagram-view]

	パイプラインとテーブルは、拡大、縮小、100% に拡大、ウィンドウのサイズに合わせて大きさを変更、自動的に配置などの表示が可能です。また、系列情報を表示 (選択した項目の上位項目や下位項目を強調表示) することもできます。オブジェクト (入力/出力テーブルまたはパイプライン) をダブルクリックすると、そのオブジェクトのプロパティを表示できます。

## 手順 5. データセットとパイプラインを監視する
このステップでは、Azure ポータルを使用して、Azure Data Factory の状況を監視します。PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。監視にコマンドレットを使用する方法の詳細については、「[Azure PowerShell を使用した Azure Data Factory の監視と管理][monitor-manage-powershell]」をご覧ください。

1. **Azure プレビュー ポータル**に移動します (閉じていた場合)。
2. **ADFTutorialOnPremDF** のブレードが開いていない場合は、**スタート画面**で **ADFTutorialOnPremDF** をクリックして開きます。
3. このブレードには、作成したテーブルとパイプラインの**数**と**名前**が表示されます。

	![Data Factory Home Page][image-data-factory-homepage-2]
4. **[データセット]** タイルをクリックします。
5. **[データセット]** ブレードで、**[EmpOnPremSQLTable]** をクリックします。

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. 現在の時刻までのデータ スライスが既に生成されており、**[準備完了]** になっています。これは、SQL Server データベースに挿入したデータが、現在まで残っているためです。下部の **[問題のあるスライス]** セクションにスライスが表示されていないことを確認します。


	**[最近更新したスライス]** と **[最近失敗したスライス]** の一覧は、どちらも **[最終更新時刻]** で並べ替えられます。次の状況では、スライスの更新時刻が変更されます。
    

	-  **Set-AzureDataFactorySliceStatus** を使用したり、スライスの **[スライス]** ブレードで **[実行]** をクリックしたりすることで、スライスの状態を手動で更新した場合。
	-  スライスの実行 (実行の開始、実行の終了と失敗、実行の終了と成功など) により、スライスの状態が変わります。
 
	一覧のタイトルをクリックするか、**[...] (省略記号)** をクリックすると、さらに多くのスライスが一覧表示されます。スライスをフィルター処理するには、ツール バーの **[フィルター]** をクリックします。
	
	代わりに、スライスの開始時刻と終了時刻で並べ替えられたデータ スライスを表示するには、**[データ スライス (スライスの時刻別)]** タイルをクリックします。

7. **[データセット]** ブレードで、**[OutputBlobTable]** をクリックします。

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. 現在の時刻までのスライスが生成されており、**[準備完了]** 状態であることを確認します。現在の時刻までのスライスの状態が **[準備完了]** になるまで待ちます。
9. 一覧の任意のデータ スライスをクリックすると、**[データ スライス]** ブレードが表示されます。

	![Data Slice Blade][image-data-factory-dataslice-blade]

	スライスが **[準備完了]** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、**[準備完了でない上位スライス]** の一覧に表示されます。

10. 下部にある一覧の **[アクティビティの実行]** をクリックして、**[アクティビティの実行の詳細]** を表示します。

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. **[X]** をクリックしてすべてのブレードを閉じ、**ADFTutorialOnPremDF** のホーム ブレードに戻ります。
14. (省略可能) **[パイプライン]** をクリックし、**[ADFTutorialOnPremDF]** をクリックして、入力テーブル (**Consumed**) または出力テーブル (**Produced**) をドリル スルーします。
15. **Azure Storage Explorer** などのツールを使用して、出力を確認します。

	![Azure ストレージ エクスプローラー][image-data-factory-stroage-explorer]


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

	
4. Azure PowerShell で **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript** フォルダーに移動します。次のコマンドに示すように、ローカル変数 **$Key** に関連付けられた **RegisterGateway.ps1** スクリプトを実行して、コンピューターにインストールされているクライアント エージェントを、前に作成した論理ゲートウェイに登録します。

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. **Get-AzureDataFactoryGateway** コマンドレットを使用して、データ ファクトリ内のゲートウェイの一覧を取得できます。**[状態]** に **[オンライン]** と表示されている場合、ゲートウェイをいつでも使用できることを意味します。

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

**Remove-AzureDataFactoryGateway** コマンドレットを使用して、ゲートウェイを削除できます。また、**Set-AzureDataFactoryGateway** コマンドレットを使用して、ゲートウェイの記述を更新できます。これらのコマンドレットの文法やその他の詳細については「Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)」を参照してください。



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

<!---HONumber=July15_HO4-->