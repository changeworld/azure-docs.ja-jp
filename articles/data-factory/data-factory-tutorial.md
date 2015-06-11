<properties 
	pageTitle="Azure Data Factory を使用してログ ファイルの移動と処理を行う" 
	description="この高度なチュートリアルは、ほぼの実際のシナリオについて説明し、Azure のデータのファクトリのサービスとデータの工場出荷時のエディターを使用してシナリオを実装します。" 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# チュートリアル: マーケティング キャンペーンの効果を測定します。  
Contoso は、ゲーム機、携帯機器、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。これらのゲームは、多くのログを生成し、Contoso の目標は、顧客の基本設定、人口統計、動作アップセルとクロスセルの機会を識別する、ビジネスの発展の新しい強力な機能を開発、およびユーザーより優れたエクスペリエンスを提供するなどの使用状況に関する情報を入手のこれらのログを収集および分析します。

このチュートリアルでは、Contoso がサンプルのログの収集、処理とそれらに参照データを拡充およびによって、データの変換を開始しましたが、マーケティング キャンペーンの効果を評価するデータのファクトリのパイプラインを作成します。次の 3 つのパイプラインを持ちます。

1.	 **PartitionGameLogsPipeline** blob ストレージからのゲームの実際のイベントの読み取りを年、月、および日に基づいてパーティションを作成します。
2.	 **EnrichGameLogsPipeline** と地理コードの参照データのパーティション分割されたゲーム イベントを結合し、IP アドレスを対応する地理的場所にマップすることによって、データを拡充します。
3.	 **AnalyzeMarketingCampaignPipeline** パイプラインは、強化されたデータを利用し、それをマーケティング キャンペーンの有効性を含む最終的な出力を作成するには、広告データによって処理されます。

