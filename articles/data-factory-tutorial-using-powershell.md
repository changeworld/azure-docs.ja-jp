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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# チュートリアル: 移動し、データのファクトリを使用してログ ファイルの処理を [PowerShell]
この記事では、Azure Data Factory を使用してデータをログ ファイルから洞察へと変化させながら、ログ処理の標準的なシナリオを包括的なチュートリアルとして提供します。

## シナリオ
Contoso は、ゲーム機、携帯機器、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。これらのゲームはそれぞれが大量のログを産み出します。Contoso 社の目標は、これらのゲームが産み出すログを収集解析することで有益な情報を手に入れ、アップセルやクロスセルの機会を見極め、新しい魅力的な機能などを開発する、つまりビジネスを向上させて顧客により良い体験を提供することです。
 
このチュートリアルでは、サンプル ログを収集、処理して参照データで強化し、このデータを変換して Contoso 社 が最近開始したマーケティング キャンペーンの有効性を評価します。

## チュートリアルの準備をする
1.	読み取り [Azure のデータのファクトリの概要][adfintroduction] を Azure のデータのファクトリの概要と、最上位レベルの概念の理解を取得します。
2.	このチュートリアルを実施するには Azure サブスクリプションが必要です。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。
3.	ダウンロードしてインストールする必要があります [Azure PowerShell][download-azure-powershell] コンピューターにします。 
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
エンド ツー エンドのワークフローは、次に示します。 ![チュートリアルのエンド ツー エンド フロー][image-data-factory-tutorial-end-to-end-flow]

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

1.	ログインした後、 [Azure プレビュー ポータル][azure-preview-portal], 、] をクリックして **新規** 、左下隅にあるとをクリックしてから **データ ファクトリ** 上、 **新規** ブレードします。 

	![新しい DataFactory -> します。][image-data-factory-new-datafactory-menu]
	
	表示されない場合は、 **データ ファクトリ** 上、 **新規** ブレードで下にスクロールします。
	
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

> [AZURE.NOTE]この記事は、関連付けられているサービス、テーブル、およびパイプラインを作成するには、Azure PowerShell を使用します。参照してください [データ工場出荷時のエディターを使用してチュートリアル][adftutorial-using-editor] Azure ポータルで、具体的にはデータ工場出荷時のエディターを使用してこのチュートリアルを実行するかどうか。

このステップでは、次の関連付けられているサービスを作成します。 StorageLinkedService、AzureSqlLinkedService、HDInsightStorageLinkedService、および HDInsightLinkedService です。


1.	 **LogProcessingFactory** ブレードをクリックして **関連付けられているサービス** 並べて表示します。

	![関連付けられているサービスのタイル][image-data-factory-tutorial-linkedservice-tile]

2.  **関連付けられているサービス** ブレードで] をクリックして **+ データ ストア** コマンド バーからです。

	![関連付けられているサービスでは、ストアを追加します。][image-data-factory-tutorial-linkedservices-add-datstore]

3.  **データ ストアの新しい** ブレードでを入力 **StorageLinkedService** の **名前**, 、] をクリックして **型 (必要な設定)**, を選択して **Azure のストレージ アカウント**です。

	![データ ストアの種類 - Azure のストレージ][image-data-factory-tutorial-datastoretype-azurestorage]

4.  **データ ストアの新しい** ブレードでは、2 つの新しいフィールドを参照してください。 **アカウント名** と **アカウント キー**です。アカウント名とアカウント キーを入力、 **Azure ストレージ アカウント**です。

	![Azure のストレージ設定][image-data-factory-tutorial-azurestorage-settings]

	以下に示すように、ポータルから Azure ストレージ アカウントのアカウント名とアカウント キーを取得できます。

	![ストレージ キー][image-data-factory-tutorial-storage-key]
  
