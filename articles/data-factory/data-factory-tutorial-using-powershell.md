<properties 
	pageTitle="Azure Data Factory を使用してログ ファイルの移動と処理を行う (Azure PowerShell)" 
	description="この高度なチュートリアルでは、現実に近いシナリオについて説明し、そのシナリオを Azure Data Factory サービスと Azure PowerShell を使用して実装します。" 
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
	ms.date="10/29/2015" 
	ms.author="spelluru"/>

# チュートリアル: Data Factory を使用したログ ファイルの移動と処理 [PowerShell]
この記事では、Azure Data Factory を使用してデータをログ ファイルから洞察へと変化させながら、ログ処理の標準的なシナリオを包括的なチュートリアルとして提供します。

> [AZURE.IMPORTANT]この記事では、すべての Data Factory コマンドレットを取り上げているわけではありません。Data Factory コマンドレットに関する包括的なドキュメントについては、「[Data Factory コマンドレット リファレンス][cmdlet-reference]」を参照してください。
>    
> Azure PowerShell 1.0 プレビューを使用する場合は、[ここ](https://msdn.microsoft.com/library/dn820234.aspx)に記載されているコマンドレットを使用する必要があります。たとえば、New-AzureDataFactory を使用する代わりに、New-AzureRMDataFactory を使用します。

## シナリオ
Contoso は、ゲーム機、携帯デバイス、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。これらのゲームはそれぞれが大量のログを産み出します。Contoso 社の目標は、これらのゲームが産み出すログを収集解析することで有益な情報を手に入れ、アップセルやクロスセルの機会を見極め、新しい魅力的な機能などを開発する、つまりビジネスを向上させて顧客により良い体験を提供することです。
 
このチュートリアルでは、サンプル ログを収集、処理して参照データで強化し、このデータを変換して Contoso 社 が最近開始したマーケティング キャンペーンの有効性を評価します。

## チュートリアルの準備をする
1.	「[Azure Data Factory の概要][adfintroduction]」を読んで Azure Data Factory の概要を理解し、全体的な概念を把握します。
2.	このチュートリアルを実施するには Azure サブスクリプションが必要です。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料試用版][azure-free-trial]に関するページを参照してください。
3.	[Azure PowerShell][download-azure-powershell] をダウンロードしてコンピューターにインストールする必要があります。 
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
	- Azure SQL データベース
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
全体のワークフローは次のとおりです。![チュートリアルの全体のフロー][image-data-factory-tutorial-end-to-end-flow]

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

1.	[Azure ポータル][azure-portal]にログインしたら、左下隅の **[新規]** をクリックし、**[新規]** ブレードの **[Data Factory]** をクリックします。 

	![New->DataFactory][image-data-factory-new-datafactory-menu]
	
	**[Data Factory]** が **[新規]** ブレードに表示されていない場合は、下にスクロールします。
	
5. **[新しいデータ ファクトリ]** ブレードで、**[名前]** フィールドに「**LogProcessingFactory**」と入力します。

	![[データ ファクトリ] ブレード][image-data-factory-tutorial-new-datafactory-blade]

6. "**ADF**" という名前の Azure リソース グループをまだ作成していない場合は、次の手順を実行します。
	1. **[リソース グループ名]** をクリックし、**[新しいリソース グループを作成]** をクリックします。
	
		![[リソース グループ] ブレード][image-data-factory-tutorial-resourcegroup-blade]
	2. **[リソース グループを作成]** ブレードで、リソース グループの名前として「**ADF**」と入力し、**[OK]** をクリックします。
	
		![リソース グループの作成][image-data-factory-tutorial-create-resourcegroup]
7. **[リソース グループ名]** の **[ADF]** を選択します。  
8.	**[新しいデータ ファクトリ]** ブレードで、**[スタート画面に追加]** が既定で選択されていることに注意してください。これにより、スタート画面 (Azure ポータルへのログイン時に表示される画面) のデータ ファクトリにリンクが追加されます。

	![データ ファクトリ作成ブレード  
][image-data-factory-tutorial-create-datafactory]