## チュートリアルの準備をする
1.	読み取り [Azure のデータのファクトリの概要][adfintroduction] を Azure のデータのファクトリの概要と、最上位レベルの概念の理解を取得します。
2.	このチュートリアルを実施するには Azure サブスクリプションが必要です。サブスクリプションの入手方法の詳細については、[購入オプション](http://azure.microsoft.com/pricing/purchase-options/)、[メンバー プラン](http://azure.microsoft.com/pricing/member-offers/)、または[無料評価版](http://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
3.	ダウンロードしてインストールする必要があります [Azure PowerShell][download-azure-powershell] コンピューターにします。サンプル データのアップロードと pig と hive スクリプトを blob ストレージにデータのファクトリのコマンドレットが実行されます。 
2.	**(推奨)** レビューおよびチュートリアルでの演習、 [Azure のデータのファクトリを使い始める][adfgetstarted] ポータルとコマンドレットについての知識を取得する簡単なチュートリアルについては、資料です。
3.	**(推奨)** レビューおよびチュートリアルでは、演習、 [の Pig の使用と Azure のデータのファクトリでの Hive][usepigandhive] 、Azure の blob ストアへの内部設置型のデータ ソースからデータを移動するためのパイプラインを作成する手順については記事です。
4.	ダウンロード [ADFWalkthrough][adfwalkthrough-download] ファイルを **C:\ADFWalkthrough** フォルダー **フォルダー構造を保持する**:
	- **パイプライン:** 、パイプラインの定義を含む JSON ファイルが含まれます。
	- **テーブル:** テーブルの定義を含む JSON ファイルが含まれます。
	- **LinkedServices:** 、記憶域とコンピューティング (HDInsight) の定義を含む JSON ファイルが含まれているクラスター 
	- **スクリプト:** のデータの処理に使用され、パイプラインから呼び出される Hive と Pig のスクリプトが含まれています
	- **SampleData:** このチュートリアル用のサンプル データが含まれています。
	- **設置:** 、内部設置型データへのアクセスを示すために使用される JSON ファイルとスクリプトが含まれています
	- **uploadSampleDataAndScripts.ps1:** このスクリプトを Azure にサンプル データ (&) スクリプトをアップロードします。
5. 以下の Azure リソースが作成済みであることを確認してください。			
	- Azure ストレージ アカウント
	- Azure SQL データベース
	- Azure の HDInsight クラスター version 3.1 以降 (または、オンデマンドで使用する HDInsight クラスターをデータのファクトリのサービスが自動的に作成されます)	
7. Azure リソースを作成したなら、上記の各リソースへの接続に必要な情報を持っていることを確認します。
 	- **Azure ストレージ アカウント** - アカウント名とアカウント キー。  
	- **Azure SQL データベース** -サーバー、データベース、ユーザー名、およびパスワード。
	- **Azure の HDInsight クラスター**.-HDInsight クラスターの名前、ユーザー名、パスワード、およびアカウント名、およびこのクラスターに関連付けられている Azure のストレージのアカウント キー。HDInsight クラスターではなく、オンデマンドでの HDInsight クラスターを使用する場合は、この手順を省略します。  
8. 起動 **Azure PowerShell** と、次のコマンドを実行します。Azure PowerShell を開いたままにします。終了して再起動した場合は、これらのコマンドを再度実行する必要があります。
	- 実行 **Add-azureaccount** 、ユーザー名と、Azure プレビュー ポータルにサインインに使用するパスワードを入力します。  
	- 実行 **Get-azuresubscription** をこのアカウントのすべてのサブスクリプションを表示します。
	- 実行 **Select-azuresubscription** を使用するサブスクリプションを選択します。このサブスクリプションは、Azure プレビュー ポータルで使用したものと同じである必要があります。	

## 概要
全体のワークフローは以下のとおりです。

![チュートリアルのエンド ツー エンド フロー][image-data-factory-tutorial-end-to-end-flow]

1.  **PartitionGameLogsPipeline** 、blob ストレージ (RawGameEventsTable) から、生のゲームのイベントを読み取り、および年、月、および日 (PartitionedGameEventsTable) に基づくパーティションを作成します。
2.  **EnrichGameLogsPipeline** 地理コード (RefGetoCodeDictionaryTable) とパーティション分割されたゲーム イベント (PartitionedGameEvents テーブルでは、PartitionGameLogsPipeline の出力) を結合および IP アドレスを対応する地理的な場所 (EnrichedGameEventsTable) にマップすることによって、データを拡充します。
3.  **AnalyzeMarketingCampaignPipeline** パイプラインは、強化されたデータ (EnrichedGameEventTable、EnrichGameLogsPipeline によって生成される) を利用し、それを Azure SQL データベース (MarketingCampainEffectivensessSQLTable) と、分析用に Azure blob ストレージ (MarketingCampaignEffectivenessBlobTable) にコピーされる広告データ マーケティング キャンペーンの有効性の最終的な出力を作成するには、(RefMarketingCampaignnTable) によって処理されます。
    
## チュートリアル: の作成、展開、およびワークフローの監視
1. [手順 1: サンプル データとスクリプトをアップロード](#MainStep1)です。この手順では、すべてのサンプル データ (すべてのログと参照データを含む) とワークフローによって実行される Hive/Pig スクリプトをアップロードします。実行するスクリプトは、Azure SQL Database (MarketingCampaigns)、テーブル、ユーザー定義型、およびストアド プロシージャの作成も行います。
2. [手順 2: Azure のデータのファクトリを作成する](#MainStep2)です。この手順では、"LogProcessingFactory" という名前の Azure データ ファクトリを作成します。
3. [手順 3: 関連付けられているサービスを作成する](#MainStep3)です。この手順では、以下のリンクされたサービスを作成します: 
	
	- 	**StorageLinkedService**です。未処理のゲーム イベント、パーティションに分割されたゲーム イベント、強化されたゲーム イベント、マーケティング キャンペーンの有効な情報を含む Azure ストレージの場所をリンクし、LogProcessingFactory へのマーケティング データの参照を行います。   
	- 	**AzureSqlLinkedService**です。マーケティング キャンペーンの有効性の情報を含む Azure SQL データベースをリンクします。 
	- 	**HDInsightStorageLinkedService**です。HDInsightLinkedService が参照する HDInsight クラスターに関連付けられている Azure BLOB ストレージをリンクします。 
	- 	**HDInsightLinkedService**です。Azure HDInsight クラスターを LogProcessingFactory にリンクします。このクラスターはデータの pig/hive 処理に使用されます。 
 		
4. [手順 4: テーブルを作成する](#MainStep4)です。この手順では、以下のテーブルを作成します。
	
	- **RawGameEventsTable**です。このテーブルは、未処理のゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/rawgameevents/)。 
	- **PartitionedGameEventsTable**です。このテーブルは、パーティションに分割されたゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/partitionedgameevents/)。 
	- **RefGeoCodeDictionaryTable**です。このテーブルは、地理的参照コードの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/refdata/refgeocodedictionary/)。
	- **RefMarketingCampaignTable**です。このテーブルは、マーケティング キャンペーンの参照データの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/refdata/refmarketingcampaign/)。
	- **EnrichedGameEventsTable**です。このテーブルは、強化されたゲーム イベントのデータの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/logs/enrichedgameevents/)。
	- **MarketingCampaignEffectivenessSQLTable**です。次の表は、マーケティング キャンペーンの有効性のデータを含む AzureSqlLinkedService で定義されている、Azure SQL データベースで、SQL テーブル (MarketingCampaignEffectiveness) を指定します。 
	- **MarketingCampaignEffectivenessBlobTable**です。このテーブルは、マーケティング キャンペーンの有効性データの場所を、StorageLinkedService によって定義された Azure BLOB ストレージ内に指定します (adfwalkthrough/marketingcampaigneffectiveness/)。 

	