5. クリックした後 **[ok]** 、新しいデータ ストアのブレードが表示されます **StorageLinkedService** の一覧で、 **データ ストア** 上、 **関連付けられているサービス** ブレードします。確認 **通知** (左側) のハブにすべてのメッセージを参照してください。

	![記憶域と関連付けられているサービス ブレード][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. 繰り返し **手順 2 ～ 5** リンクという名前のサービスの新しいインスタンスを作成する: **HDInsightStorageLinkedService**です。これは、HDInsight クラスターで使用するストレージです。
7. 両方を表示することを確認 **StorageLinkedService** と **HDInsightStorageLinkedService** 、関連付けられているサービスのブレードにある一覧でします。
8.  **関連付けられているサービス** ブレードで] をクリックして **追加 (+) データ ストア** コマンド バーからです。
9. 入力 **AzureSqlLinkedService** の名前。
10. クリックして **型 (必要な設定)**, を選択して **Azure SQL データベース**です。
11. これでする必要があります、次の追加のフィールドに、 **データ ストアの新しい** ブレードします。Azure SQL データベースの名前を入力してください。 **サーバー**, 、**データベース** 名、 **ユーザー名**, 、および **パスワード**, 、] をクリック **[ok]**です。
	1. 入力 **MarketingCampaigns** の **データベース**です。これは、手順 1. で実行するスクリプトが作成した Azure SQL データベースです。このデータベースが実際に作成されたこと、スクリプトで (このようなエラーが発生しました) を確認する必要があります。
		
 		![Azure の SQL の設定][image-data-factory-tutorial-azuresql-settings]

		Azure 管理ポータルからこれらの値を取得するには、[MarketingCampaigns データベースの SQL Database 接続文字列を表示する] をクリックします。

		![Azure SQL データベースの接続文字列][image-data-factory-tutorial-azuresql-database-connection-string]

12. 作成したすべての 3 つのデータ ストアが表示されることを確認します。 **StorageLinkedService**, 、**HDInsightStorageLinkedService**, 、および **AzureSqlLinkedService**です。
13. 別のリンクされているサービスを作成する必要がありますが、具体的には、コンピューティング サービスにこの **Azure の HDInsight クラスター**です。ポータルでは、まだリンクされたコンピューティング サービスの作成をサポートしていません。そのため、Azure PowerShell を使用して、このリンクされたサービスを作成する必要があります。 
14. 切り替える **Azure PowerShell** 既に開かれている (または) の起動がある場合 **Azure PowerShell**です。終了が Azure PowerShell を再度開く場合は、次のコマンドを実行する必要があります。 
	- 実行 **Add-azureaccount** 、ユーザー名と、Azure プレビュー ポータルにサインインに使用するパスワードを入力します。  
	- 実行 **Get-azuresubscription** をこのアカウントのすべてのサブスクリプションを表示します。
	- 実行 **Select-azuresubscription** を使用するサブスクリプションを選択します。このサブスクリプションは、Azure プレビュー ポータルで使用したものと同じである必要があります。 
15. 切り替える **AzureResourceManager** モードとして、Azure のデータのファクトリ コマンドレットはこのモードで使用できます。

		Switch-AzureMode AzureResourceManager

16. 移動し、 **LinkedServices** サブフォルダーに **C:\ADFWalkthrough** 、場所、ファイルを抽出したフォルダーから (または)。
17. 開いている **HDInsightLinkedService.json** 、好みのエディターと型に設定されている通知で **HDInsightOnDemandLinkedService**です。


	> [AZURE.NOTE]Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。試しに、オンデマンド クラスターを使用してみましょう。HDInsightLinkedService は、データのファクトリをオンデマンドで HDInsight クラスターをリンクします。HDInsight クラスターを使用するのには (replace clustername、ユーザー名、および適切な値のパスワード) を次に示すよう HDInsightLinkedService.json ファイルの [プロパティ] セクションを更新します。
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. 以下のコマンドを使用して、データ ファクトリの名前に $df 変数を設定します。

		$df = “LogProcessingFactory”
