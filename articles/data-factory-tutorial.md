<properties 
	pageTitle="Azure Data Factory を使用してログ ファイルの移動と処理を行う" 
	description="この高度なチュートリアルでは、実際に使用されているものに近いシナリオについて説明すると共に、Azure データ ファクトリ サービスを使用してそのシナリオを実装します。" 
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
	ms.date="1/28/2015" 
	ms.author="spelluru"/>

# チュートリアル: Data Factory を使用してログ ファイルの移動と処理を行う
この記事では、Azure Data Factory を使用してデータをログ ファイルから洞察へと変化させながら、ログ処理の標準的なシナリオを包括的なチュートリアルとして提供します。 

## シナリオ
Contoso は、ゲーム機、携帯機器、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。これらのゲームはそれぞれが大量のログを産み出します。Contoso 社の目標は、これらのゲームが産み出すログを収集解析することで有益な情報を手に入れ、アップセルやクロスセルの機会を見極め、新しい魅力的な機能などを開発する、つまりビジネスを向上させて顧客により良い体験を提供することです。
 
このチュートリアルでは、サンプル ログを収集、処理して参照データで強化し、このデータを変換して Contoso 社 が最近開始したマーケティング キャンペーンの有効性を評価します。

## チュートリアルの準備をする
1.	「[Azure Data Factory の概要][adfintroduction]」を読んで Azure Data Factory の概要を理解し、全体的な概念を把握します。
2.	このチュートリアルを実施するには Azure サブスクリプションが必要です。サブスクリプションの入手方法の詳細については、「[Purchase Options (購入オプション)][azure-purchase-options]」、「[Member Offers (メンバー プラン)][azure-member-offers]」、または「[Free Trial (無料評価版)][azure-free-trial]」を参照してください。
3.	[Azure PowerShell][download-azure-powershell] をダウンロードしてコンピューターにインストールする必要があります。 
2.	**(推奨)** ポータルとコマンドレットに慣れるための簡単なチュートリアルについての記事「[Azure Data Factory を使ってみる][adfgetstarted]」にあるチュートリアルの見直しと練習をしてください。
3.	**(推奨)** パイプラインを作成して内部設置型のデータ ソースから Azure BLOB ストアにデータを移動するチュートリアルについての記事「[Azure Data Factory で Pig と Hive を使用する][usepigandhive]」の見直しと練習をしてください。
4.	[ADFWalkthrough][adfwalkthrough-download] ファイルを **C:\ADFWalkthrough** フォルダーにダウンロードしますが、**以下のフォルダー構造は維持してください。**
	- **Pipelines:**   パイプラインの定義を含む JSON ファイルが含まれています。
	- **Tables:**   テーブルの定義を含む JSON ファイルが含まれています。
	- **LinkedServices: ** ストレージおよびコンピューティング (HDInsight) クラスターの定義を含む JSON ファイルが含まれています。 
	- **Scripts: ** これにはデータの処理に使用され、パイプラインから呼び出される Hive および Pig スクリプトが含まれています。
	- **SampleData: ** このチュートリアル用のサンプル データが含まれています。
	- **OnPremises: ** 内部設置型のデータへのアクセス方法を示すために使用する JSON ファイルとスクリプトが含まれています。
	- **AzureEnvironmentSetup.ps1: ** Azure 環境を設定するための PowerShell スクリプトです。
	- **uploadSampleDataAndScripts.ps1: ** このスクリプトは、サンプル データおよびスクリプトを Azure にアップロードします。
5. 以下の Azure リソースが作成済みであることを確認してください。			
	- Azure ストレージ アカウント
	- Azure SQL Database
	- Azure HDInsight クラスター Version 3.1 以降	
7. Azure リソースを作成したなら、上記の各リソースへの接続に必要な情報を持っていることを確認します。
 	- **Azure ストレージ アカウント** - アカウント名とアカウント キー。  
	- **Azure SQL Database** - サーバー、データベース、ユーザー名、およびパスワード。
	- **Azure HDInsight クラスター** - HDInsight クラスター名、ユーザー名、パスワード、このクラスターに関連付けられている Azure ストレージのアカウント名およびアカウント キー。  
 8. **Azure PowerShell** を起動し、**C:\ADFWalkthrough** へ移動して準備スクリプト **\AzureEnvironmentSetup.ps1** を実行します。
 
		スクリプトを実行する前に、サブスクリプションに関連付けられている サブスクリプション名と Microsoft アカウントを用意する必要があります。

		Azure PowerShell   を起動するたびに、上記のスクリプトを実行して Azure 環境をセットアップする必要があります。構成はセッション間で引き継がれず、同時に複数のセッションを使用した場合でも個別に実行する必要があります。

		または、**Add-AzureAccount** コマンドレットを使用して Azure にサインインし、**Select-AzureSubscription** コマンドレットを使用してサブスクリプションを選択 (複数のサブスクリプションを持っている場合) することができます。
	