5. [手順 5: を作成およびスケジュール パイプライン](#MainStep5)です。この手順では、以下のパイプラインを作成します。
	- **PartitionGameLogsPipeline**です。このパイプラインは、BLOB ストレージ (RawGameEventsTable) から未処理のゲーム イベントを読み取り、年、月、日に基づくパーティション (PartitionedGameEventsTable) を作成します。 


		![PartitionGamesLogs パイプライン][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**です。このパイプラインは、パーティションに分割されたゲーム イベント (PartitionGameLogsPipeline の出力結果である PartitionedGameEvents table) と地理的コード (RefGetoCodeDictionaryTable) を結合し、IPアドレスを対応する地理的な位置 (EnrichedGameEventsTable) にマッピングすることでデータを強化します。

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**です。このパイプラインは、強化されたゲーム イベントのデータ (EnrichGameLogsPipeline によって生成される EnrichedGameEventTable) を活用し、それを広告データ (RefMarketingCampaignnTable) と共に処理してマーケティング キャンペーンの有効性という最終的なアウトプットを作成しますが、これは解析のために Azure SQL database (MarketingCampainEffectivensessSQLTable) と Azure BLOB ストレージ (MarketingCampaignEffectivenessBlobTable) へコピーされます。


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [手順 6: パイプラインとデータのスライスを監視する](#MainStep6)です。この手順では、Azure ポータルを使用して、パイプライン、テーブル、およびデータ スライスを監視します。

## <a name="MainStep1"></a> 手順 1: サンプル データとスクリプトをアップロードします。
この手順では、すべてのサンプル データ (すべてのログと参照データを含む) とワークフローによって呼び出される Hive/Pig スクリプトをアップロードします。また実行するスクリプトと呼ばれる、Azure SQL データベースを作成する **MarketingCampaigns**, 、テーブルにすると、ユーザー定義型、およびストアド プロシージャです。

テーブル、ユーザー定義型、およびストアド プロシージャは、マーケティング キャンペーン有効性の結果を Azure BLOB ストレージから Azure SQL データベースに移動する際に使用されます。

1. 開いている **uploadSampleDataAndScripts.ps1** から **C:\ADFWalkthrough** フォルダー (または、抽出したファイルが含まれているフォルダー) でお気に入りのエディターを置き換え、強調表示されている、クラスターの情報を含むファイルを保存、します。


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][ダウンロード][sqlcmd-install]   
2. ローカル コンピューターに Azure SQL Database へのアクセス権があることを確認してください。アクセスを有効にするには、 **Azure 管理ポータル** または **sp_set_firewall_rule** をコンピューターの IP アドレス用のファイアウォール ルールを作成するのには、master データベースにします。この変更が有効になるまで最大で 5 分かかる場合があります。参照してください [Azure SQL のファイアウォールの規則を設定する][azure-sql-firewall]です。
4. Azure PowerShell でのサンプルは、展開した場所に移動します (例: **C:\ADFWalkthrough**)
5. 実行 **uploadSampleDataAndScripts.ps1** 
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

## <a name="MainStep2"></a> 手順 2: Azure のデータのファクトリを作成します。
このステップで、Azure のデータのファクトリという名前を作成する **LogProcessingFactory**です。

1.	ログインした後、 [Azure プレビュー ポータル][azure-preview-portal], 、] をクリックして **新規** 、左下隅にあるから次のようにクリックします。 **データ分析** で、 **作成** ] をクリックしてブレード、 **データ ファクトリ** で、 **データ分析** ブレードします。 

	![新しい DataFactory -> します。][image-data-factory-new-datafactory-menu]

