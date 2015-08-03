<properties 
	pageTitle="Azure Data Factory のトラブルシューティング" 
	description="Azure データ ファクトリの使用に関する問題のトラブルシューティングを行う方法について説明します。" 
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Data Factory のトラブルシューティング
Azure ポータル (または) Azure PowerShell コマンドレットを使用して Azure Data Factory の問題についてトラブルシューティングを行うことができます。このトピックには、Azure ポータルを使用し、Data Factory で発生するエラーをすばやく解決する方法について学べるチュートリアルが含まれています。

## 問題: Data Factory コマンドレットを実行できない
この問題を解決するには、Azure のモードを **AzureResourceManager** に切り替えます。

**Azure PowerShell** を起動し、以下のコマンドを実行して **AzureResourceManager** モードに切り替えます。**AzureResourceManager** モードでは Azure Data Factory コマンドレットが使用できます。

         switch-azuremode AzureResourceManager

## 問題: Data Factory コマンドレットを実行する際の認証エラー
Azure PowerShell で使用する Azure アカウントまたはサブスクリプションが正しくない可能性があります。次のコマンドレットを使用して、Azure PowerShell で使用する適切な Azure アカウントとサブスクリプションを選択してください。

1. Add-AzureAccount - 適切なユーザー ID とパスワードを使用します。
2. Get-AzureSubscription - アカウントのサブスクリプションをすべて表示します。 
3. Select-AzureSubscription <subscription name> - 適切なサブスクリプションを選択します。Azure プレビュー ポータルでデータ ファクトリの作成に使用するのと同じものを使用します。

## 問題: Azure ポータルから Data Gateway の高速セットアップを起動できない
Data Gateway の高速セットアップを起動するには、Internet Explorer または Microsoft ClickOnce と互換性のある Web ブラウザーが必要です。高速セットアップを開始できない場合、次の手順を実行できます。

1. 他のブラウザーで失敗した場合は、Internet Explorer に切り替えます。または
2. ポータルの同じブレードに表示される [手動セットアップ] リンクを使用してインストールを実行し、Data Management Gateway 構成の準備ができたら、画面に表示されているキーをコピーしておき、Data Management Gateway 構成の準備ができたときに貼り付けます。起動しない場合は、"Microsoft Data Management Gateway" の [スタート] メニューを確認し、起動した時点でキーを貼り付けます。 


## 問題: Azure ポータルから資格情報マネージャーを起動できない
Azure ポータルから SQL Server のリンクされたサービスをセットアップまたは更新する場合、資格情報マネージャー アプリケーションが起動してセキュリティを確保します。この場合、Internet Explorer か、Microsoft の ClickOnce と互換性のある Web ブラウザーが必要です。他のブラウザーで失敗した場合は、Internet Explorer に切り替えることができます。

## 問題: オンプレミス SQL Server に接続できない 
ゲートウェイがインストールされているコンピューターから SQL Server に接続できることを確認します。ゲートウェイがインストールされているコンピューターで、次のことを実行できます。

1. SQL Server がインストールされているコンピューターに Ping を送信します。または
2. SQL Server Management Studio (SSMS) を使用して、Azure ポータルで指定した資格情報で SQL Server のインスタンスに接続してみます。


## 問題: 入力スライスが PendingExecution または PendingValidation 状態のままになっている

このスライスは、さまざまな理由から **PendingExecution** または **PendingValidation** になります。一般的な理由の 1 つとして、パイプラインの最初のテーブルまたはデータセットの **availability** セクションで **waitOnExternal** プロパティが指定されていないことが挙げられます。Azure Data Factory の範囲外で生成されるデータセットの場合、**availability** セクションで、**waitOnExternal** プロパティによるマーキングが必要です。これは、データが外部データであり、データ ファクトリ内のパイプラインでサポートされていないことを示します。それぞれのストアでデータが使用可能になると、データ スライスは **Ready** とマーキングされます。

**waitOnExternal** プロパティの使用方法については、次の例を参照してください。既定値が使用されるように、このセクションでプロパティの値を設定せずに **waitOnExternal {}** を指定できます。

このプロパティの詳細については、[JSON スクリプティング リファレンス][json-scripting-reference]のトピック「テーブル」を参照してください。
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 このエラーを解決するには、入力テーブルの JSON 定義に **waitOnExternal** セクションを追加し、テーブルをもう一度作成します。

## 問題: ハイブリッド コピー操作に失敗する
詳細情報:

1. ゲートウェイがインストールされているコンピューターで Data Management Gateway Configuration Manager を起動します。**[ゲートウェイ名]** が **Azure ポータル**の論理ゲートウェイ名に設定されており、**[ゲートウェイ キーの状態]** が **[登録済み]**、**[サービスの状態]** が **[開始]** になっていることを確認します。 
2. **イベント ビューアー**を起動します。**[アプリケーションとサービス ログ]** を展開し、**[Data Management Gateway]** をクリックします。Data Management Gateway に関連するエラーがあるか確認します。 

## 問題: オンデマンドの HDInsight プロビジョニングがエラーで失敗する

種類が HDInsightOnDemandLinkedService のリンクされたサービスを使用する場合、Azure BLOB ストレージを指す linkedServiceName を指定する必要があります。このストレージ アカウントを使用して、オンデマンド HDInsight クラスターのすべてのログとサポート ファイルをコピーします。HDInsight でオンデマンド プロビジョニングを実行するアクティビティは、次のエラーで失敗することがあります。

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

通常このエラーは、linkedServiceName で指定されたストレージ アカウントの場所が、HDInsight のプロビジョニングが行われているのと同じデータ センターの場所でないことを示します。たとえば、Azure Data Factory の場所が米国西部で、HDInsight のオンデマンド プロビジョニングも米国西部で実行するとしても、Azure BLOB ストレージ アカウントの場所が米国東部に設定されていると、オンデマンド プロビジョニングは失敗します。

さらに、2 つ目の JSON プロパティ additionalLinkedServiceNames では、オンデマンド HDInsight に追加のストレージ アカウントを指定できます。これらの追加のリンクされたストレージ アカウントは HDInsight クラスターと同じ場所に存在する必要があります。そうでないと、同じエラーで失敗します。



## 問題: カスタム アクティビティが失敗する
Azure Data Factory でカスタム アクティビティ (パイプライン アクティビティの種類が CustomActivity) を使用する場合、カスタム アプリケーションは、所定のリンクされたサービスから HDInsight に対して、マップのみのストリーミング MapReduce ジョブとして実行されます。

カスタム アクティビティを実行すると、Azure Data Factory が HDInsight クラスターからその出力をキャプチャし、Azure BLOB ストレージ アカウントの*adfjobs* ストレージ コンテナーに保存できます。エラーが発生した場合は、エラーが発生した後に出力テキスト ファイル **stderr** からテキストを読み取ることができます。このファイルは、Web ブラウザーで開いた Azure ポータルからアクセスして読み取ることができます。またはストレージ エクスプローラー ツールを使用して、Azure BLOB ストレージのストレージ コンテナーに保存されているファイルに直接アクセスできます。

特定のカスタム アクティビティのログを列挙して読み取るには、このページの後半で紹介するチュートリアルのいずれかの手順に従います。概要:

1.  Azure ポータルで Data Factory を**参照**して場所を特定します。
2.  **[ダイアグラム]** ボタンを使用してデータ ファクトリのダイアグラムを表示し、**[データセット]** テーブルをクリックすると、カスタム アクティビティを持つ特定の**パイプライン**が表示されます。 
3.  **[テーブル]** ブレードで、調査対象期間の **[問題のあるスライス]** にある対象のスライスをクリックします。
4.  詳しい情報が示された **[データ スライス]** ブレードが表示され、そのスライスの**アクティビティの実行**が一覧表示されます。一覧の**アクティビティ**をクリックします。 
5.  **[アクティビティの実行の詳細]** ブレードが表示されます。このブレードの中央には、**エラー メッセージ**が表示されます。ブレードの下部には、そのアクティビティの実行に関連するいくつかの**ログ ファイル**が一覧表示されます。
	- Logs/system-0.log
	- 状態
	- Status/exit
	- Status/stderr
	- Status/stdout

6. 一覧の先頭にある**ログ ファイル**の項目をクリックすると、そのログが新しいブレードに読みやすいフルテキスト表示で開きます。各ログをクリックして、そのログのテキストを確認します。テキスト ビューアー ブレードが開きます。**[ダウンロード]** ボタンをクリックしてテキスト ファイルをダウンロードし、オフラインで表示することもできます。

カスタム アクティビティの**一般的なエラー** の 1 つは、終了コード "1" でのパッケージの実行の失敗です。詳細については、"wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr" を参照してください。

この種類のエラーの詳細を確認するには、**stderr** ファイルを開きます。このファイルで確認できる一般的なエラーの 1 つは、次のようなタイムアウト状態です: INFO mapreduce.Job: Task Id : attempt_1424212573646_0168_m_000000_0, Status : FAILED AttemptID:attempt_1424212573646_0168_m_000000_0 Timed out after 600 secs

たとえば、30 分間以上にわたり、このジョブを 3 回再試行すると、同じエラーが複数回表示されます。