## 概要
全体のワークフローは以下のとおりです。
	![Tutorial End to End Flow][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline** が未処理のゲーム イベントを BLOB ストレージ (RawGameEventsTable) から読み取り、年、月、日に基づくパーティションを作成します (PartitionedGameEventsTable)。
2. **EnrichGameLogsPipeline** はパーティションに分割されたゲーム イベント (PartitionGameLogsPipeline の出力結果である PartitionedGameEvents table) と地理的コード (RefGetoCodeDictionaryTable) を結合し、IP アドレスを対応する地理的な位置 (EnrichedGameEventsTable) にマッピングすることでデータを強化します。
3. **AnalyzeMarketingCampaignPipeline** パイプラインは、強化されたデータ (EnrichGameLogsPipeline によって生成される EnrichedGameEventTable) を活用し、それを広告データ (RefMarketingCampaignnTable) と共に処理してマーケティング キャンペーンの有効性という最終的なアウトプットを作成しますが、これは解析のために Azure SQL Database (MarketingCampainEffectivensessSQLTable) と Azure BLOB ストレージ (MarketingCampaignEffectivenessBlobTable) へコピーされます。
    
## 作成、配置、ワークフローの監視
1. [手順 1.サンプル データとスクリプトのアップロード](#MainStep1)。この手順では、すべてのサンプル データ (すべてのログと参照データを含む) とワークフローによって実行される Hive/Pig スクリプトをアップロードします。実行するスクリプトは、Azure SQL Database (MarketingCampaigns)、テーブル、ユーザー定義型、およびストアド プロシージャの作成も行います。
2. [手順 2.Azure データ ファクトリの作成](#MainStep2)。この手順では、"LogProcessingFactory" という名前の Azure データ ファクトリを作成します。
3. [手順 3.リンクされたサービスの作成](#MainStep3)。この手順では、以下のリンクされたサービスを作成します。 
	
	- 	**StorageLinkedService**。未処理のゲーム イベント、パーティションに分割されたゲーム イベント、強化されたゲーム イベント、マーケティング キャンペーンの有効な情報を含む Azure ストレージの場所をリンクし、LogProcessingFactory へのマーケティング データの参照を行います。   
	- 	**AzureSqlLinkedService**。マーケティング キャンペーンの有効性の情報を含む Azure SQL データベースをリンクします。 
	- 	**HDInsightStorageLinkedService**。HDInsightLinkedService が参照する HDInsight クラスターに関連付けられている Azure BLOB ストレージをリンクします。 
	- 	**HDInsightLinkedService**。Azure HDInsight クラスターを LogProcessingFactory にリンクします。このクラスターはデータの pig/hive 処理に使用されます。 
 		
4. [手順 4.テーブルの作成](#MainStep4)。この手順では、以下のテーブルを作成します。  	
	
	- **RawGameEventsTable**。このテーブルは、未処理のゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/rawgameevents/)。 
	- **PartitionedGameEventsTable**。このテーブルは、パーティションに分割されたゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/partitionedgameevents/)。 
	- **RefGeoCodeDictionaryTable**。このテーブルは、地理的参照コードの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/refdata/refgeocodedictionary/)。
	- **RefMarketingCampaignTable**。このテーブルは、マーケティング キャンペーンの参照データの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/refdata/refmarketingcampaign/)。
	- **EnrichedGameEventsTable**。このテーブルは、強化されたゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/enrichedgameevents/)。
	- **MarketingCampaignEffectivenessSQLTable**。このテーブルは、AzureSqlLinkedService によって定義された、マーケティング キャンペーンの有効性データを含む Azure SQL Database 内で SQL テーブル (MarketingCampaignEffectiveness) を指定します。 
	- **MarketingCampaignEffectivenessBlobTable**。このテーブルは、マーケティング キャンペーンの有効性データの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/marketingcampaigneffectiveness/)。 

	
