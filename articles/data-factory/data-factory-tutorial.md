<properties 
	pageTitle="Azure Data Factory を使用してログ ファイルの移動と処理を行う (Azure クラシック ポータル)" 
	description="この高度なチュートリアルでは、現実に近いシナリオについて説明し、そのシナリオを Azure クラシック ポータルで Azure Data Factory サービスと Data Factory Editor を使用して実装します。" 
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
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# チュートリアル: マーケティング キャンペーンの有効性の測定  
Contoso は、ゲーム機、携帯デバイス、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。これらのゲームはそれぞれが大量のログを産み出します。Contoso 社の目標は、これらのゲームが産み出すログを収集解析することで、顧客の好み、人口統計データ、使用形態などの有益な情報を手に入れ、アップセルやクロスセルの機会を見極め、新しい魅力的な機能を開発してビジネスの成長を促進し、顧客により良い体験を提供することです。

このチュートリアルでは、Data Factory パイプラインを作成して、サンプル ログの収集、参照データによるログの処理と強化、データの変換により、Contoso 社が最近立ち上げたマーケティング キャンペーンの有効性を評価します。次の 3 つのパイプラインがあります。

1.	**PartitionGameLogsPipeline** は、未処理のゲーム イベントを BLOB ストレージから読み取り、年、月、日に基づくパーティションを作成します。
2.	**EnrichGameLogsPipeline** は、パーティション分割されたゲーム イベントを geo コードの参照データと結合し、IP アドレスを対応する地理的場所にマップすることによってデータを強化します。
3.	**AnalyzeMarketingCampaignPipeline** パイプラインは、強化されたデータを利用し、そのデータを広告データと共に処理して、マーケティング キャンペーンの有効性を含む最終的な出力を作成します。