19. コマンドレットを使用して **新規 AzureDataFactoryLinkedService** 次のようにリンクされているサービスを作成します。ストレージ アカウントから始めます。

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	ResourceGroupName、DataFactoryName または LinkedService の名前に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、リンクされたサービスの JSON ファイルが見つからない場合は、そのファイルの完全なパスを入力します。
20. 次の 4 つすべての関連付けられているサービスを参照してください、 **関連付けられているサービス** ブレードの次のようです。リンクされているサービスのブレードが開いていない場合に関連付けられているサービスをクリックして、 **データ ファクトリ** ページ **LogProcessingFactory**です。[リンクされたサービス] ブレードが更新されるまで数秒かかる場合があります。

	![サービスのリンクすべて][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> 手順 4: テーブルを作成します。 
この手順では、以下のテーブルを作成します。

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![チュートリアルのエンド ツー エンド フロー][image-data-factory-tutorial-end-to-end-flow]
 
上の図は、中央の列がパイプラインを、上と下の列がテーブルを表しています。

Azure ポータルはデータ セットとテーブルの作成をまだサポートしていないため、今回のリリースでは Azure PowerShell を使用してテーブルを作成する必要があります。

### テーブルを作成するには

1.	移動し、Azure PowerShell、 **テーブル** フォルダー (* * C:\ADFWalkthrough\Tables**) が、サンプルを解凍した場所からです。 
2.	コマンドレットを使用して **新規 AzureDataFactoryTable** の次のようにテーブルを作成する **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	ResourceGroupName および DataFactoryName に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、テーブルの JSON ファイルがコマンドレットで見つけられない場合は、そのファイルの完全なパスを入力します。

3. 前の手順を繰り返して、以下のテーブルを作成します。
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4.  **Azure プレビュー ポータル**, 、] をクリックして **データセット** で、 **データ ファクトリ** のブレードに **LogProcessingFactory** のすべてのデータセットが表示されることを確認し、(テーブルは四角形のデータセット)。

	![データをすべての設定します。][image-data-factory-tutorial-datasets-all]

	また、Azure PowerShell から次のコマンドを使用することも可能です。
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> 手順 5: を作成して、パイプラインのスケジュールを設定します。
このステップでは、次のパイプラインを作成します。 PartitionGameLogsPipeline、EnrichGameLogsPipeline、および AnalyzeMarketingCampaignPipeline です。

1.  **Windows エクスプ ローラー**, に移動し、 **パイプライン** でサブ フォルダー **C:\ADFWalkthrough** フォルダー (または、サンプルを抽出した場所から)。
2.	開いている **PartitionGameLogsPipeline.json** 任意のエディターで、データのストレージ アカウントの情報には、ストレージ アカウントに、強調表示されている置き換えます、ファイルを保存します。
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. 手順を繰り返して、以下のパイプラインを作成します。
	1. **EnrichGameLogsPipeline**.json (3 回の出現)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 回の出現)

	**重要:** すべてを交換したことを確認 <storageaccountname> をストレージ アカウント名です。
 
4.   **Azure PowerShell**, に移動し、 **パイプライン** でサブ フォルダー **C:\ADFWalkthrough** フォルダー (または、サンプルを抽出した場所から)。
5.  コマンドレットを使用して **新規 AzureDataFactoryPipeline** の次のように、パイプラインを作成する **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	ResourceGroupName、DataFactoryName、またはパイプライン名に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、パイプラインの JSON ファイルのファイルの完全パスを提供します。
6. 前の手順を繰り返して、以下のパイプラインを作成します。
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. コマンドレットを使用して **Get AzureDataFactoryPipeline** のパイプラインの一覧を取得します。
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. パイプラインを作成したら、データ処理を実行する期間を指定できます。パイプラインの有効期間を指定することで、各 ADF テーブルに対して定義された Availability プロパティに基づいてデータ スライスが処理される期間を定義します。

パイプラインの有効期間を指定するために、Set-AzureDataFactoryPipelineActivePeriod コマンドレットが使用できます。このチュートリアルでサンプル データは 05/01 ～ 05/05 です。使用して 2014年-05-01、StartDateTime として。EndDateTime は任意です。
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. パイプラインの有効期間を設定することを確認します。
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. 前の 2 つの手順を繰り返して、以下のパイプラインの有効期間を設定します。
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11.  **Azure プレビュー ポータル**, 、] をクリックして **パイプライン** タイル (パイプラインの名前) ではなく、 **データ ファクトリ** ブレードで、 **LogProcessingFactory**, 、作成したパイプラインが表示されます。

	![すべてのパイプライン][image-data-factory-tutorial-pipelines-all]

