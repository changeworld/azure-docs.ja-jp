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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Data Factory のトラブルシューティング
Azure ポータル (または) Azure PowerShell コマンドレットを使用して Azure Data Factory の問題についてトラブルシューティングを行うことができます。このトピックには、Azure ポータルを使用し、Data Factory で発生するエラーをすばやく解決する方法について学べるチュートリアルが含まれています。

## 問題: データの工場出荷時のコマンドレットを実行できません
この問題を解決するに Azure のモードを切り替える **AzureResourceManager**:

起動 **Azure PowerShell** に切り替えるには、次のコマンドを実行して、 **AzureResourceManager** モード。Azure のデータのファクトリのコマンドレットは、 **AzureResourceManager** モード。

         switch-azuremode AzureResourceManager

## 承認されていないエラーをデータの工場出荷時のコマンドレットを実行している場合は、問題点:
おそらく使用しない、右の Azure アカウントまたはサブスクリプション、Azure PowerShell を使用します。次のコマンドレットを使用すると、右の Azure アカウントと、Azure PowerShell を使用するサブスクリプションを選択できます。

1. 追加の AzureAccount - を使用して、適切なユーザー ID とパスワード
2. Get-azuresubscription - アカウントのすべてのサブスクリプションを表示します。 
3. Select-azuresubscription <subscription name> -右のサブスクリプションを選択します。1 つの Azure プレビュー ポータルでのデータのファクトリの作成に使用すると、同じを使用します。

## データ ゲートウェイ Express セットアップ Azure ポータルからの起動に失敗する問題点。
Express をセットアップするため、データのゲートウェイには、Internet Explorer や Microsoft ClickOnce 互換性のある web ブラウザーが必要です。できますが、Express のセットアップを開始する場合

1. その他のブラウザーとが失敗した場合は、Internet Explorer に切り替えます。または
2. ポータルでは、同じのブレードに表示される手動セットアップ」のリンクを使用して、インストールのインストールを行うし、画面に収録されているキーをコピーし、準備ができたら、Data Management Gateway 構成を貼り付けます。を起動しない場合は"Microsoft Data Management Gateway"の [スタート] メニューを確認し、を起動するときに、キーに貼り付けます。 


## Azure ポータルからの資格情報マネージャーの起動に失敗する問題点。
セットアップする場合またはセキュリティを保証する更新プログラム、Azure ポータルで、資格情報マネージャー アプリケーションを使用して SQL Server のリンクされているサービスが起動します。これは、Internet Explorer や Microsoft ClickOnce 互換性のある web ブラウザーが必要です。その他のブラウザーが失敗した場合は、Internet Explorer に切り替えることができます。

## 内部設置型 SQL Server への接続に失敗する問題点。 
ゲートウェイがインストールされているコンピューターから SQL Server に接続できることを確認します。ゲートウェイがインストールされているコンピューターですることができます。

1. SQL Server がインストールされているコンピューターを ping します。または
2. SQL Server Management Studio (SSMS) を使用して、Azure ポータルで指定した資格情報を使用して SQL Server のインスタンスに接続してみてください。


## 問題: 入力スライスが PendingExecution または PendingValidation 状態でこれまで

スライスにある **PendingExecution** または **PendingValidation** さまざまな理由と、一般的な理由の 1 つの状態です、 **waitOnExternal** でプロパティが指定されていない、 **可用性** 、パイプラインで最初のテーブルとデータセットのセクションです。Azure のデータのファクトリのスコープ外で生成される任意のデータセットを指定してください **waitOnExternal** のプロパティの [ **可用性** セクションです。これは、データが外部としない、データ、ファクトリ内の任意のパイプラインによってサポートを示します。データ スライスとマークされて **準備ができて** 、それぞれのストアにデータが使用可能です。

使用方法を次の例を参照してください、 **waitOnExternal** プロパティです。指定できます **waitOnExternal {}** せず、既定値が使用できるように、このセクションでのプロパティの値を設定します。

テーブルのトピックを参照してください。 [JSON スクリプト参照][json-scripting-reference] の詳細についてはこのプロパティ。
	
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

 エラーを解決するのには次のように追加します。、 **waitOnExternal** 入力テーブルの JSON 定義にセクションと、テーブルを再作成します。

## 問題: ハイブリッド コピー操作が失敗します。
詳細を参照してください。

1. ゲートウェイがインストールされているコンピューターで Data Management Gateway の構成マネージャーを起動します。いることを確認、 **ゲートウェイ名** 上に、ゲートウェイの論理名に設定されている、 **Azure ポータル**, 、**ゲートウェイのキーの状態** は **登録されている** と **サービスの状態** は **開始**です。 
2. 起動 **イベント ビューアー**です。展開 **アプリケーションとサービス ログ** ] をクリック **Data Management Gateway**です。Data Management Gateway に関連するエラーがあるか確認します。 