5.  **データの新しいファクトリ** ブレードで入力 **LogProcessingFactory** の **名前**です。

	![データの工場出荷時のブレード][image-data-factory-tutorial-new-datafactory-blade]

6. という名前の Azure リソース グループを作成していない場合は、 **ADF** 既にを次の操作します。
	1. クリックして **リソース グループ名**, 、] をクリック **新しいリソース グループの作成**です。
	
		![リソース グループのブレード][image-data-factory-tutorial-resourcegroup-blade]
	2.  **リソース グループの作成** ブレードで入力 **ADF** ] をクリックして、リソース グループの名前の **[ok]**です。
	
		![リソース グループを作成します。][image-data-factory-tutorial-create-resourcegroup]
7. 選択 **ADF** の **リソース グループ名**です。  
8.	 **データの新しいファクトリ** ブレード、ことに注意して **スタート ボードに追加** が既定で選択します。これにより、スタート画面 (Azure プレビュー ポータルへのログイン時に表示される画面) のデータ ファクトリにリンクが追加されます。

	![データの工場出荷時のブレードを作成します。][image-data-factory-tutorial-create-datafactory]

9.	 **データの新しいファクトリ** ブレードで] をクリックして **作成** 、データのファクトリを作成します。
10.	必要がありますを参照して、データのファクトリの作成後、 **データ ファクトリ** 」というタイトルのブレード **LogProcessingFactory**です。

	![データの工場出荷時のホーム ページ][image-data-factory-tutorial-datafactory-homepage]

	
	表示されない場合は、次のいずれかを行います。

	- クリックして **LogProcessingFactory** 上、 **スタート ボード** ([ホーム] ページ)
	- をクリックして **参照** 、左側では、次のようにクリックします。 **すべて**, 、] をクリック **データ ファクトリ**, 、データのファクトリをクリックします。
 
	> [AZURE.NOTE]Azure Data Factor の名前はグローバルに一意にする必要があります。エラーが発生する場合: **データ工場出荷時の名前"LogProcessingFactory"は使用できません**, 、(たとえば、yournameLogProcessingFactory) の名前を変更します。このチュートリアルの手順を実行中に LogProcessingFactory の代わりに、この名前を使用します。
 
## <a name="MainStep3"></a> 手順 3: 関連付けられているサービスを作成します。

> [AZURE.NOTE]この記事は、関連付けられているサービス、テーブル、およびパイプラインを作成するのに、データのファクトリ エディター具体的には、Azure ポータルを使用します。参照してください [チュートリアル Azure PowerShell を使用して][adftutorial-using-powershell] Azure PowerShell を使用してこのチュートリアルを実行する場合。

この手順では、以下のリンクされたサービスを作成します:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService。 

### StorageLinkedService と HDInsightStorageLinkedService を作成します。

1.	 **データ ファクトリ** ブレードで] をクリックして **作成者と展開** タイルを起動する、 **エディター** データ ファクトリのです。

	![タイルの作成とデプロイ][image-author-deploy-tile]

	> [AZURE.NOTE]参照してください [データ工場出荷時のエディター][data-factory-editor] データ工場出荷時のエディターの詳細の概要についてのトピックです。

2.   **エディター**, 、] をクリックして **データ ストアの新しい** ] ボタンをクリックし、ツールバー **Azure ストレージ** 、ドロップ ダウン メニューからです。右側のウィンドウで、リンクされている Azure のストレージ サービスを作成するためには、JSON テンプレートが表示されます。
	
	![新しいデータ ストアのボタンのエディター][image-editor-newdatastore-button]