## チュートリアルの準備をする
1.	「[Azure Data Factory の概要][adfintroduction]」を読んで Azure Data Factory の概要を理解し、全体的な概念を把握します。
2.	このチュートリアルを実施するには Azure サブスクリプションが必要です。サブスクリプションの入手方法の詳細については、[購入オプション](http://azure.microsoft.com/pricing/purchase-options/)、[メンバー プラン](http://azure.microsoft.com/pricing/member-offers/)、または[無料試用版](http://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
3.	[Azure PowerShell][download-azure-powershell] をダウンロードしてコンピューターにインストールする必要があります。Data Factory コマンドレットを実行し、サンプル データと pig/hive スクリプトを BLOB ストレージにアップロードします。 
2.	**(推奨)** ポータルとコマンドレットに慣れるための簡単なチュートリアルについての記事「[Azure Data Factory を使ってみる][adfgetstarted]」にあるチュートリアルを確認し、実際に行ってみます。
3.	**(推奨)** パイプラインを作成してオンプレミスのデータ ソースから Azure BLOB ストアにデータを移動するチュートリアルについての記事「[Azure Data Factory で Pig と Hive を使用する][usepigandhive]」にあるチュートリアルを確認し、実際に行ってみます。
4.	[ADFWalkthrough][adfwalkthrough-download] ファイルを **C:\\ADFWalkthrough** フォルダーにダウンロードしますが、**以下のフォルダー構造は維持してください**。
	- **Pipelines:** パイプラインの定義を含む JSON ファイルが含まれています。
	- **Tables:** テーブルの定義を含む JSON ファイルが含まれています。
	- **LinkedServices:** ストレージおよびコンピューティング (HDInsight) クラスターの定義を含む JSON ファイルが含まれています。 
	- **Scripts:** データの処理に使用され、パイプラインから呼び出される Hive および Pig スクリプトが含まれています。
	- **SampleData:** このチュートリアル用のサンプル データが含まれています。
	- **OnPremises:** オンプレミス データへのアクセス方法を示すために使用する JSON ファイルとスクリプトが含まれています。
	- **uploadSampleDataAndScripts.ps1:** このスクリプトは、サンプル データとスクリプトを Azure にアップロードします。
5. 以下の Azure リソースが作成済みであることを確認してください。			
	- Azure ストレージ アカウント
	- Azure SQL Database
	- Azure HDInsight クラスター Version 3.1 以上 (または、Data Factory サービスで自動的に作成されるオンデマンド HDInsight クラスターを使用します)	
7. Azure リソースを作成したなら、上記の各リソースへの接続に必要な情報を持っていることを確認します。
 	- **Azure ストレージ アカウント** - アカウント名とアカウント キー。  
	- **Azure SQL Database** - サーバー、データベース、ユーザー名、パスワード。
	- **Azure HDInsight クラスター** - HDInsight クラスター名、ユーザー名、パスワード、このクラスターに関連付けられている Azure ストレージのアカウント名とアカウント キー。独自の HDInsight クラスターではなく、オンデマンド HDInsight クラスターを使用する場合は、この手順を省略できます。  
8. **Azure PowerShell** を起動して次のコマンドを実行します。Azure PowerShell は開いたままにしておきます。Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
	- **Add-AzureAccount** を実行し、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。  
	- **Get-AzureSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
	- **Select-AzureSubscription** を実行して、使用するサブスクリプションを選択します。このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。	

## 概要
全体のワークフローは以下のとおりです。

![チュートリアルの全体のフロー][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline** が未処理のゲーム イベントを BLOB ストレージ (RawGameEventsTable) から読み取り、年、月、日に基づくパーティションを作成します (PartitionedGameEventsTable)。
2. **EnrichGameLogsPipeline** がパーティション分割されたゲーム イベント (PartitionGameLogsPipeline の出力結果である PartitionedGameEventsTable) と geo コード (RefGetoCodeDictionaryTable) を結合し、IP アドレスを対応する地理的位置 (EnrichedGameEventsTable) にマッピングすることでデータを強化します。
3. **AnalyzeMarketingCampaignPipeline** パイプラインが強化されたデータ (EnrichGameLogsPipeline によって生成された EnrichedGameEventTable) を活用し、これを広告データ (RefMarketingCampaignnTable) と共に処理してマーケティング キャンペーンの有効性という最終的な出力を作成します。これは解析のために Azure SQL Database (MarketingCampainEffectivensessSQLTable) と Azure BLOB ストレージ (MarketingCampaignEffectivenessBlobTable) にコピーされます。
    
## チュートリアル: ワークフローの作成、デプロイ、監視
1. [手順 1. サンプル データとスクリプトをアップロードする](#MainStep1)。この手順では、すべてのサンプル データ (すべてのログと参照データを含む) とワークフローによって実行される Hive/Pig スクリプトをアップロードします。実行するスクリプトは、Azure SQL Database (MarketingCampaigns)、テーブル、ユーザー定義型、およびストアド プロシージャの作成も行います。
2. [手順 2. Azure Data Factory を作成する](#MainStep2)。この手順では、"LogProcessingFactory" という名前の Azure データ ファクトリを作成します。
3. [手順 3. リンクされたサービスを作成する](#MainStep3)。この手順では、以下のリンクされたサービスを作成します: 
	
	- 	**StorageLinkedService**。未処理のゲーム イベント、パーティションに分割されたゲーム イベント、強化されたゲーム イベント、マーケティング キャンペーンの有効な情報、geo コードの参照データを含む Azure ストレージの場所をリンクし、LogProcessingFactory へのマーケティング データの参照を行います。   
	- 	**AzureSqlLinkedService**。マーケティング キャンペーンの有効性の情報を含む Azure SQL データベースをリンクします。 
	- 	**HDInsightStorageLinkedService**。HDInsightLinkedService が参照する HDInsight クラスターに関連付けられている Azure BLOB ストレージをリンクします。 
	- 	**HDInsightLinkedService**。Azure HDInsight クラスターを LogProcessingFactory にリンクします。このクラスターはデータの pig/hive 処理に使用されます。 
 		
4. [手順 4: テーブルを作成する](#MainStep4)。この手順では、以下のテーブルを作成します。
	
	- **RawGameEventsTable**。このテーブルは、未処理のゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/rawgameevents/)。 
	- **PartitionedGameEventsTable**。このテーブルは、パーティションに分割されたゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/partitionedgameevents/)。 
	- **RefGeoCodeDictionaryTable**。このテーブルは、地理的参照コードの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/refdata/refgeocodedictionary/)。
	- **RefMarketingCampaignTable**。このテーブルは、マーケティング キャンペーンの参照データの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/refdata/refmarketingcampaign/)。
	- **EnrichedGameEventsTable**。このテーブルは、強化されたゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/enrichedgameevents/)。
	- **MarketingCampaignEffectivenessSQLTable**。このテーブルは、AzureSqlLinkedService によって定義された、マーケティング キャンペーンの有効性データを格納する Azure SQL Database 内の SQL テーブル (MarketingCampaignEffectiveness) を指定します。 
	- **MarketingCampaignEffectivenessBlobTable**。このテーブルは、マーケティング キャンペーンの有効性データの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/marketingcampaigneffectiveness/)。 

	