12.  **データ ファクトリ** ブレードで、 **LogProcessingFactory**, 、] をクリックして **ダイアグラム**です。

	![ダイアグラムのリンク][image-data-factory-tutorial-diagram-link]

13. 表示されているダイアグラムは並べ替えることが可能で、以下のダイアグラムは上部が直接の入力を示し、下部が出力を示しています。わかりますの出力、 **PartitionGameLogsPipeline** EnrichGameLogsPipeline およびの出力への入力として渡され、 **EnrichGameLogsPipeline** に渡される、 **AnalyzeMarketingCampaignPipeline**です。タイトルをダブルクリックして、ブレードが示すアイテムについての詳細を表示します。

	![ダイアグラム ビュー][image-data-factory-tutorial-diagram-view]

	**ご利用ありがとうございます。** Azure データ ファクトリ、リンクされたサービス、パイプライン、テーブルを作成し、ワークフローを開始することに成功しました。


## <a name="MainStep6"></a> 手順 6: パイプラインとデータのスライスを監視します。 

1.	[LogProcessingFactory] の [データ ファクトリ] ブレードを開いていない場合、以下のいずれかを実施できます。
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
 
7. ここで、[、 **パイプライン** のブレードに **PartiionGameLogsPipeline**, 、] をクリックして **Produced**です。
8. このパイプラインが生成するデータ セットの一覧が表示されるはずです。 
9. クリックして **PartitionedGameEvents** テーブルに、 **データセットを生成する** ブレードします。 
10.	確認して、 **状態** のすべてのスライスに設定されている **準備ができて**です。 
11.	スライスのいずれかをクリックして **準備ができて** を参照してください、 **データ スライス** そのスライスのブレードします。

	![データ スライスを RawGameEventsTable ブレード][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	エラーがあった場合は、表示される、 **失敗 **ここでの状態。状態の両方のいずれかのスライスを表示することもあります **準備ができて**, 、またはその両方の状態 **PendingValidation**, 、スライスが処理する速度によって異なります。
 
	参照してください、 [Azure データ工場出荷時の開発者向けリファレンス][developer-reference] のすべてのスライスの可能な状態の理解にします。

12.	 **データ スライス** ブレードからの実行] をクリックして、 **アクティビティの実行** ] ボックスの一覧です。そのスライスの [アクティビティの実行] ブレードが表示されるはずです。次を参照してください **アクティビティの実行の詳細** ブレードします。

	![アクティビティの実行の詳細] ブレード][image-data-factory-monitoring-activity-run-details]

13.	クリックして **ダウンロード** ファイルをダウンロードします。この画面は、HDInsight の処理で発生するエラーのトラブルシューティングを行うときに、特に役立ちます。
	 
	
見ることができます、すべてのパイプラインが実行を完了した場合、 **MarketingCampaignEffectivenessTable** で、 **MarketingCampaigns** 結果を表示する Azure SQL データベースです。

**ご利用ありがとうございます。** これでワークフローの監視とトラブルシューティングができます。Azure Data Factory を使用してデータを処理し分析結果を得る方法を学びました。

## 内部設置型データを使用するために、チュートリアルを拡張します。
ログ処理シナリオではこの記事で、チュートリアルの最後の手順では、マーケティング キャンペーンの有効性の出力は、Azure SQL データベースにコピーされました。分析のため、所属する組織内にある内部設置型の SQL Server にこのデータを移動することも可能です。
 
Azure Blob からマーケティング キャンペーンの有効性のデータをコピーするのには内部設置型 SQL Server、オンプレミスで追加のリンクされているサービスを作成する必要がある、テーブルとパイプラインは、この記事でこのチュートリアルで導入されました。

実際、 [チュートリアル: を使用して内部設置型データ ソース][tutorial-onpremises-using-powershell] マーケティング キャンペーンの有効性のデータを内部設置型 SQL Server データベースにコピーするためのパイプラインを作成する方法について説明します。
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir-->