3. 置き換える **accountname** と **accountkey** アカウント名とアカウント キーの値を Azure ストレージ アカウントをします。

	![Blob ストレージのエディター JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]参照してください [JSON スクリプト参照](http://go.microsoft.com/fwlink/?LinkId=516971) JSON のプロパティに関する詳細です。

4. クリックして **展開** 、StorageLinkedService を展開するには、ツールバーのです。メッセージが表示されることを確認 **リンク サービス作成が正常に** 、タイトル バーにします。

	![エディターの Blob ストレージを展開します。][image-editor-blob-storage-deploy]

5. リンクされたという名前のサービスを別の Azure ストレージを作成する手順を繰り返します。 **HDInsightStorageLinkedService** 、HDInsight クラスターに関連付けられている記憶域です。値を変更、リンクされているサービスの JSON スクリプトでは、 **名前** プロパティを **HDInsightStorageLinkedService**です。

### AzureSqlLinkedService を作成します。
1.  **データ工場出荷時のエディター** , 、] をクリックして **データ ストアの新しい** ] ボタンをクリックし、ツールバー **Azure SQL データベース** 、ドロップ ダウン メニューからです。右側のウィンドウで、リンクされている Azure の SQL サービスを作成するためには、JSON テンプレートが表示されます。
2. 置き換える **servername**, 、**username@servername**, 、および **パスワード** 、Azure の SQL サーバー、ユーザーのアカウントとパスワードの名前を持つ 。3. 置き換える **databasename** で **MarketingCampaigns**です。これは、手順 1. で実行するスクリプトが作成した Azure SQL データベースです。このデータベースが実際に作成されたこと、スクリプトで (このようなエラーが発生しました) を確認する必要があります。 
3. をクリックして **展開** 、ツールバーを作成し、AzureSqlLinkedService を展開します。

### HDInsightLinkedService を作成します。
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。試しに、オンデマンド クラスターを使用してみましょう。

#### オンデマンド HDInsight クラスターを使用するには
1. クリックして **新しいコンピューティング** クリックし、コマンド バーから **オンデマンドでの HDInsight クラスター** ] メニューからです。
2. JSON スクリプトでは、次のように 
	1.  **ClusterSize** プロパティでは、HDInsight クラスターのサイズを指定します。
	2.  **JobsContainer** プロパティでは、クラスターのログが格納される既定のコンテナーの名前を指定します。このチュートリアルでは、次のように指定します。 **adfjobscontainer**です。
	3.  **TimeToLive** プロパティ、どのくらいの期間、顧客が削除される前にアイドル状態できるを指定します。 
	4.  **バージョン** プロパティを使用する場合、HDInsight のバージョンを指定します。このプロパティを除外する場合は、最新のバージョンが使用します。  
	5.  **LinkedServiceName**, 、指定 **HDInsightStorageLinkedService** ことを Get で作成したチュートリアルを開始します。 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		注意して、 **型** にリンクされているサービスの設定は **HDInsightOnDemandLinkedService**です。

2. クリックして **展開** リンクされているサービスをデプロイするには、コマンド バー。
   
   
#### 独自の HDInsight クラスターを使用するには 

1. クリックして **新しいコンピューティング** クリックし、コマンド バーから **HDInsight クラスター** ] メニューからです。
2. JSON スクリプトでは、次のように 
	1.  **ClusterUri** プロパティで、HDInsight の URL を入力します。例: https://<clustername>.azurehdinsight.net/     
	2.  **UserName** プロパティ、HDInsight クラスターへのアクセス権を持つユーザーの名前を入力します。
	3.  **パスワード** プロパティでは、ユーザーのパスワードを入力します。 
	4.  **LinkedServiceName** プロパティ、入力 **StorageLinkedService**です。これは、入門チュートリアルで作成したリンクのサービスです。 

	Nore を **型** にリンクされているサービスの設定は **HDInsightBYOCLinkedService** (BYOC は独自のクラスターを状態を表します)。

2. クリックして **展開** リンクされているサービスをデプロイするには、コマンド バー。


## <a name="MainStep4"></a> 手順 4: テーブルを作成します。
 
このステップでは、次の表のデータのファクトリを作成します。

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![チュートリアルのエンド ツー エンド フロー][image-data-factory-tutorial-end-to-end-flow]
 
上の図は、中央の列がパイプラインを、上と下の列がテーブルを表しています。

### テーブルを作成するには
	
1.  **エディター** をクリックして、データのファクトリ **新しいデータセット** ] ボタンをクリックして、ツールバーを **Azure Blob ストレージ** 、ドロップ ダウン メニューからです。 
2. 右側のウィンドウからの JSON スクリプトを JSON に置き換えます、 **RawGameEventsTable.json** ファイルを **C:\ADFWalkthrough\Tables** フォルダーです。
3. クリックして **展開** 、ツールバーを作成し、テーブルを展開します。いることを確認、 **正常に作成されたテーブル** 、エディターのタイトル バーにメッセージをします。
4. 次のファイルの内容で手順 1. ～ 3. を繰り返します。 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. 次のファイルの内容で手順 1. ～ 3. を繰り返します。選択が **Azure Sql** をクリックした後 **新しいデータセット**です。
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> 手順 5: を作成して、パイプラインのスケジュールを設定します。
この手順では、以下のパイプラインを作成します。

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### パイプラインを作成するには