5. [手順 5.パイプラインの作成およびスケジュール設定](#MainStep5)。この手順では、以下のパイプラインを作成します。
	- **PartitionGameLogsPipeline**。このパイプラインは、BLOB ストレージ (RawGameEventsTable) から未処理のゲーム イベントを読み取り、年、月、日に基づくパーティション (PartitionedGameEventsTable) を作成します。 


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**。このパイプラインは、パーティションに分割されたゲーム イベント (PartitionGameLogsPipeline の出力結果である PartitionedGameEvents table) と地理的コード (RefGetoCodeDictionaryTable) を結合し、IPアドレスを対応する地理的な位置 (EnrichedGameEventsTable) にマッピングすることでデータを強化します。 

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**。このパイプラインは、強化されたゲーム イベントのデータ (EnrichGameLogsPipeline によって生成される EnrichedGameEventTable) を活用し、それを広告データ (RefMarketingCampaignnTable) と共に処理してマーケティング キャンペーンの有効性という最終的なアウトプットを作成しますが、これは解析のために Azure SQL database (MarketingCampainEffectivensessSQLTable) と Azure BLOB ストレージ (MarketingCampaignEffectivenessBlobTable) へコピーされます。


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [手順 6.パイプラインとデータ スライスの監視](#MainStep6)。この手順では、Azure ポータルを使用して、パイプライン、テーブル、およびデータ スライスを監視します。

### <a name="MainStep1"></a>手順 1.サンプル データとスクリプトのアップロード
この手順では、すべてのサンプル データ (すべてのログと参照データを含む) とワークフローによって呼び出される Hive/Pig スクリプトをアップロードします。実行するスクリプトは、**"MarketingCampaigns"** という名前の Azure SQL データベース、テーブル、ユーザー定義型、およびストアド プロシージャの作成も行います。 

テーブル、ユーザー定義型、およびストアド プロシージャは、マーケティング キャンペーン有効性の結果を Azure BLOB ストレージから Azure SQL データベースに移動する際に使用されます。

1. お好みのエディターで **C:\ADFWalkthrough** フォルダー (または展開したファイルを含むフォルダー) から **uploadSampleDataAndScripts.ps1** を開き、強調表示されている部分を自分のクラスター情報に置き換え、ファイルを保存します。


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [WACOM.NOTE] このスクリプトを使用するには、コンピューターに sqlcmd ユーティリティがインストールされている必要があります。SQL Server がインストール済みであれば、これもインストールされています。そうでない場合は、このユーティリティを[ダウンロード][sqlcmd-install]し、インストールしてください。 
	> または、C:\ADFWalkthrough\Scripts フォルダーにあるファイルを使用して pig/hive スクリプトおよびサンプル ファイルを BLOB ストレージ内の adfwalkthrough コンテナーにアップロードし、Azure SQL データベースである MarketingCamapaigns 内に MarketingCampaignEffectiveness テーブルを作成することも可能です。   
2. ローカル コンピューターに Azure SQL Database へのアクセス権があることを確認してください。アクセスを有効にするには、**Azure の管理ポータル**またはマスター データベース上の **sp_set_firewall_rule** を使用して、コンピューターの IP アドレスに対するファイアウォール規則を作成します。この変更が有効になるまで最大で 5 分かかる場合があります。「[Setting firewall rules for Azure SQL (Azure SQL のファイアウォール規則を設定する)][azure-sql-firewall]」を参照してください。
3. **Azure PowerShell** を起動します。 
4. サンプルを展開した場所に移動します (例: **C:\ADFWalkthrough**)
5. **uploadSampleDataAndScripts.ps1**を実行します。 
6. スクリプトが正常に実行されると、以下が表示されます。

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created 'MarketingCampaigns' Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


### <a name="MainStep2"></a>手順 2.Azure データ ファクトリの作成
この手順では、**"LogProcessingFactory"** という名前の Azure データ ファクトリを作成します。

1.	[Azure プレビュー ポータル][azure-preview-portal]へのログイン後、左下隅の **[新規]** をクリックし、**[新規]** ブレードの **[Data Factory]** をクリックします。 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	**[Data Factory]** が **[新規]** ブレードに表示されていない場合は、下にスクロールします。 
	
5. **[新しいデータ ファクトリ]** ブレードで、**[名前]** フィールドに「**LogProcessingFactory**」と入力します。

	![Data Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6. **"ADF"** という名前の Azure リソース グループを作成済みでない場合、以下を実施します。
	1. **[リソース グループ名]** をクリックし、**[新しいリソース グループを作成]** をクリックします。
	
		![Resource Group Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. **[リソース グループを作成]** ブレードで、リソース グループの名前を「**ADF**」と入力し、**[OK]** をクリックします。
	
		![Create Resource Group][image-data-factory-tutorial-create-resourcegroup]
7. **[リソース グループ名]** の **[ADF]** を選択します。  
8.	**[新しいデータ ファクトリ]** ブレードにおいて、既定では **[スタート画面に追加]** が選択されていることに注意してください。これにより、スタート画面 (Azure プレビュー ポータルへのログイン時に表示される画面) のデータ ファクトリにリンクが追加されます。

	![Create Data Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	**[新しいデータ ファクトリ]** ブレードで、**[作成]** をクリックしてデータ ファクトリを作成します。
10.	データ ファクトリが作成されると、**LogProcessingFactory** というタイトルの **[データ ファクトリ]** ブレードが表示されます。

	![Data Factory Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	表示されない場合は、次のいずれかを行います。

	- **スタート画面** (ホーム ページ) の **[LogProcessingFactory]** をクリックする。
	- 左側の **[参照]** をクリックし、**[すべて]** をクリックし、**[データ ファクトリ]** をクリックし、最後に目的のデータ ファクトリをクリックする。
 

 
### <a name="MainStep3"></a>手順 3.リンクされたサービスの作成

この手順では、以下のリンクされたサービスを作成します: StorageLinkedService、AzureSqlLinkedService、HDInsightStorageLinkedService、および HDInsightLinkedService。

1.	**[LogProcessingFactory]** ブレードで、**[リンクされたサービス]** タイルをクリックします。

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. **[リンクされたサービス]** ブレードで、コマンド バーから **[+ データ ストア]** をクリックします。	

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. **[新しいデータ ストア]** ブレードで、**[名前]** フィールドに「**StorageLinkedService**」と入力し、**[種類 (設定が必要)]** をクリックし、**[Azure ストレージ アカウント]** を選択します。

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. **[新しいデータ ストア]** ブレードに、2 つの新しいフィールドが表示されます。**[アカウント名]** と **[アカウント キー]** です。**Azure ストレージ アカウント**のアカウント名とアカウント キーを入力します。

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	以下に示すように、ポータルから Azure ストレージ アカウントのアカウント名とアカウント キーを取得できます。

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. [新しいデータ ストア] ブレードで **[OK]** をクリックすると、**[リンクされたサービス]** ブレードの **[データ ストア]** リスト内に **[StorageLinkedService]** が表示されるはずです。メッセージを見るには **[通知]** ハブ (左側) を確認します。

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. **手順 2. ～ 5.** を繰り返して次の名前のリンクされたサービスを作成します: **HDInsightStorageLinkedService**。これは、HDInsight クラスターで使用するストレージです。
7. **StorageLinkedService** と **HDInsightStorageLinkedService** が両方とも [リンクされたサービス] ブレードに表示されていることを確認します。
8. **[リンクされたサービス]** ブレードで、コマンド バーから **[+ データ ストア]** をクリックします。
9. 名前を「**AzureSqlLinkedService**」と入力します。
10. **[種類 (設定が必要)]** をクリックし、**[Azure SQL Database]** を選択します。
11. これで、**[新しいデータ ストア]** ブレードに以下の追加フィールドが表示されるはずです。Azure SQL Database の **サーバー**、**データベース**名、**ユーザー名**、および**パスワード**を入力し、**[OK]** をクリックします。
	1. **データベース**には「**MarketingCampaigns**」と入力します。これは手順 1. で実行したスクリプトによって作成された Azure SQL データベースです。このデータベースが本当にスクリプトによって作成されたものか確認する必要があります (エラーが発生していた場合に備えて)。
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		Azure 管理ポータルからこれらの値を取得するには、[MarketingCampaigns データベースの SQL Database 接続文字列を表示する] をクリックします。

		![Azure SQL Database Connection String][image-data-factory-tutorial-azuresql-database-connection-string]

12. 作成した 3 つのデータ ストアがすべて表示されていることを確認します。**StorageLinkedService**、**HDInsightStorageLinkedService**、および **AzureSqlLinkedService** です。
13. リンクされたサービスをもう 1 つ作成する必要がありますが、これはコンピューティング サービス、特に **Azure HDInsight クラスター**向けのものです。ポータルでは、まだリンクされたコンピューティング サービスの作成をサポートしていません。そのため、Azure PowerShell を使用して、このリンクされたサービスを作成する必要があります。 
14. 既に起動済みの場合は **Azure PowerShell** に切り替え、そうでない場合は **Azure PowerShell** を起動します。
15. Azure Data Factory コマンドレットは **AzureResourceManager** モードでのみ使用できるので、このモードに切り替えます。

		Switch-AzureMode AzureResourceManager

16. **C:\ADFWalkthrough** 内、またはファイルを展開したフォルダーもしくは場所から **LinkedServices** サブフォルダ―に移動します。
17. お好みのエディターで **HDInsightLinkedService.json** を開き、強調表示されている部分を自分のクラスター情報に置き換え、ファイルを保存します。

        "clusterUri": "https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "<hdiusername>",
        "location": "<hdiregion>",
        "password": "<hdipassword>",

18. 以下のコマンドを使用して、データ ファクトリの名前に $df 変数を設定します。

		$df = "LogProcessingFactory"
19. **New-AzureDataFactoryLinkedService** コマンドレットを使用して以下のようにリンクされたサービスを作成します。ストレージ アカウントから始めます。

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	ResourceGroupName、DataFactoryName または LinkedService の名前に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、リンクされたサービスの JSON ファイルが見つからない場合は、そのファイルの完全なパスを入力します。
20. 以下に示すように、**[リンクされたサービス]** ブレードに 4 つのリンクされたサービスがすべて表示されるはずです。[リンクされたサービス] ブレードが開いていない場合は、**[LogProcessingFactory]** の **[データ ファクトリ]** ページ内で [リンクされたサービス] をクリックします。[リンクされたサービス] ブレードが更新されるまで数秒かかる場合があります。

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

### <a name="MainStep4"></a>手順 4.テーブルの作成 
この手順では、以下のテーブルを作成します。 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial End-to-End Flow][image-data-factory-tutorial-end-to-end-flow]
 
上の図は、中央の列がパイプラインを、上と下の列がテーブルを表しています。 

Azure ポータルはデータ セットとテーブルの作成をまだサポートしていないため、今回のリリースでは Azure PowerShell を使用してテーブルを作成する必要があります。

#### テーブルを作成するには

1.	Azure PowerShell で、サンプルを展開した場所から **Tables** フォルダー (**C:\ADFWalkthrough\Tables\**) に移動します。 
2.	**New-AzureDataFactoryTable** コマンドレットを使用して、以下のように **RawGameEventsTable**.json のテーブルを作成します。	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RawGameEventsTable.json

	ResourceGroupName および DataFactoryName に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、テーブルの JSON ファイルがコマンドレットで見つけられない場合は、そのファイルの完全なパスを入力します。

3. 前の手順を繰り返して、以下のテーブルを作成します。	
		
	- **PartitionedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionedGameEventsTable.json

	- **RefGeoCodeDictionaryTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefGeoCodeDictionaryTable.json

	- **RefMarketingCampaignTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefMarketingCampaignTable.json

	- **EnrichedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichedGameEventsTable.json

	- **MarketingCampaignEffectivenessSQLTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessSQLTable.json

	- **MarketingCampaignEffectivenessBlobTable**
			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessBlobTable.json



4. **Azure プレビュー ポータル**内で、**[LogProcessingFactory]** の **[データ ファクトリ]** ブレードの **[データセット]** をクリックし、すべてのデータセットが表示されているか確認します (テーブルは四角形のデータセットです)。 

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	また、Azure PowerShell から次のコマンドを使用することも可能です。
			
		Get-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df

	


### <a name="MainStep5"></a>手順 5.パイプラインの作成およびスケジュール設定
この手順では、以下のパイプラインを作成します: PartitionGameLogsPipeline、EnrichGameLogsPipeline、および AnalyzeMarketingCampaignPipeline。

1. **Windows エクスプローラー**で、**C:\ADFWalkthrough** フォルダーから (またはサンプルを展開した場所から) **Pipelines** サブフォルダ―に移動します。
2.	好みのエディターで **PartitionGameLogsPipeline.json** を開き、強調表示されている部分をデータ ストレージ アカウント情報の自分のストレージ アカウントに置き換えて、ファイルを保存します。
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. 手順を繰り返して、以下のパイプラインを作成します。
	1. **EnrichGameLogsPipeline**.json (出現回数 3 回)
	2. **AnalyzeMarketingCampaignPipeline**.json (出現回数 3 回)

	**重要: ** すべての <storageaccountname> を自分のストレージ アカウントに置き換えたことを確認してください。 
 
4.  **Azure PowerShell** で、**C:\ADFWalkthrough** フォルダーから (またはサンプルを展開した場所から) **Pipelines** サブフォルダ―に移動します。
5.  **New-AzureDataFactoryPipeline** コマンドレットを使用して **PartitionGameLogspeline**.json 用のパイプラインを以下のように作成します。	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionGameLogsPipeline.json

	ResourceGroupName、DataFactoryName、またはパイプライン名に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、パイプラインの JSON ファイルのファイルの完全パスを提供します。
6. 前の手順を繰り返して、以下のパイプラインを作成します。
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\AnalyzeMarketingCampaignPipeline.json

7. **Get-AzureDataFactoryPipeline** コマンドレットを使用してパイプラインの一覧を取得します。
			
		Get-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df

8. パイプラインを作成したら、データ処理を実行する期間を指定できます。パイプラインの有効期間を指定することで、各 ADF テーブルに対して定義された Availability プロパティに基づいてデータ スライスが処理される期間を定義します。

パイプラインの有効期間を指定するために、Set-AzureDataFactoryPipelineActivePeriod コマンドレットが使用できます。このチュートリアルでは、サンプル データは 05/01 ～ 05/05 のものです。2014-05-01 を StartDateTime として使用します。EndDateTime は任意です。
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name PartitionGameLogsPipeline
  
9. パイプラインの有効期間を設定することを確認します。
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. 前の 2 つの手順を繰り返して、以下のパイプラインの有効期間を設定します。
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline** 
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name AnalyzeMarketingCampaignPipeline

11. **Azure プレビュー ポータル**で、**[LogProcessingFactory]** の **[データ ファクトリ]** ブレードの **[パイプライン]** タイル (パイプライン名ではない) をクリックすると、作成したパイプラインが表示されるはずです。

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. **[LogProcessingFactory]** の **[データ ファクトリ]** ブレードで、**[ダイアグラム]** をクリックします。

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. 表示されているダイアグラムは並べ替えることが可能で、以下のダイアグラムは上部が直接の入力を示し、下部が出力を示しています。**PartitionGameLogsPipeline** の出力が EnrichGameLogsPipeline に入力として渡され、**EnrichGameLogsPipeline** の出力が **AnalyzeMarketingCampaignPipeline** に渡されていることがわかります。タイトルをダブルクリックして、ブレードが示すアイテムについての詳細を表示します。

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**ご利用ありがとうございます。** Azure データ ファクトリ、リンクされたサービス、パイプライン、テーブルを作成し、ワークフローを開始することに成功しました。 


### <a name="MainStep6"></a>手順 6.パイプラインとデータ スライスの監視 

1.	[LogProcessingFactory] の [データ ファクトリ] ブレードを開いていない場合、以下のいずれかを実施できます。
	1.	**スタート画面**で **[LogProcessingFactory]** をクリックします。データ ファクトリの作成中に、**[スタート画面に追加]** オプションが自動でオンになっています。

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. **[参照]** ハブをクリックし、**[すべて]** をクリックします。
	 	
		![Monitoring Hub Everything][image-data-factory-monitoring-hub-everything]

	3. **[参照]** ブレードで、**[データ ファクトリ]** を選択し、**[データ ファクトリ]** ブレードで **[LogProcessingFactory]** を選択します。

	![Monitoring Browse Datafactories][image-data-factory-monitoring-browse-datafactories]
2. いくつかの方法でデータ ファクトリが監視できます。パイプラインまたはデータ セットで監視が始められます。パイプラインで監視を始め、さらに詳しく学びましょう。 
3.	**[データ ファクトリ]** ブレードの **[パイプライン]** をクリックします。 
4.	[パイプライン] ブレードで **[PartitionGameLogsPipeline]** をクリックします。 
5.	**[PartitionGameLogsPipeline]** の **[パイプライン]** ブレードを見ると、パイプラインが **[RawGameEventsTable]** データセットを使用していることがわかります。  **[RawGameEventsTable]**をクリックします。

	![Pipeline Consumed and Produced][image-data-factory-monitoring-pipeline-consumed-produced]

6. **[RawGameEventsTable]** の [テーブル] ブレードに、すべてのスライスが表示されています。以下のスクリーン ショットでは、すべてのスライスが **[準備完了]** の状態で、問題のあるスライスはありません。これは、そのデータがすぐに処理できることを意味します。	

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. **[PartiionGameLogsPipeline]** の **[パイプライン]** ブレードで、**[生成済み]** をクリックします。 
8. このパイプラインが生成するデータ セットの一覧が表示されるはずです。 
9. **[生成済みデータセット]** ブレードの **[PartitionedGameEvents]** テーブルをクリックします。 
10.	すべてのスライスの **[状態]** が **[準備完了]** になっていることを確認します。 
11.	**[準備完了]** になっているスライスの 1 つをクリックし、そのスライスの **[データ スライス]** ブレードを表示させます。

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	エラーが発生していた場合、ここに **[失敗]** という状態が表示されます。  また、スライスがどの程度の速さで処理されるかによって、両方のスライスが **[準備完了]** や **[検証を保留中]** の状態で表示される場合もあります。
 
	「[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference]」を参照し、考えられるすべてのスライスの状態について理解してください。

12.	**[データ スライス]** ブレードで、**[アクティビティの実行]** リストから [実行] をクリックします。そのスライスの [アクティビティの実行] ブレードが表示されるはずです。以下の **[アクティビティの実行の詳細]** ブレードが表示されます。

	![Activity Run Details blade][image-data-factory-monitoring-activity-run-details]

13.	**[ダウンロード]** をクリックしてファイルをダウンロードします。この画面は、HDInsight の処理で発生するエラーのトラブルシューティングを行うときに、特に役立ちます。 
	 
	
すべてのパイプラインが実行を完了すると、Azure SQL データベースである **MarketingCampaigns** 内の **MarketingCampaignEffectivenessTable** を調べて実行結果を確認することができます。 

**ご利用ありがとうございます。**これでワークフローの監視とトラブルシューティングができます。Azure Data Factory を使用してデータを処理し分析結果を得る方法を学びました。

	 

## 内部設置型のデータを使用する

**(推奨)**パイプラインを作成して内部設置型の SQL Server から Azure BLOB ストアにデータを移動するチュートリアルについての記事「[パイプラインが内部設置型のデータを扱えるようにする][useonpremisesdatasources]」内のチュートリアルの確認と練習をしてください。


このチュートリアルでは、パイプラインが内部設置型のデータを扱えるようにするため、その環境を設定する方法を学びます。
 
パーティション -> 強化 -> 分析のワークフローを含む最初のチュートリアルにおいて、ログ処理のシナリオの最後の手順で、マーケティング キャンペーンの有効性のアウトプットが Azure SQL データベースにコピーされました。分析のため、所属する組織内にある内部設置型の SQL Server にこのデータを移動することも可能です。
 
マーケティング キャンペーンの有効性データを Azure BLOB から内部設置型の SQL Server にコピーするため、最初のチュートリアルで導入したものと同じコマンドレット一式を使って、内部設置型のリンクされたサービス、テーブル、およびパイプラインを追加で作成する必要があります。

このチュートリアルでは、以下の手順を実施します。 

1. [手順 1.データ管理ゲートウェイの作成](#OnPremStep1)。Data Management Gateway   は、所属する組織内の内部設置型のデータ ソースに、クラウドからのアクセスを提供するクライアント エージェントです。このゲートウェイによって、内部設置型の SQL Server と Azure データ ストアの間でデータ転送が可能になります。	

	内部設置型の SQL Server データベースをリンクされたサービスとして Azure データ ファクトリに追加する前に、企業環境内に少なくとも 1 つのゲートウェイがインストールされており、それが Azure Data Factory に登録されている必要があります。

2. [手順 2.内部設置型の SQL Server 用にリンクされたサービスを作成する](#OnPremStep2)。この手順では、まず内部設置型の SQL Server コンピューター上にデータベースとテーブルを作成し、その後リンクされたサービス、**OnPremSqlLinkedService** を作成します。  
3. [手順 3.テーブルとパイプラインの作成](#OnPremStep3)。この手順では、**MarketingCampaignEffectivenessOnPremSQLTable** テーブルと **EgressDataToOnPremPipeline** パイプラインを作成します。 

4. [手順 4.パイプラインの監視と結果の確認](#OnPremStep4)。この手順では、Azure ポータルを使用して、パイプライン、テーブル、およびデータ スライスを監視します。


### <a name="OnPremStep1"></a>手順 1.Data Management Gateway の作成

Data Management Gateway は、所属する組織内の内部設置型のデータ ソースに、クラウドからのアクセスを提供するクライアント エージェントです。このゲートウェイによって、内部設置型の SQL Server と Azure データ ストアの間でデータ転送が可能になります。
  
内部設置型の SQL Server データベースをリンクされたサービスとして Azure データ ファクトリに追加する前に、企業環境内に少なくとも 1 つのゲートウェイがインストールされており、それが Azure Data Factory に登録されている必要があります。

すでに使用できるデータ ゲートウェイがある場合は、この手順をスキップします。

1.	論理データ ゲートウェイを作成します。**Azure プレビュー ポータル**で、**[データ ファクトリ]** ブレードの **[リンクされたサービス]** をクリックします。
2.	コマンド バーの **[+ データ ゲートウェイ]** をクリックします。  
3.	**[新しいデータ ゲートウェイ]** ブレードで、**[作成]** をクリックします。
4.	**[作成]** ブレードで、データ ゲートウェイの **[名前]** に「**MyGateway**」と入力します。
5.	**[地域を選択]** をクリックし、必要があれば変更します。 
6.	**[作成]** ブレードで **[OK]** をクリックします。 
7.	**[構成]** ブレードが表示されるはずです。 
8.	**[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。これによりゲートウェイのダウンロード、インストール、およびコンピューター上での構成が行われ、サービスに登録されます。すでにコンピューターにインストール済みのゲートウェイがあり、それをポータル上に作成したこの論理ゲートウェイにリンクさせたい場合、このブレード上のキーを使用して Data Management Gateway Manager (プレビュー) ツールでゲートウェイの再登録をします。

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. **[OK]** をクリックして **[構成]** ブレードを閉じ、また **[OK]** をクリックして **[作成]** ブレードを閉じます。**[リンクされたサービス]** ブレードの **[マイゲートウェイ]** の状態が **[良好]** に変わるまで待ちます。また、**Data Management Gateway Configuration Manager (プレビュー)** ツールを起動し、ゲートウェイの名前がポータル上の名前と一致すること、および **[状態]** が **[登録済み]** であることを確認することも可能です。最新の状態を確認するため、場合によっては [リンクされたサービス] ブレードをいったん閉じて再度開く必要があります。画面が最新の状態に更新されるまで、数分かかる場合があります。	

### <a name="OnPremStep2"></a>手順 2.内部設置型の SQL Server 用にリンクされたサービスを作成する

この手順では、まず内部設置型の SQL Server コンピューター上にデータベースとテーブルを作成し、その後リンクされたサービスを作成します。

#### 内部設置型のデータベースとテーブルを準備する

最初に、SQL Server データベース、テーブル、ユーザー定義型、およびストアド プロシージャを作成する必要があります。これらは **MarketingCampaignEffectiveness** の結果を Azure BLOB から SQL Server データベースに移動するために使用されます。

1.	**Windows エクスプローラー**で、**C:\ADFWalkthrough** (またはサンプルを展開した場所) にある **OnPremises** サブ フォルダーに移動します。
2.	お好みのエディターで **prepareOnPremDatabase&Table.ps1** を開き、強調表示部分を自分の SQL Server 情報に置き換え、ファイルを保存します (**SQL 認証** の詳細を入力してください)。このチュートリアルのために、データベースの SQL 認証を有効にします。 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. **Azure PowerShell** で、**C:\ADFWalkthrough\OnPremises** フォルダーに移動します。
4.	**prepareOnPremDatabase&Table.ps1** を実行します **(& を二重引用符で囲むか、以下のようにします)**。
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Once the script executes successfully, you will see the following:	
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


#### リンクされたサービスの作成

1.	**Azure プレビュー ポータル**で、**[LogProcessingFactory]** の **[データ ファクトリ]** ブレードにある **[リンクされたサービス]** タイルをクリックします。
2.	**[リンクされたサービス]** ブレードで、**[+ データ ストア]** をクリックします。
3.	**[新しいデータ ストア]** ブレードで、**[名前]** に「**OnPremSqlLinkedService**」と入力します。 
4.	**[種類 (設定が必要)]** をクリックし、**[SQL Server]** を選択します。これで、**[新しいデータ ストア]** ブレードに **[データ ゲートウェイ]**、**Server (サーバー)**、**Database (データベース)**、および **CREDENTIALS (資格情報)** の設定が表示されるはずです。 
5.	**[データ ゲートウェイ (必要な設定を構成する)]** をクリックし、前に作成した **[マイゲートウェイ]** を選択します。 
6.	**MarketingCampaigns** データベースのホスト データベース サーバーの **[名前]** を入力します。 
7.	データベースには「**MarketingCampaigns**」と入力します。 
8.	**[資格情報]** をクリックします。 
9.	**[資格情報]** ブレードで、**[ここをクリックして安全に資格情報を設定する]** をクリックします。
10.	これによりワンクリック アプリケーションが初めてインストールされ、**[資格情報の設定]** ダイアログ ボックスが起動します。 
11.	**[資格情報の設定]** ダイアログ ボックスに、**[ユーザー名]** と **[パスワード]**を入力し、**[OK]** をクリックします。ダイアログ ボックスが閉じるまで待ちます。 
12.	**[新しいデータ ストア]** ブレードで、**[OK]** をクリックします。 
13.	**[リンクされたサービス]** ブレードで、**OnPremSqlLinkedService** が一覧に表示され、リンクされたサービスの **[状態]** が **[良好]** であることを確認します。

### <a name="OnPremStep3"></a>手順 3.テーブルとパイプラインの作成

#### 内部設置型の論理テーブルを作成する

1.	**Azure PowerShell** で、**C:\ADFWalkthrough\OnPremises** フォルダーに移動します。 
2.	**New-AzureDataFactoryTable** コマンドレットを使用して以下のように **MarketingCampaignEffectivenessOnPremSQLTable.json** のテーブルを作成します。

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### パイプラインを作成して Azure BLOB から SQL Server へデータをコピーする

1.	**New-AzureDataFactoryPipeline** コマンドレットを使用して以下のように **EgressDataToOnPremPipeline.json** のパイプラインを作成します。

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EgressDataToOnPremPipeline.json
	 
2. **Set-AzureDataFactoryPipelineActivePeriod** コマンドレットを使用して **EgressDataToOnPremPipeline** の有効期間を指定します。

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EgressDataToOnPremPipeline

	「**Y**」と入力して続行します。
	
### <a name="OnPremStep4"></a>手順 4.パイプラインの監視と結果の確認

「[手順 6: テーブルとパイプラインの監視](#MainStep6)  」で説明したものと同じ手順に従い、新しい内部設置型の ADF テーブル用の新しいパイプラインとデータ スライスを監視します。
 
**MarketingCampaignEffectivenessOnPremSQLTable** テーブルのスライスの状態が [準備完了] に変われば、パイプラインがスライスの実行を完了したことを意味します。結果を表示するには、SQL Server 内の **MarketingCampaigns** データベースの **MarketingCampaignEffectiveness** テーブルにクエリを実行します。
 
ご利用ありがとうございます。内部設置型のデータを使用するためのチュートリアルを無事に終了しました。 
 
## 関連項目

記事 | 説明
------ | ---------------
[パイプラインが内部設置型のデータを扱えるようにする][useonpremisesdatasources] | この記事には、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[Data Factory で Pig と Hive を使用する][usepigandhive] | この記事には、HDInsight アクティビティを使用して hive/pig スクリプトを実行し、入力データを処理して出力データを生成する方法を説明したチュートリアルが記載されています。 
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。 
[Azure PowerShell を使用した Azure Data Factory の監視と管理][monitor-manage-using-powershell] | この記事では、Azure PowerShell コマンドレットを使用して Azure Data Factory を監視する方法について説明しています。記事に含まれている例を ADFTutorialDataFactory で試すことができます。
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。エラーを発生させて (Azure SQL データベースのテーブルを削除する)、ADFTutorialDataFactory でこの記事のチュートリアルを試すことができます。 
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。
[Azure Data Factory Cmdlet Reference (Azure Data Factory コマンドレット リファレンス)][cmdlet-reference] | このリファレンスには、すべての Data Factory コマンドレットに関する詳細が記載されています。 

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfintroduction]: ../data-factory-introduction
[useonpremisesdatasources]: ../data-factory-use-onpremises-datasources
[usepigandhive]: ../data-factory-pig-hive-activities

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/ja-jp/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png

<!--HONumber=46--> 