## 問題: でオンデマンド HDInsight のプロビジョニングがエラーで失敗します。

型 HDInsightOnDemandLinkedService のリンクされているサービスを使用する場合を Azure Blob ストレージを指す linkedServiceName を指定する必要があります。このストレージ アカウントは、ログと、オンデマンドでの HDInsight クラスターのサポート ファイルをコピーするために使用します。場合があります、アクティビティをオンデマンドでの HDInsight でのプロビジョニングを行うことがあります、次のエラーで失敗します。

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

このエラーは、通常、linkedServiceName で指定されたストレージ アカウントの場所が、同じデータ センターの場所としてここで、HDInsight のプロビジョニングが行われているされていないことを示します。たとえば場合は、Azure のデータのファクトリの場所が米国西部、およびオンデマンドでの HDInsight のプロビジョニングで行われます米国西部ですが Azure blob ストレージ アカウントの場所が米国東部に設定されて、オンデマンドでのプロビジョニングは失敗します。

さらがありますが、2 つ目の JSON プロパティ additionalLinkedServiceNames にオンデマンドでの HDInsight に、追加のストレージ アカウントを指定できます。これらの追加のリンクされたストレージ アカウントは、HDInsight クラスターと同じ場所にする必要がありますか、同じエラーで失敗します。



## 問題: カスタム アクティビティが失敗します。
を Azure のデータのファクトリ (パイプライン アクティビティ型 [customactivity]) でカスタム アクティビティを使用する場合、カスタム アプリケーションを実行、指定されたリンクされているサービスから HDInsight へマップとしてのみ MapReduce ジョブをストリーミングします。

Azure のデータのファクトリは、HDInsight クラスターから、その出力をキャプチャして保存できるようにする、カスタム アクティビティを実行すると、 *adfjobs* 、Azure Blob ストレージ アカウントのストレージ コンテナー。エラーの場合に、テキストを読み取ることができます **stderr** 、障害が発生した後、出力テキスト ファイルです。ファイルは、アクセス可能であり、Azure ポータルから自体または Azure Blob ストレージには、ストレージ コンテナーに直接保持ファイルにアクセスする記憶域エクスプ ローラー ツールを使用して、web ブラウザーで読み取り可能です。

列挙し、特定のカスタム アクティビティのログを読み取りは次の図に示しているチュートリアルでは、いずれかの後でこのページにします。概要。

1.  Azure ポータルで **参照** をデータのファクトリを検索します。
2.  使用して、 **ダイアグラム** 、データのファクトリ ダイアグラムを表示するボタンをクリックして、 **データセット** 、特定の次の表を **パイプライン** 、カスタム アクティビティを持ちます。 
3.   **テーブル** ブレード、興味のスライスをクリックして、 **問題スライス** 調査する時間帯のです。
4.  詳細 **データ スライス** ブレードが表示され、複数を一覧できます **活動が実行される** 、スライスします。クリックして、 **アクティビティ** をクリックします。 
5.   **アクティビティの実行の詳細** ブレードが表示されます。これには、一覧、 **エラー メッセージ** 、ブレード、およびいくつかの途中で **ログ ファイル** そのアクティビティの実行に関連しているブレードの下部に一覧表示します。
	- ログ/システム-0.log
	- 状態
	- 状態と終了
	- 状態と stderr
	- ステータス/stdout

6. 最初のクリックして **ログ ファイル** テキスト全体を読むの表示で新しいブレードで、リスト、および、ログ内の項目が開きます。1 つずつ] をクリックして、各ログのテキストを確認します。テキスト ビューアー ブレードが開きます。クリックして、 **ダウンロード** (省略可能) をオフラインでのテキスト ファイルをダウンロードするボタンをクリックします。

1 つ **一般的なエラー** カスタム アクティビティからはパッケージの実行で失敗しました終了コード '1' です。参照してください 'wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/状態/stderr' の詳細。

この種類のエラーの詳細を確認するには開きます、 **stderr** ファイルです。1 つの一般的なエラーが表示されるは次のように、タイムアウト状態: mapreduce の情報です。ジョブを: タスク Id: attempt_1424212573646_0168_m_000000_0、状態: 失敗した AttemptID:attempt_1424212573646_0168_m_000000_0 がタイムアウトになりました 600 秒後に

このエラーが、ジョブが 3 回再試行など、30 個以上の分にわたっている場合、複数回表示されます。