9.	**[新しいデータ ファクトリ]** ブレードで、**[作成]** をクリックしてデータ ファクトリを作成します。
10.	データ ファクトリが作成されると、**LogProcessingFactory** というタイトルの **[Data Factory]** ブレードが表示されます。

	![データ ファクトリのホーム ページ][image-data-factory-tutorial-datafactory-homepage]

	
	表示されない場合は、次のいずれかを行います。

	- **スタート画面** (ホーム ページ) で **[LogProcessingFactory]** をクリックします。
	- 左側の **[参照]** をクリックし、**[すべて]**、**[Data Factory]** の順にクリックし、最後に目的のデータ ファクトリをクリックします。
 
	Azure Data Factor の名前はグローバルに一意にする必要があります。**""LogProcessingFactory" という名前の Data Factory は使用できません"** というエラー メッセージが表示された場合は、名前を変更します (例: yournameLogProcessingFactory)。このチュートリアルの手順の実行中に、この名前を LogProcessingFactory の代わりに使用します。
 
## <a name="MainStep3"></a>手順 3. リンクされたサービスを作成する

> [AZURE.NOTE]この記事では、Azure PowerShell を使用して、リンク サービス、テーブル、パイプラインを作成します。Azure クラシック ポータル、具体的には Data Factory エディターを使用してこのチュートリアルを実行する場合は、[Data Factory エディターの使用方法に関するチュートリアル][adftutorial-using-editor]をご覧ください。

この手順では、StorageLinkedService、AzureSqlLinkedService、HDInsightStorageLinkedService、HDInsightLinkedService というリンクされたサービスを作成します。