このタイムアウト エラーは、600 秒 (10 分) のタイムアウトが発生したことを示します。これは通常、カスタム .Net アプリケーションから、ステータスの更新情報が 10 分間発行されていないことを意味します。アプリケーションが何らかの操作の待機中に長時間にわたって応答を停止した場合、アプリケーションがそのまま待機し続けたり、Azure Data Factory パイプラインの遅延を招いたりするのを防ぐために、10 分のタイムアウトが安全なメカニズムとなります。

このタイムアウトは、カスタム アクティビティにリンクされている HDInsight クラスターの構成に由来します。設定は **mapred.task.timeout** です。この設定の既定値は、Apache の既定の設定 (http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml) に記載されているとおり、600,000 ミリ秒です。

この既定値は、HDInsight プロビジョニング クラスターのプロビジョニング時に既定値を変更することでオーバーライドできます。Azure Data Factory と、**HDInsight オンデマンド**のリンクされたサービスを使用する場合、JSON プロパティ HDInsightOnDemandLinkedService の近くに JSON プロパティを追加できます。たとえば、この JSON プロパティを使用して、値を 20 分に増やすことができます。
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

これらの MapReduce 構成プロパティを編集するための詳細なコンテキストと完全な JSON の例については、MSDN のドキュメント (https://msdn.microsoft.com/library/azure/dn893526.aspx) の例 3 を参照してください。

## 問題: 登録済みのリソース プロバイダーが見つからないことを示すエラー 400 (無効な要求) により、PowerShell の要求に失敗する

2015 年 3 月 10 日の時点で、Azure Data Factory PowerShell 初期プライベート プレビュー バージョン 2014-05-01-preview、2014-07-01-preview、2014-08-01-preview は廃止されます。この URL (http://go.microsoft.com/?linkid=9811175&clcid=0x409) からのダウンロードなど、Azure PowerShell のダウンロードの一部として追加された、ADF コマンドレットの最新バージョンを使用することをお勧めします。

Azure PowerShell SDK の廃止されたバージョンを使用すると、次のエラーが表示される場合があります。

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a>チュートリアル: データのコピーで発生するエラーのトラブルシューティング
このチュートリアルでは、「Data Factory を使ってみる」の記事にあるチュートリアルでエラーを発生させ、Azure ポータルを使用してエラーのトラブルシューティングを行う方法を学びます。

### 前提条件
1. 記事「[Azure Data Factory を使ってみる][adfgetstarted]」にあるチュートリアルを最後まで実施します。
2. **ADFTutorialDataFactory** が Azure SQL Database 内の **emp** テーブルでデータを生成していることを確認します。  
3. Azure SQL Database から **emp** テーブルを削除 (**drop table emp**) します。これにより、エラーが発生します。
4. **Azure PowerShell** で以下のコマンドを実行し、**emp** テーブルにデータの書き込みを試みるよう、パイプラインのアクティブ期間を更新しても、このテーブルは存在しません。

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	**StartDateTime** の値を現在の日付に置き換え、**EndDateTime** の値を翌日の日付に置き換えます。


### Azure プレビュー ポータルを使用して、エラーのトラブルシューティングを行う

1.	[Azure プレビュー ポータル][azure-preview-portal]にログインします。 
2.	**スタート画面**で **[ADFTutorialDataFactory]** をクリックします。**スタート画面**にデータ ファクトリのリンクが表示されていない場合は、**[参照]** ハブ、**[すべて]** の順にクリックします。**[参照]** ブレードの **[データ ファクトリ…]** をクリックし、**[ADFTutorialDataFactory]** をクリックします。
3.	**[データセット]** タイルに **[エラーあり]** が表示されていることに注目してください。**[エラーあり]** をクリックします。**[エラーありデータセット]** ブレードが表示されます。

	![［エラーあり］ リンクがある Data Factory][image-data-factory-troubleshoot-with-error-link]

4. **[エラーありデータセット]** ブレードで **[EmpSQLTable]** をクリックして **[テーブル]** ブレードを表示します。

	![[エラーありデータセット] ブレード][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. **[テーブル]** ブレードに問題のあるスライスが表示されます。たとえば、ブレード下部にある **[問題のあるスライス]** リスト内の、エラーのあるスライスです。また、**[最近のスライス]** リストで最近エラーが発生したスライスを確認することもできます。**[問題のあるスライス]** リスト内のスライスをクリックします。

	![問題のあるスライスを表示している [テーブル] ブレード][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	**[問題のあるスライス]** (特定の問題ではない) をクリックすると、**[データ スライス]** ブレードが表示されるので、**[特定の問題のあるスライス]** をクリックして選択したデータ スライスの **[データ スライス]** スライドを表示します。

6. **EmpSQLTable** の **[データ スライス]** ブレードには、ブレード下部のリスト内のスライスに対して、すべての **[アクティビティの実行]** が表示されています。リスト内の [失敗] になっている **[アクティビティの実行]** をクリックします。

	![アクティビティの実行を表示しているデータ スライス][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. 選択したアクティビティの実行に対する **[アクティビティの実行の詳細]** ブレードに、エラーの詳細が表示されます。このシナリオでは、"**Invalid object name ‘emp‘**" と表示されます。

	![エラーが発生しているアクティビティの実行の詳細][image-data-factory-troubleshoot-activity-run-with-error]

この問題を解決するには、「[Data Factory を使ってみる][adfgetstarted]」の記事にある SQL スクリプトを使用して **emp** テーブルを作成します。


### Azure PowerShell コマンドレットを使用して、エラーのトラブルシューティングを行う
1.	**Azure PowerShell** を起動します。 
2.	Data Factory コマンドレットは **AzureResourceManager** モードでのみ使用できるので、このモードに切り替えます。

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice コマンドを実行してスライスとその状態を確認します。[状態] が [失敗] になっているスライスが表示されるはずです。

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	**StartDateTime** を **Set-AzureDataFactoryPipelineActivePeriod** 用に指定した StartDateTime の値に置き換えます。

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	出力結果のうち、問題のあるスライス (**[状態]** が **[失敗]** になっているスライス) の **[開始]** の日時をメモしてください。 
4. **Get-AzureDataFactoryRun** コマンドレットを実行して、スライスのアクティビティの実行について詳細を取得します。
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	**StartDateTime** の値は、前の手順でメモしたエラーまたは問題のあるスライスの [開始] の日時です。日時は二重引用符で囲む必要があります。
5. エラーの詳細を含む (以下のような) 出力結果が表示されます。

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> チュートリアル: Hive/Pig 処理で発生するエラーのトラブルシューティング
このチュートリアルでは、Azure プレビュー ポータルと Azure PowerShell の両方を使用して、Hive/Pig 処理で発生するエラーのトラブルシューティングを行う手順を説明します。


### チュートリアル: Azure ポータルを使用して Pig/Hive 処理で発生するエラーのトラブルシューティングを行う
このシナリオでは、HDInsight クラスターでの Hive 処理で発生するエラーが原因で、データ セットがエラー状態になっています。

1. **[Data Factory]** のホーム ページから **[データセット]** タイル上の **[エラーあり]** をクリックします。

	![[データセット] タイルの ［エラーあり］ リンク][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. **[エラーありデータセット]** ブレードで、関心のある **テーブル** をクリックします。

	![[エラーありデータセット] ブレード][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. **[テーブル]** ブレードで、**[状態]** が **[失敗]** になっている **[問題のあるスライス]** をクリックします。

	![問題のあるスライスを含むテーブル][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. **[データ スライス]** ブレードで、失敗している **[アクティビティの実行]** をクリックします。

	![実行が失敗しているデータ スライス][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. **[アクティビティの実行の詳細]** ブレードでは、HDInsight 処理に関連するファイルをダウンロードできます。**[Status/stderr]** の **[ダウンロード]** をクリックし、エラーの詳細を含むエラー ログ ファイルをダウンロードします。

	![［ダウンロード］ リンクがあるアクティビティの実行の詳細][image-data-factory-troubleshoot-activity-run-details]

    
### チュートリアル: Azure PowerShell を使用して Pig/Hive 処理で発生するエラーのトラブルシューティングを行う
1.	**Azure PowerShell** を起動します。 
2.	Data Factory コマンドレットは **AzureResourceManager** モードでのみ使用できるので、このモードに切り替えます。

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice コマンドを実行してスライスとその状態を確認します。[状態] が [失敗] になっているスライスが表示されるはずです。

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	**StartDateTime** を **Set-AzureDataFactoryPipelineActivePeriod** 用に指定した StartDateTime の値に置き換えます。

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	出力結果のうち、問題のあるスライス (**[状態]** が **[失敗]** になっているスライス) の **[開始]** の日時をメモしてください。 
4. **Get-AzureDataFactoryRun** コマンドレットを実行して、スライスのアクティビティの実行について詳細を取得します。
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	**StartDateTime** の値は、前の手順でメモしたエラーまたは問題のあるスライスの [開始] の日時です。日時は二重引用符で囲む必要があります。
5. エラーの詳細を含む (以下のような) 出力結果が表示されます。

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. 上記出力結果の Id 値を使用して **Save-AzureDataFactoryLog** コマンドレットを実行し、同コマンドレットの **-DownloadLogs** オプションを使用してログ ファイルをダウンロードできます。



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

<!---HONumber=July15_HO4-->