このタイムアウト エラーが 600 秒 (10 分) を示すタイムアウトの問題が発生しました。これは通常、カスタム .Net アプリケーションに 10 分用のステータス更新プログラムを発行していないを意味します。場合は、アプリケーションはぶら下がりまたはタイムアウトは、安全性のメカニズムを永久に待機していると、Azure のデータのファクトリのパイプラインを遅らせることを防ぐために 10 分が長すぎる場合、何かで待機を停止します。

このタイムアウトは、カスタム アクティビティにリンクされている HDInsight クラスターの構成で発生します。設定が **mapred.task.timeout**, 、Apache 既定の設定は、ここに記載されているように 600000 (ミリ秒単位) を既定値します。 http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

オーバーライドこの既定値を HDInsight のプロビジョニングのクラスターのプロビジョニング時に、既定の設定を変更します。Azure のデータのファクトリを使用する場合と **HDInsight オンデマンド** サービスでは、リンクされている、HDInsightOnDemandLinkedService JSON プロパティ近く JSON プロパティを追加することができます。次の例では、この JSON プロパティを使用して、20 分に値を大きくことができます。
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

詳細なコンテキストをこれらのマップを編集する JSON の例については、MSDN のドキュメントの構成プロパティの参照例 3 を減らす https://msdn.microsoft.com/library/azure/dn893526.aspx

## 問題: PowerShell 要求が失敗したエラーのエラーで 400 正しくない要求「登録済みのリソース プロバイダーを... に見つかった」

2015 年 3 月 10 日の時点では、Azure のデータのファクトリ PowerShell 初期プライベート プレビュー バージョン 2014年-05-01-プレビュー、2014年-07-01-プレビュー、および 2014年-08-01-preview は中止されます。これで、Azure PowerShell をダウンロード、この URL からのダウンロードなどの一部である、ADF のコマンドレットの最新バージョンを使用することをお勧め http://go.microsoft.com/?linkid=9811175&clcid=0x409

Azure PowerShell SDK の提供が中止されたバージョンを使用する場合、次のエラーが表示される場合があります。

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


## <a name="copywalkthrough"></a> チュートリアル: データのコピーでエラーのトラブルシューティング
このチュートリアルでは、「Data Factory を使ってみる」の記事にあるチュートリアルでエラーを発生させ、Azure ポータルを使用してエラーのトラブルシューティングを行う方法を学びます。

### 前提条件
1. チュートリアルを完了、 [Azure のデータのファクトリを使い始める][adfgetstarted] 記事です。
2. いることを確認、 **ADFTutorialDataFactory** 内のデータの生成、 **emp** Azure SQL データベースのテーブルです。  
3. これで、削除、 **emp** テーブル (* * テーブルのドロップ emp * *)、Azure SQL データベースからです。これにより、エラーが発生します。
4. 次のコマンドを実行、 **Azure PowerShell** にデータを書き込むしようとするように、パイプラインの有効期間を更新する、 **emp** テーブルは既に存在しません。

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]置き換える <b>StartDateTime</b> 、現在の日付値と <b>EndDateTime</b> で、次の日の値です。


### Azure プレビュー ポータルを使用して、エラーのトラブルシューティングを行う

1.	ログイン [Azure プレビュー ポータル][azure-preview-portal]です。 
2.	クリックして **ADFTutorialDataFactory** から、 **スタート ボード**です。リンクをデータのファクトリが表示されない場合、 **スタート ボード**, 、] をクリックして **参照** ハブと] をクリック **すべて**です。をクリックして **データ ファクトリ...** で、 **参照** ] をクリックしてブレード、 **ADFTutorialDataFactory**です。
3.	「することに注意してください **エラーで** 上、 **データセット** 並べて表示します。クリックして **エラー**です。表示されます **エラーによるデータセットの** ブレードします。

	![データの工場出荷時のエラー リンクを含む][image-data-factory-troubleshoot-with-error-link]

4.  **データセット** エラーのブレードをクリックして **EmpSQLTable** を表示するには、 **テーブル** ブレードします。

	![エラーのブレードを持つデータセット][image-data-factory-troubleshoot-datasets-with-errors-blade]