1.	**[LogProcessingFactory]** ブレードで、**[リンクされたサービス]** タイルをクリックします。

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. **[リンクされたサービス]** ブレードで、コマンド バーの **[+ データ ストア]** をクリックします。

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. **[新しいデータ ストア]** ブレードで、**[名前]** フィールドに「**StorageLinkedService**」と入力し、**[種類 (設定が必要)]** をクリックして、**[Azure ストレージ アカウント]** を選択します。

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. **[新しいデータ ストア]** ブレードに、**[アカウント名]** と **[アカウント キー]** の 2 つの新しいフィールドが表示されます。**Azure ストレージ アカウント**のアカウント名とアカウント キーを入力します。

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	以下に示すように、ポータルから Azure ストレージ アカウントのアカウント名とアカウント キーを取得できます。

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. [新しいデータ ストア] ブレードで **[OK]** をクリックすると、**[リンクされたサービス]** ブレードの **[データ ストア]** の一覧に **[StorageLinkedService]** が表示されます。(左側の) **[通知]** ハブでメッセージを確認します。

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. **手順 2. ～ 5.** を繰り返して、**HDInsightStorageLinkedService** という名前の別のリンクされたサービスを作成します。これは、HDInsight クラスターで使用するストレージです。
7. [リンクされたサービス] ブレードの一覧に、**StorageLinkedService** と **HDInsightStorageLinkedService** が表示されていることを確認します。
8. **[リンクされたサービス]** ブレードで、コマンド バーの **[+ データ ストア]** をクリックします。
9. 名前として「**AzureSqlLinkedService**」と入力します。
10. **[種類 (設定が必要)]** をクリックし、**[Azure SQL Database]** を選択します。
11. これで、**[新しいデータ ストア]** ブレードに、次の追加フィールドが表示されます。Azure SQL Database の**サーバー**、**データベース名**、**ユーザー名**、**パスワード**を入力し、**[OK]** をクリックします。
	1. **[データベース]** に「**MarketingCampaigns**」と入力します。これは、手順 1. で実行したスクリプトによって作成された Azure SQL Database です。このデータベースがスクリプトによって実際に作成されていることを確認する必要があります (エラーが発生した場合)。
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		[Azure クラシック ポータル](http://manage.windowsazure.com)からこれらの値を取得するには、[MarketingCampaigns データベースの SQL Database 接続文字列を表示する] をクリックします。

		![Azure SQL Database 接続文字列][image-data-factory-tutorial-azuresql-database-connection-string]

12. 作成した 3 つのデータ ストア (**StorageLinkedService**、**HDInsightStorageLinkedService**、**AzureSqlLinkedService**) がすべて表示されていることを確認します。
13. リンクされたサービスをもう 1 つ作成する必要がありますが、これはコンピューティング サービス (具体的には **Azure HDInsight クラスター**) にリンクされたサービスです。ポータルでは、まだリンクされたコンピューティング サービスの作成をサポートしていません。そのため、Azure PowerShell を使用して、このリンクされたサービスを作成する必要があります。 
14. 既に開いている場合は **Azure PowerShell** に切り替え、そうでない場合は **Azure PowerShell** を起動します。Azure PowerShell を一度閉じてから再度開いた場合は、次のコマンドを実行する必要があります。 
	- **Add-AzureAccount** を実行し、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。  
	- **Get-AzureSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
	- **Select-AzureSubscription** を実行して、使用するサブスクリプションを選択します。このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。 
15. Azure Data Factory コマンドレットは **AzureResourceManager** モードでのみ使用できるので、このモードに切り替えます。

		Switch-AzureMode AzureResourceManager

16. **C:\\ADFWalkthrough** またはファイルの展開先のフォルダーから **LinkedServices** サブフォルダーに移動します。
17. 好みのエディターで **HDInsightLinkedService.json** を開き、種類が **HDInsightOnDemandLinkedService** に設定されていることを確認します。


	Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。試しに、オンデマンド クラスターを使用してみましょう。
	
	HDInsightLinkedService は、オンデマンド HDInsight クラスターをデータ ファクトリにリンクします。独自の HDInsight クラスターを使用するには、次に示すように HDInsightLinkedService.json ファイルの Properties セクションを更新します (クラスター名、ユーザー名、パスワードを適切な値に置き換えます)。
	
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
19. **New-AzureDataFactoryLinkedService** コマンドレットを使用して、以下のようにリンクされたサービスを作成します。ストレージ アカウントから始めます。

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	ResourceGroupName、DataFactoryName または LinkedService の名前に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、リンクされたサービスの JSON ファイルが見つからない場合は、そのファイルの完全なパスを入力します。
20. 次に示すように、**[リンクされたサービス]** ブレードに 4 つのリンク サービスがすべて表示されます。[リンクされたサービス] ブレードが開いていない場合は、**[LogProcessingFactory]** の **[DATA FACTORY]** ページで [リンクされたサービス] をクリックします。[リンクされたサービス] ブレードが更新されるまで数秒かかる場合があります。

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a>手順 4. テーブルを作成する 
この手順では、以下のテーブルを作成します。

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![チュートリアルの全体のフロー][image-data-factory-tutorial-end-to-end-flow]
 
上の図は、中央の列がパイプラインを、上と下の列がテーブルを表しています。

Azure クラシック ポータルはデータ セットとテーブルの作成をまだサポートしていないため、このリリースでは Azure PowerShell を使用してテーブルを作成する必要があります。

### テーブルを作成するには

1.	Azure PowerShell で、サンプルを展開した場所から **Tables** フォルダー (**C:\\ADFWalkthrough\\Tables**) に移動します。
2.	**New-AzureDataFactoryDataset** コマンドレットを使用して、次のように **RawGameEventsTable**.json のデータセットを作成します。	


		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	ResourceGroupName および DataFactoryName に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、テーブルの JSON ファイルがコマンドレットで見つけられない場合は、そのファイルの完全なパスを入力します。

3. 前の手順を繰り返して、以下のテーブルを作成します。
		
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. **Azure ポータル**で、**[LogProcessingFactory]** の **[Data Factory]** ブレードの **[データセット]** をクリックし、すべてのデータセットが表示されていることを確認します (テーブルは四角形のデータセットです)。

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	また、Azure PowerShell から次のコマンドを使用することも可能です。
			
		Get-AzureDataFactoryDataset –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a>手順 5. パイプラインを作成してスケジュールを設定する
この手順では、PartitionGameLogsPipeline、EnrichGameLogsPipeline、AnalyzeMarketingCampaignPipeline の各パイプラインを作成します。

1. **エクスプローラー**で、**C:\\ADFWalkthrough** フォルダー (またはサンプルを展開した場所) から **Pipelines** サブフォルダーに移動します。
2.	好みのエディターで **PartitionGameLogsPipeline.json** を開き、強調表示されている部分をデータ ストレージ アカウント情報の自分のストレージ アカウントに置き換え、ファイルを保存します。
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. 手順を繰り返して、以下のパイプラインを作成します。
	1. **EnrichGameLogsPipeline**.json (出現回数 3 回)
	2. **AnalyzeMarketingCampaignPipeline**.json (出現回数 3 回)

	**重要:** すべての <storageaccountname> を自分のストレージ アカウント名に置き換えたことを確認してください。
 
4.  **Azure PowerShell** で、**C:\\ADFWalkthrough** フォルダー (またはサンプルを展開した場所) から **Pipelines** サブフォルダーに移動します。
5.  **New-AzureDataFactoryPipeline** コマンドレットを使用して、次のように **PartitionGameLogspeline**.json のパイプラインを作成します。	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	ResourceGroupName、DataFactoryName、またはパイプライン名に別の名前を使用している場合は、上記コマンドレットでそれらの名前を参照します。また、パイプラインの JSON ファイルのファイルの完全パスを提供します。
6. 前の手順を繰り返して、以下のパイプラインを作成します。
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. **Get-AzureDataFactoryPipeline** コマンドレットを使用して、パイプラインの一覧を取得します。
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. パイプラインを作成したら、データ処理を実行する期間を指定できます。パイプラインの有効期間を指定することで、各 ADF テーブルに対して定義された Availability プロパティに基づいてデータ スライスが処理される期間を定義します。

パイプラインの有効期間を指定するために、Set-AzureDataFactoryPipelineActivePeriod コマンドレットが使用できます。このチュートリアルでは、サンプル データは 05/01 ～ 05/05 のものです。2014-05-01 を StartDateTime として使用します。EndDateTime は任意です。
			
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

11. **Azure ポータル**で、**[LogProcessingFactory]** の **[Data Factory]** ブレードの (パイプライン名ではなく) **[パイプライン]** タイルをクリックすると、作成したパイプラインが表示されます。

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. **[LogProcessingFactory]** の **[Data Factory]** ブレードで、**[ダイアグラム]** をクリックします。

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. 表示されているダイアグラムは並べ替えることが可能で、以下のダイアグラムは上部が直接の入力を示し、下部が出力を示しています。**PartitionGameLogsPipeline** の出力が EnrichGameLogsPipeline に入力として渡され、**EnrichGameLogsPipeline** の出力が **AnalyzeMarketingCampaignPipeline** に渡されていることがわかります。タイトルをダブルクリックして、ブレードが示すアーティファクトについての詳細を表示します。

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**ご利用ありがとうございます。** Azure データ ファクトリ、リンクされたサービス、パイプライン、テーブルを作成し、ワークフローを開始することに成功しました。


## <a name="MainStep6"></a>手順 6. パイプラインとデータ スライスを監視する 

1.	[LogProcessingFactory] の [データ ファクトリ] ブレードを開いていない場合、以下のいずれかを実施できます。
	1.	**スタート画面**で **[LogProcessingFactory]** をクリックします。データ ファクトリの作成中に、**[スタート画面に追加]** オプションが自動でオンになっています。

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. **[参照]** ハブをクリックし、**[すべて]** をクリックします。
	 	
		![すべてのハブの監視][image-data-factory-monitoring-hub-everything]

		**[参照]** ブレードで **[Data Factory]** を選択し、**[Data Factory]** ブレードで **[LogProcessingFactory]** を選択します。

		![データ ファクトリの監視][image-data-factory-monitoring-browse-datafactories]
2. いくつかの方法でデータ ファクトリが監視できます。パイプラインまたはデータ セットで監視が始められます。パイプラインで監視を始め、さらに詳しく学びましょう。 
3.	**[Data Factory]** ブレードの **[パイプライン]** をクリックします。 
4.	[パイプライン] ブレードで **[PartitionGameLogsPipeline]** をクリックします。 
5.	**[PartitionGameLogsPipeline]** の **[パイプライン]** ブレードを見ると、パイプラインが **[RawGameEventsTable]** データセットを使用していることがわかります。**[RawGameEventsTable]** をクリックします。

	![パイプラインでの使用と生成][image-data-factory-monitoring-pipeline-consumed-produced]

6. **[RawGameEventsTable]** の [テーブル] ブレードに、すべてのスライスが表示されています。以下のスクリーン ショットでは、すべてのスライスが **[準備完了]** の状態で、問題のあるスライスはありません。これは、そのデータがすぐに処理できることを意味します。

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. **[PartiionGameLogsPipeline]** の **[パイプライン]** ブレードで、**[生成済み]** をクリックします。
8. このパイプラインが生成するデータ セットの一覧が表示されるはずです。 
9. **[生成済みデータセット]** ブレードの **[PartitionedGameEvents]** テーブルをクリックします。 
10.	すべてのスライスの **[状態]** が **[準備完了]** になっていることを確認します。 
11.	**[準備完了]** になっているスライスの 1 つをクリックし、そのスライスの **[データ スライス]** ブレードを表示します。

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	エラーが発生していた場合、ここに **[失敗]** という状態が表示されます。また、スライスがどの程度の速さで処理されるかによって、両方のスライスが **[準備完了]** 状態で表示される場合もあれば、**[検証を保留中]** 状態で表示される場合もあります。
 
	[Data Factory の開発者用リファレンス][developer-reference]を参照し、考えられるすべてのスライスの状態を把握してください。

12.	**[データ スライス]** ブレードで、**[アクティビティの実行]** の一覧から [実行] をクリックします。そのスライスの [アクティビティの実行] ブレードが表示されるはずです。以下の **[アクティビティの実行の詳細]** ブレードが表示されます。

	![[アクティビティの実行の詳細] ブレード][image-data-factory-monitoring-activity-run-details]

13.	**[ダウンロード]** をクリックしてファイルをダウンロードします。この画面は、HDInsight の処理で発生するエラーのトラブルシューティングを行うときに、特に役立ちます。
	 
	
すべてのパイプラインが実行を完了すると、Azure SQL Database である **MarketingCampaigns** 内の **MarketingCampaignEffectivenessTable** を調べて実行結果を確認できます。

**お疲れさまでした。** これでワークフローの監視とトラブルシューティングができます。Azure Data Factory を使用してデータを処理し分析結果を得る方法を学びました。

## チュートリアルをさらに進めてオンプレミス データを使用する
この記事のチュートリアルにあるログ処理のシナリオの最後の手順で、マーケティング キャンペーンの有効性の出力が Azure SQL Database にコピーされました。分析のため、所属する組織内にあるオンプレミスの SQL Server にこのデータを移動することも可能です。
 
マーケティング キャンペーンの有効性データを Azure BLOB からオンプレミスの SQL Server にコピーするには、この記事のチュートリアルで導入したオンプレミスのリンクされたサービス、テーブル、パイプラインを追加で作成する必要があります。

[オンプレミス データ ソースの使用に関するチュートリアル][tutorial-onpremises-using-powershell]を実践して、マーケティング キャンペーンの有効性データをオンプレミスの SQL Server データベースにコピーするためのパイプラインを作成する方法を習得します。
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
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

<!---HONumber=AcomDC_1203_2015-->