1. **Data Factory エディター**で、ツール バーの **[新しいパイプライン]** をクリックします。ボタンが表示されない場合は、ツール バーの **[...] (省略記号)** をクリックします。または、ツリー ビューの **[パイプライン]** を右クリックして、**[新しいパイプライン]** をクリックする方法もあります。
2. 右側のウィンドウからの JSON スクリプトを JSON に置き換えます、 **PartitionGameLogsPipeline.json** ファイルを **C:\ADFWalkthrough\Pipelines** フォルダーです。
3. JSON の**閉じ角かっこ (']')** の最後に**コンマ (',')** を追加してから、その閉じ角かっこの後に次の 3 行を追加します。 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]このチュートリアルのサンプル データが 2014 年 5 月 1 日から 2014 年 5 月 5 日であるため、開始時刻と終了時刻が 2014 年 5 月 1 日および 2014 年 5 月 5 日に設定されていることに注意してください。
 
3. ツール バーの **[デプロイ]** クリックして、パイプラインを作成してデプロイします。エディターのタイトル バーに "**パイプラインが正常に作成されました**" というメッセージが表示されていることを確認します。
4. 次のファイルの内容で手順 1. ～ 3. を繰り返します。 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. キーを押して、データの工場出荷時のブレードを閉じる **X** (右上)、ホーム ページを参照してください (* * データ ファクトリ **ブレード)、データのファクトリの 。
### ダイアグラム ビュー

1.  **データ ファクトリ** ブレードで、 **LogProcessingFactory**, 、] をクリックして **ダイアグラム**です。 

	![ダイアグラムのリンク][image-data-factory-tutorial-diagram-link]

2. 表示されているダイアグラムは並べ替えることが可能で、以下のダイアグラムは上部が直接の入力を示し、下部が出力を示しています。わかりますの出力、 **PartitionGameLogsPipeline** EnrichGameLogsPipeline およびの出力への入力として渡され、 **EnrichGameLogsPipeline** に渡される、 **AnalyzeMarketingCampaignPipeline**です。タイトルをダブルクリックして、ブレードが示すアイテムについての詳細を表示します。

	![ダイアグラム ビュー][image-data-factory-tutorial-diagram-view]