5. [手順 5. パイプラインを作成してスケジュールを設定する](#MainStep5)。この手順では、以下のパイプラインを作成します。
	- **PartitionGameLogsPipeline**。このパイプラインは、BLOB ストレージ (RawGameEventsTable) から未処理のゲーム イベントを読み取り、年、月、日に基づくパーティション (PartitionedGameEventsTable) を作成します。 


		![PartitionGamesLogsPipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**。このパイプラインは、パーティションに分割されたゲーム イベント (PartitionGameLogsPipeline の出力結果である PartitionedGameEvents table) と geo コード (RefGetoCodeDictionaryTable) を結合し、IPアドレスを対応する地理的な位置 (EnrichedGameEventsTable) にマッピングすることでデータを強化します。

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**。このパイプラインは、強化されたゲーム イベントのデータ (EnrichGameLogsPipeline によって生成される EnrichedGameEventTable) を活用し、それを広告データ (RefMarketingCampaignnTable) と共に処理してマーケティング キャンペーンの有効性という最終的なアウトプットを作成しますが、これは解析のために Azure SQL database (MarketingCampainEffectivensessSQLTable) と Azure BLOB ストレージ (MarketingCampaignEffectivenessBlobTable) へコピーされます。


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [手順 6. パイプラインとデータ スライスを監視する](#MainStep6)。この手順では、Azure クラシック ポータルを使用して、パイプライン、テーブル、データ スライスを監視します。

## <a name="MainStep1"></a> 手順 1. サンプル データとスクリプトをアップロードする
この手順では、すべてのサンプル データ (すべてのログと参照データを含む) とワークフローによって呼び出される Hive/Pig スクリプトをアップロードします。実行するスクリプトは、"**MarketingCampaigns**" という名前の Azure SQL Database、テーブル、ユーザー定義型、およびストアド プロシージャの作成も行います。

テーブル、ユーザー定義型、およびストアド プロシージャは、マーケティング キャンペーン有効性の結果を Azure BLOB ストレージから Azure SQL データベースに移動する際に使用されます。

1. お好みのエディターで **C:\\ADFWalkthrough** フォルダー (または展開したファイルを含むフォルダー) から **uploadSampleDataAndScripts.ps1** を開き、強調表示されている部分を自分のクラスター情報に置き換え、ファイルを保存します。


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	このスクリプトを使用するには、コンピューターに sqlcmd ユーティリティがインストールされている必要があります。SQL Server がインストール済みであれば、これもインストールされています。そうでない場合は、このユーティリティを[ダウンロード][sqlcmd-install]し、インストールしてください。
	
	または、C:\\ADFWalkthrough\\Scripts フォルダーにあるファイルを使用して pig/hive スクリプトおよびサンプル ファイルを BLOB ストレージ内の adfwalkthrough コンテナーにアップロードし、Azure SQL データベースである MarketingCamapaigns 内に MarketingCampaignEffectiveness テーブルを作成することも可能です。
   
2. ローカル コンピューターに Azure SQL Database へのアクセス権があることを確認してください。アクセスを有効にするには、[Azure クラシック ポータル](http://manage.windowsazure.com)、またはマスター データベース上の **sp\_set\_firewall\_rule** を使用して、コンピューターの IP アドレスに対するファイアウォール規則を作成します。この変更が有効になるまで最大で 5 分かかる場合があります。[Azure SQL のファイアウォール規則の設定][azure-sql-firewall]に関するページを参照してください。
4. Azure PowerShell で、サンプルを展開した場所に移動します (例: **C:\\ADFWalkthrough**)。
5. **uploadSampleDataAndScripts.ps1** を実行します。 
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
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> 手順 2. Azure Data Factory を作成する
この手順では、"**LogProcessingFactory**" という名前の Azure Data Factory を作成します。

1.	[Azure ポータル][azure-portal]にログインした後、左下隅にある **[新規]** をクリックして、**[作成]** ブレードで **[データ分析]** をクリックし、**[データ分析]** ブレードで **[Data Factory]** をクリックします。 

	![New->DataFactory][image-data-factory-new-datafactory-menu]

5. **[新しいデータ ファクトリ]** ブレードで、**[名前]** フィールドに「**LogProcessingFactory**」と入力します。

	![[データ ファクトリ] ブレード][image-data-factory-tutorial-new-datafactory-blade]

6. "**ADF**" という名前の Azure リソース グループをまだ作成していない場合は、次の手順を実行します。
	1. **[リソース グループ名]** をクリックし、**[新しいリソース グループを作成]** をクリックします。
	
		![[リソース グループ] ブレード][image-data-factory-tutorial-resourcegroup-blade]
	2. **[リソース グループを作成]** ブレードで、リソース グループの名前として「**ADF**」と入力し、**[OK]** をクリックします。
	
		![リソース グループの作成][image-data-factory-tutorial-create-resourcegroup]
7. **[リソース グループ名]** の **[ADF]** を選択します。  
8.	**[新しいデータ ファクトリ]** ブレードで、**[スタート画面に追加]** が既定で選択されていることに注意してください。これにより、スタート画面 (Azure ポータルへのログイン時に表示される画面) のデータ ファクトリにリンクが追加されます。

	![データ ファクトリ作成ブレード][image-data-factory-tutorial-create-datafactory]

9.	**[新しいデータ ファクトリ]** ブレードで、**[作成]** をクリックしてデータ ファクトリを作成します。
10.	データ ファクトリが作成されると、**LogProcessingFactory** というタイトルの **[Data Factory]** ブレードが表示されます。

	![データ ファクトリのホーム ページ][image-data-factory-tutorial-datafactory-homepage]

	
	表示されない場合は、次のいずれかを行います。

	- **スタート画面** (ホーム ページ) で **[LogProcessingFactory]** をクリックします。
	- 左側の **[参照]** をクリックし、**[すべて]**、**[Data Factory]** の順にクリックし、最後に目的のデータ ファクトリをクリックします。
 
	Azure Data Factory の名前はグローバルに一意にする必要があります。**""LogProcessingFactory" という名前の Data Factory は使用できません"** というエラー メッセージが表示された場合は、名前を変更します (例: yournameLogProcessingFactory)。このチュートリアルの手順の実行中に、この名前を LogProcessingFactory の代わりに使用します。
 
## <a name="MainStep3"></a>手順 3. リンクされたサービスを作成する

> [AZURE.NOTE]この記事では、Azure クラシック ポータル (具体的には Data Factory エディター) を使用して、リンクされたサービス、テーブル、パイプラインを作成する方法について説明します。Azure PowerShell を使用してこのチュートリアルを実行する場合は、[Azure PowerShell の使用に関するチュートリアル][adftutorial-using-powershell]を参照してください。

この手順では、以下のリンクされたサービスを作成します:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService。 

### StorageLinkedService と HDInsightStorageLinkedService の作成

1.	**[Data Factory]** ブレードで、**[作成とデプロイ]** タイルをクリックして、Data Factory の**エディター**を起動します。

	![[作成とデプロイ] タイル][image-author-deploy-tile]

	Data Factory エディターの詳細については、トピック「[Data Factory エディター][data-factory-editor]」を参照してください。

2.  **エディター**のツール バーで **[新しいデータ ストア]** ボタンをクリックし、ドロップダウン メニューから **[Azure Storage]** を選択します。Azure Storage のリンクされたサービスを作成するための JSON テンプレートが右側のウィンドウに表示されます。
	
	![エディターの [新しいデータ ストア] ボタン][image-editor-newdatastore-button]

3. **accountname** と **accountkey** を Azure ストレージ アカウントの名前とキーの値に置き換えます。

	![Editor Blob Storage JSON][image-editor-blob-storage-json]
	
	JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](http://go.microsoft.com/fwlink/?LinkId=516971)を参照してください。

4. ツール バーの **[デプロイ]** をクリックして、StorageLinkedService をデプロイします。タイトル バーに **"リンクされたサービスが正常に作成されました"** というメッセージが表示されていることを確認します。

	![エディターの Blob Storage のデプロイ][image-editor-blob-storage-deploy]

5. 手順を繰り返して、HDInsight クラスターに関連付けられているストレージに対して **HDInsightStorageLinkedService** という名前で Azure Storage のリンクされたサービスを作成します。リンクされたサービスの JSON スクリプトで、**name** プロパティの値を **HDInsightStorageLinkedService** に変更します。

### AzureSqlLinkedService の作成
1. **Data Factory エディター**のツール バーで **[新しいデータ ストア]** ボタンをクリックし、ドロップダウン メニューから **[Azure SQL Database]** を選択します。Azure SQL のリンクされたサービスを作成するための JSON テンプレートが右側のウィンドウに表示されます。
2. **servername**、****username@servername**、**password** を、Azure SQL のサーバー名、ユーザー アカウント、パスワードに置き換えます。
3. **databasename** を **MarketingCampaigns** に置き換えます。これは、手順 1. で実行したスクリプトによって作成された Azure SQL Database です。このデータベースがスクリプトによって実際に作成されていることを確認する必要があります (エラーが発生した場合)。 
3. ツール バーの **[デプロイ]** をクリックして、AzureSqlLinkedService を作成してデプロイします。

### HDInsightLinkedService の作成
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。試しに、オンデマンド クラスターを使用してみましょう。

#### オンデマンド HDInsight クラスターを使用するには
1. コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[オンデマンド HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。 
	1. **clusterSize** プロパティには、HDInsight クラスターのサイズを指定します。
	3. **timeToLive** プロパティには、顧客が削除されるまでの間にアイドル状態でいられる時間を指定します。 
	4. **version** プロパティには、使用する HDInsight のバージョンを指定します。このプロパティを除外した場合は、最新バージョンが使用されます。  
	5. **linkedServiceName** には、入門チュートリアルで作成した **HDInsightStorageLinkedService** を指定します。 

			{
			    "name": "HDInsightOnDemandLinkedService",
				    "properties": {
			        "type": "HDInsightOnDemand",
			        "description": "",
			        "typeProperties": {
		    	    "clusterSize": "4",
			            "timeToLive": "00:30:00",
		    	    "version": "3.2",
			            "linkedServiceName": "StorageLinkedService"
			        }
		    	}
			}

		リンクされたサービスの **type** は **HDInsightOnDemand** に設定されます。

2. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。
   
   
#### 独自の HDInsight クラスターを使用するには 

1. コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。 
	1. **clusterUri** プロパティには、HDInsight の URL を入力します。たとえば、https://<clustername>.azurehdinsight.net/ などです。     
	2. **UserName** プロパティには、HDInsight クラスターにアクセスできるユーザー名を入力します。
	3. **Password** プロパティには、ユーザーのパスワードを入力します。 
	4. **LinkedServiceName** プロパティには、「**StorageLinkedService**」と入力します。これは、入門チュートリアルで作成したリンク サービスです。 

	リンクされたサービスの **type** は **HDInsightBYOCLinkedService** (BYOC - Bring Your Own Cluster: クラスターを自分で用意する) に設定されます。

2. コマンド バーの **[デプロイ]** をクリックして、リンクされたサービスをデプロイします。


## <a name="MainStep4"></a>手順 4. テーブルを作成する
 
この手順では、以下の Data Factory テーブルを作成します。

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![チュートリアルの全体のフロー][image-data-factory-tutorial-end-to-end-flow]
 
上の図は、中央の列がパイプラインを、上と下の列がテーブルを表しています。

### テーブルを作成するには
	
1. Data Factory **エディター**のツール バーで **[新しいデータセット]** ボタンをクリックし、ドロップダウン メニューから **[Azure BLOB ストレージ]** をクリックします。 
2. 右側のウィンドウにある JSON を、**C:\\ADFWalkthrough\\Tables** フォルダーにある **RawGameEventsTable.json** の JSON スクリプトに置き換えます。
3. ツール バーの **[デプロイ]** をクリックし、テーブルを作成してデプロイします。エディターのタイトル バーに "**テーブルが正常に作成されました**" というメッセージが表示されていることを確認します。
4. 次のファイルの内容で手順 1. ～ 3. を繰り返します。 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. 次のファイルの内容で手順 1. ～ 3. を繰り返します。ただし、**[新しいデータセット]** をクリックした後は、**[Azure Sql]** を選択します。
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a>手順 5. パイプラインを作成してスケジュールを設定する
この手順では、以下のパイプラインを作成します。

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### パイプラインを作成するには

1. **Data Factory エディター**で、ツール バーの **[新しいパイプライン]** をクリックします。ボタンが表示されない場合は、ツール バーの **[...] \(省略記号)** をクリックします。または、ツリー ビューの **[パイプライン]** を右クリックして、**[新しいパイプライン]** をクリックする方法もあります。
2. 右側のウィンドウにある JSON を、**C:\\ADFWalkthrough\\Pipelines** フォルダーにある **PartitionGameLogsPipeline.json** の JSON スクリプトに置き換えます。
3. JSON の**閉じ角かっこ (']')** の最後に**コンマ (',')** を追加してから、その閉じ角かっこの後に次の 3 行を追加します。 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	このチュートリアルのサンプル データが 2014 年 5 月 1 日から 2014 年 5 月 5 日であるため、開始時刻と終了時刻が 2014 年 5 月 1 日および 2014 年 5 月 5 日に設定されていることに注意してください。
 
	オンデマンド HDInsight のリンクされたサービスを使用する場合は、**linkedServiceName** プロパティを **HDInsightOnDemandLinkedService** に設定します。
3. ツール バーの **[デプロイ]** クリックして、パイプラインを作成してデプロイします。エディターのタイトル バーに "**パイプラインが正常に作成されました**" というメッセージが表示されていることを確認します。
4. 次のファイルの内容で手順 1. ～ 3. を繰り返します。 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. **[X]** (右上隅) をクリックして Data Factory のブレードを閉じ、Data Factory のホーム ページ (**[DATA FACTORY]** ブレード) を表示します。

### Diagram view

1. **[LogProcessingFactory]** の **[Data Factory]** ブレードで、**[ダイアグラム]** をクリックします。 

	![[ダイアグラム] リンク][image-data-factory-tutorial-diagram-link]

2. 表示されているダイアグラムは並べ替えることが可能で、以下のダイアグラムは上部が直接の入力を示し、下部が出力を示しています。**PartitionGameLogsPipeline** の出力が EnrichGameLogsPipeline に入力として渡され、**EnrichGameLogsPipeline** の出力が **AnalyzeMarketingCampaignPipeline** に渡されていることがわかります。タイトルをダブルクリックして、ブレードが示すアーティファクトについての詳細を表示します。

	![[ダイアグラム] ビュー][image-data-factory-tutorial-diagram-view]

3. **[AnalyzeMarketingCampaignPipeline]** を右クリックし、**[パイプラインを開く]** をクリックします。アクティビティの入力呼び出し力のデータセットと、パイプライン内のすべてのアクティビティが表示されます。
 
	![パイプラインを開く](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	左上にある階層リンクで **[Data Factory]** をクリックすると、すべてのパイプラインでダイアグラム ビューに戻ります。


**お疲れさまでした。** Azure データ ファクトリ、リンクされたサービス、パイプライン、テーブルを作成し、ワークフローを開始することに成功しました。


## <a name="MainStep6"></a>手順 6. パイプラインとデータ スライスを監視する 

1.	**[LogProcessingFactory]** の **[Data Factory]** ブレードを開いていない場合、以下のいずれかを実行できます。
	1.	**スタート画面**で **[LogProcessingFactory]** をクリックします。データ ファクトリの作成中に、**[スタート画面に追加]** オプションが自動でオンになっています。

		![監視スタート画面][image-data-factory-monitoring-startboard]

	 	

	2. **[参照]** をクリックし、**[参照]** ブレードで **[Data Factory]** を選択し、**[Data Factory]** ブレードで **[LogProcessingFactory]** を選択します。

		![データ ファクトリの監視][image-data-factory-monitoring-browse-datafactories]
2. いくつかの方法でデータ ファクトリが監視できます。パイプラインまたはデータ セットで監視が始められます。パイプラインで監視を始め、さらに詳しく学びましょう。 
3.	**[Data Factory]** ブレードの **[パイプライン]** をクリックします。 
4.	[パイプライン] ブレードで **[PartitionGameLogsPipeline]** をクリックします。 
5.	**[PartitionGameLogsPipeline]** の **[パイプライン]** ブレードを見ると、パイプラインが **[RawGameEventsTable]** データセットを使用していることがわかります。**[RawGameEventsTable]** をクリックします。

	![パイプラインでの使用と生成][image-data-factory-monitoring-pipeline-consumed-produced]

6. **[RawGameEventsTable]** の [テーブル] ブレードに、すべてのスライスが表示されています。以下のスクリーン ショットでは、すべてのスライスが **[準備完了]** の状態で、問題のあるスライスはありません。これは、そのデータがすぐに処理できることを意味します。

	![[RawGameEventsTable] の [テーブル] ブレード][image-data-factory-monitoring-raw-game-events-table]

	**[最近更新したスライス]** と **[最近失敗したスライス]** の一覧は、どちらも **[最終更新時刻]** で並べ替えられます。次の状況では、スライスの更新時刻が変更されます。

	-  **Set-AzureRmDataFactorySliceStatus** を使用したり、スライスの **[スライス]** ブレードで **[実行]** をクリックしたりすることで、スライスの状態を手動で更新した場合。
	-  スライスの実行 (実行の開始、実行の終了と失敗、実行の終了と成功など) により、スライスの状態が変わります。
 
	一覧のタイトルをクリックするか、**[...] \(省略記号)** をクリックすると、さらに多くのスライスが一覧表示されます。スライスをフィルター処理するには、ツール バーの **[フィルター]** をクリックします。
	
	代わりに、スライスの開始時刻と終了時刻で並べ替えられたデータ スライスを表示するには、**[データ スライス (スライスの時刻別)]** タイルをクリックします。

	![データ スライス (スライスの時刻別)][DataSlicesBySliceTime]
 
7. **[PartiionGameLogsPipeline]** の **[パイプライン]** ブレードで、**[生成済み]** をクリックします。
8. このパイプラインが生成するデータ セットの一覧が表示されるはずです。 
9. **[生成済みデータセット]** ブレードの **[PartitionedGameEvents]** テーブルをクリックします。 
10.	すべてのスライスの **[状態]** が **[準備完了]** になっていることを確認します。 
11.	**[準備完了]** になっているスライスの 1 つをクリックし、そのスライスの **[データ スライス]** ブレードを表示します。

	![[RawGameEventsTable] の [データ スライス] ブレード][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	エラーが発生していた場合、ここに **[失敗]** という状態が表示されます。また、スライスがどの程度の速さで処理されるかによって、両方のスライスが **[準備完了]** 状態で表示される場合もあれば、**[検証を保留中]** 状態で表示される場合もあります。

	スライスが **[準備完了]** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、**[準備完了でない上位スライス]** の一覧に表示されます。
 
	[Data Factory の開発者用リファレンス][developer-reference]を参照し、考えられるすべてのスライスの状態を把握してください。

12.	**[データ スライス]** ブレードで、**[アクティビティの実行]** の一覧から [実行] をクリックします。そのスライスの [アクティビティの実行] ブレードが表示されるはずです。以下の **[アクティビティの実行の詳細]** ブレードが表示されます。

	![[アクティビティの実行の詳細] ブレード][image-data-factory-monitoring-activity-run-details]

13.	**[ダウンロード]** をクリックしてファイルをダウンロードします。この画面は、HDInsight の処理で発生するエラーのトラブルシューティングを行うときに、特に役立ちます。
	 
	
すべてのパイプラインが実行を完了すると、Azure SQL Database である **MarketingCampaigns** 内の **MarketingCampaignEffectivenessTable** を調べて実行結果を確認できます。

**お疲れさまでした。** これでワークフローの監視とトラブルシューティングができます。Azure Data Factory を使用してデータを処理し分析結果を得る方法を学びました。

## チュートリアルをさらに進めてオンプレミス データを使用する
この記事のチュートリアルにあるログ処理のシナリオの最後の手順で、マーケティング キャンペーンの有効性の出力が Azure SQL Database にコピーされました。分析のため、所属する組織内にあるオンプレミスの SQL Server にこのデータを移動することも可能です。
 
マーケティング キャンペーンの有効性データを Azure BLOB からオンプレミスの SQL Server にコピーするには、この記事のチュートリアルで導入したオンプレミスのリンクされたサービス、テーブル、パイプラインを追加で作成する必要があります。

[オンプレミス データ ソースの使用に関するチュートリアル][tutorial-onpremises]を実践して、マーケティング キャンペーンの有効性データをオンプレミスの SQL Server データベースにコピーするためのパイプラインを作成する方法を習得します。


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

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

<!----HONumber=AcomDC_1210_2015-->