5.  **テーブル** ブレードで、問題のスライスではエラーでスライスなどが表示されます、 **問題スライス** 下部にあるリストです。エラー、最近のスライスを表示することもできます、 **最近使用したスライス** ] ボックスの一覧です。スライスをクリックして、 **問題スライス** ] ボックスの一覧です。

	![問題のスライスを使用してテーブル ブレード][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	をクリックすると **問題スライス** 表示されます (特定問題の場合) ではなく、 **データ スライス** ブレードをクリック、 **スライスの特定の問題** を参照してください、 **データ スライス** 、選択したデータのスライスのスライドします。

6.  **データ スライス** のブレードに **EmpSQLTable**, 、すべてを参照してください **アクティビティの実行** の下部にある一覧でスライスします。クリックして、 **アクティビティの実行** 失敗した一覧からです。

	![データ スライスのブレードをアクティブ状態の実行][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7.  **アクティビティの実行の詳細** ブレード、アクティビティの実行は、選択した、エラーの詳細が表示されます。このシナリオで表示します。 **無効なオブジェクト名 'emp'**です。

	![アクティビティの実行の詳細をエラーで][image-data-factory-troubleshoot-activity-run-with-error]

この問題を解決するのには次のように作成します。、 **emp** からスクリプトを、SQL を使用してテーブル [データ ファクトリの][adfgetstarted] 記事です。


### Azure PowerShell コマンドレットを使用して、エラーのトラブルシューティングを行う
1.	起動 **Azure PowerShell**です。 
2.	切り替える **AzureResourceManager** モード、データの工場出荷時のコマンドレットとは、このモードでのみ使用できます。

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice コマンドを実行してスライスとその状態を確認します。[状態] が [失敗] になっているスライスが表示されるはずです。

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]置き換える **StartDateTime** に対して指定した値、StartDateTime 持つ、 **セット AzureDataFactoryPipelineActivePeriod**です。

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	注意してください、 **開始** 、問題のスライスの時間 (でスライス **状態** に設定 **失敗**) 出力します。 
4. これで、実行、 **Get AzureDataFactoryRun** コマンドレットは、スライスを実行するアクティビティに関する情報を取得します。
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	値 **StartDateTime** は、前の手順からメモしたエラーおよび問題のスライスの開始時刻。日時は二重引用符で囲む必要があります。
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

 

## <a name="pighivewalkthrough"></a> チュートリアル: Hive と Pig 処理でエラーのトラブルシューティング
このチュートリアルでは、Azure プレビュー ポータルと Azure PowerShell の両方を使用して、Hive/Pig 処理で発生するエラーのトラブルシューティングを行う手順を説明します。


### チュートリアル: Azure ポータルを使用する Pig と Hive の処理でエラーのトラブルシューティングを行う
このシナリオでは、HDInsight クラスターでの Hive 処理で発生するエラーが原因で、データ セットがエラー状態になっています。

1. をクリックして **エラーで** で **データセット** タイルで、 **データ ファクトリ** ホーム ページです。

	![[データセット] タイルのエラー リンクを含む][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2.  **エラーによるデータセットの** ブレードで] をクリックして、 **テーブル** 興味のあることです。

	![エラーのブレードを持つデータセット][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3.  **テーブル** ブレードをクリックして、 **問題スライス** で **状態** に設定 **失敗**です。

	![問題のスライスを持つテーブル][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4.  **データ スライス** ブレードで] をクリックして、 **アクティビティ実行** 失敗しました。

	![失敗した実行でのデータ スライス][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5.  **アクティビティの実行の詳細** ブレードで、HDInsight の処理に関連付けられているファイルをダウンロードすることができます。をクリックして **ダウンロード** の **状態/stderr** 、エラーに関する詳細を含む、エラー ログ ファイルをダウンロードします。

	![アクティビティの実行の詳細とダウンロード リンク][image-data-factory-troubleshoot-activity-run-details]

    
### チュートリアル: Azure PowerShell を使用する Pig と Hive の処理でエラーのトラブルシューティングを行う
1.	起動 **Azure PowerShell**です。 
2.	切り替える **AzureResourceManager** モード、データの工場出荷時のコマンドレットとは、このモードでのみ使用できます。

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice コマンドを実行してスライスとその状態を確認します。[状態] が [失敗] になっているスライスが表示されるはずです。

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]置き換える **StartDateTime** に対して指定した値、StartDateTime 持つ、 **セット AzureDataFactoryPipelineActivePeriod**です。

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	注意してください、 **開始** 、問題のスライスの時間 (でスライス **状態** に設定 **失敗**) 出力します。 
4. これで、実行、 **Get AzureDataFactoryRun** コマンドレットは、スライスを実行するアクティビティに関する情報を取得します。
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	値 **StartDateTime** は、前の手順からメモしたエラーおよび問題のスライスの開始時刻。日時は二重引用符で囲む必要があります。
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

6. 実行することができます **保存 AzureDataFactoryLog** コマンドレットの上記の出力を確認して、ログをダウンロードする、Id 値を持つファイルを使用して、 **- DownloadLogs** コマンドレットのオプションです。



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

<!---HONumber=GIT-SubDir--> 