3. 右クリック **AnalyzeMarketingCampaignPipeline**, 、] をクリック **開くパイプライン**です。アクティビティの入力呼び出し力のデータセットと、パイプライン内のすべてのアクティビティが表示されます。
 
	![開いているパイプライン](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	クリックして **データ ファクトリ** すべてのパイプラインでのダイアグラム ビューに戻るには左上隅にある階層リンク内です。


**ご利用ありがとうございます。** Azure データ ファクトリ、リンクされたサービス、パイプライン、テーブルを作成し、ワークフローを開始することに成功しました。


## <a name="MainStep6"></a> 手順 6: パイプラインとデータのスライスを監視します。 

1.	ない場合、 **データ ファクトリ** ブレードで、 **LogProcessingFactory** 開くを行う、次のいずれか。
	1.	クリックして **LogProcessingFactory** 上、 **スタート ボード**です。データのファクトリを作成するときに、 **スタート ボードに追加** オプションが自動的にチェックします。

		![スタート ボードの監視][image-data-factory-monitoring-startboard]

	2. をクリックして **参照** ハブ、およびクリック **すべて**です。
	 	
		![すべてのハブの監視][image-data-factory-monitoring-hub-everything]

		 **参照** ブレード、[ **データ ファクトリ** を選択して **LogProcessingFactory** で、 **データ ファクトリ** ブレードします。

		![[参照の Datafactories の監視][image-data-factory-monitoring-browse-datafactories]
2. いくつかの方法でデータ ファクトリが監視できます。パイプラインまたはデータ セットで監視が始められます。パイプラインで監視を始め、さらに詳しく学びましょう。 
3.	クリックして **パイプライン** 上、 **データ ファクトリ** ブレードします。 
4.	クリックして **PartitionGameLogsPipeline** パイプライン ブレードでします。 
5.	 **パイプライン** のブレードに **PartitionGameLogsPipeline**, 、パイプラインを使用すると表示 **RawGameEventsTable** データセット。クリックして **RawGameEventsTable**です。

	![パイプラインを使用し、生成][image-data-factory-monitoring-pipeline-consumed-produced]

6. テーブルのブレードで **RawGameEventsTable**, 、するすべてのスライスを参照してください。は、次のスクリーン ショットのすべてのスライスは **準備ができて** 状態であり、問題のスライスがないです。これは、そのデータがすぐに処理できることを意味します。

	![RawGameEventsTable テーブル ブレード][image-data-factory-monitoring-raw-game-events-table]

	どちらも **スライスを最近更新された** と **最近スライスが失敗しました** リストはによって並べ替えられます、 **最終更新時刻**です。次の状況では、スライスの更新時間が変更されます。

	-  スライスのステータスは、手動で更新するなどを使用して、 **セット AzureDataFactorySliceStatus** (または) をクリックして **実行** 上、 **スライス** スライスのブレードします。
	-  スライスは、実行のための状態を変更 (など、実行の開始、実行を終了し、失敗した、実行が成功したが終了したなど)。
 
	リストまたはのタイトルをクリックして **しています.(省略記号)** より大規模な一覧のスライスを参照してください。クリックして **フィルター** をスライス、フィルター処理するには、ツールバーのです。
	
	代わりに、スライスの開始時刻から終了時刻によって並べ替えられたデータのスライスを表示する] をクリックして **(スライスの時刻) でのデータ スライス** 並べて表示します。

	![データのスライスでスライスの時間][DataSlicesBySliceTime]
 
7. ここで、[、 **パイプライン** のブレードに **PartiionGameLogsPipeline**, 、] をクリックして **Produced**です。
8. このパイプラインが生成するデータ セットの一覧が表示されるはずです。 
9. クリックして **PartitionedGameEvents** テーブルに、 **データセットを生成する** ブレードします。 
10.	確認して、 **状態** のすべてのスライスに設定されている **準備ができて**です。 
11.	スライスのいずれかをクリックして **準備ができて** を参照してください、 **データ スライス** そのスライスのブレードします。

	![データ スライスを RawGameEventsTable ブレード][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	エラーがあった場合は、表示される、 **失敗 **ここでの状態。状態の両方のいずれかのスライスを表示することもあります **準備ができて**, 、またはその両方の状態 **PendingValidation**, 、スライスが処理する速度によって異なります。

	スライスではない場合、 **準備ができて** 状態、準備ができていないし、現在のスライスでの実行をブロックしているアップ ストリームのスライスにわかり、 **準備ができていない上流のスライス** ] ボックスの一覧です。
 
	参照してください、 [Azure データ工場出荷時の開発者向けリファレンス][developer-reference] のすべてのスライスの可能な状態の理解にします。

12.	 **データ スライス** ブレードからの実行] をクリックして、 **アクティビティの実行** ] ボックスの一覧です。そのスライスの [アクティビティの実行] ブレードが表示されるはずです。次を参照してください **アクティビティの実行の詳細** ブレードします。

	![アクティビティの実行の詳細] ブレード][image-data-factory-monitoring-activity-run-details]

13.	クリックして **ダウンロード** ファイルをダウンロードします。この画面は、HDInsight の処理で発生するエラーのトラブルシューティングを行うときに、特に役立ちます。
	 
	
見ることができます、すべてのパイプラインが実行を完了した場合、 **MarketingCampaignEffectivenessTable** で、 **MarketingCampaigns** 結果を表示する Azure SQL データベースです。

**ご利用ありがとうございます。** これでワークフローの監視とトラブルシューティングができます。Azure Data Factory を使用してデータを処理し分析結果を得る方法を学びました。

## 内部設置型データを使用するために、チュートリアルを拡張します。
ログ処理シナリオではこの記事で、チュートリアルの最後の手順では、マーケティング キャンペーンの有効性の出力は、Azure SQL データベースにコピーされました。分析のため、所属する組織内にある内部設置型の SQL Server にこのデータを移動することも可能です。
 
Azure Blob からマーケティング キャンペーンの有効性のデータをコピーするのには内部設置型 SQL Server、オンプレミスで追加のリンクされているサービスを作成する必要がある、テーブルとパイプラインは、この記事でこのチュートリアルで導入されました。

実際、 [チュートリアル: を使用して内部設置型データ ソース][tutorial-onpremises] マーケティング キャンペーンの有効性のデータを内部設置型 SQL Server データベースにコピーするためのパイプラインを作成する方法について説明します。


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


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

<!---HONumber=GIT-SubDir--> 