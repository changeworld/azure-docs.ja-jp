<properties title="Troubleshoot Azure Data Factory issues" pageTitle="Azure Data Factory のトラブルシューティング" description="Azure データ ファクトリの使用に関する問題のトラブルシューティングを行う方法について説明します。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Data Factory のトラブルシューティング
Azure ポータル (または) Azure PowerShell コマンドレットを使用して Azure Data Factory の問題についてトラブルシューティングを行うことができます。このトピックには、Azure ポータルを使用し、Data Factory で発生するエラーをすばやく解決する方法について学べるチュートリアルが含まれています。 

## この記事の内容

- [データのコピーで発生するエラーのトラブルシューティング](#copywalkthrough)
- [Hive/Pig 処理で発生するエラーのトラブルシューティング](#pighivewalkthrough)

## <a name="copywalkthrough"></a>データのコピーで発生するエラーのトラブルシューティング
このチュートリアルでは、「Data Factory を使ってみる」の記事にあるチュートリアルでエラーを発生させ、Azure ポータルを使用してエラーのトラブルシューティングを行う方法を学びます。

### 前提条件
1. 「[Azure Data Factory を使ってみる][adfgetstarted]」の記事にあるチュートリアルを最後まで実施します。
2. **ADFTutorialDataFactory** が Azure SQL Database 内の **emp** テーブルでデータを生成していることを確認します。  
3. Azure SQL Database から **emp** テーブルを削除 (**drop table emp**) します。これにより、エラーが発生します。
4. **Azure PowerShell** で以下のコマンドを実行し、もう存在しない **emp** テーブルにデータの書き込みを試みるよう、パイプラインのアクティブ期間を更新します。

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] <b>StartDateTime</b> の値を現在の日付に置き換え、<b>EndDateTime</b> の値を翌日の日付に置き換えます。 


### Azure プレビュー ポータルを使用して、エラーのトラブルシューティングを行う

1.	[Azure プレビュー ポータル][azure-preview-portal]にログインします。 
2.	**スタート画面**で **[ADFTutorialDataFactory]** をクリックします。**スタート画面**にデータ ファクトリのリンクが表示されていない場合は、**[参照]** ハブをクリックし、**[すべて]** をクリックします。**[参照]** ブレードの **[データ ファクトリ]** をクリックし、**[ADFTutorialDataFactory]** をクリックします。
3.	**[データセット]** タイルに **[エラーあり]** が表示されていることに注目してください。**[エラーあり]** をクリックします。**[エラーありデータセット]** ブレードが表示されるはずです。

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. **[エラーありデータセット]** ブレードで **[EmpSQLTable]** をクリックして **[テーブル]** ブレードを表示させます。	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. **[テーブル]** ブレードに問題のあるスライスが表示されるはずです。たとえば、ブレード下部にある **[問題のあるスライス]** リスト内の、エラーのあるスライスです。また、**[最近のスライス]** リストで最近エラーが発生したスライスを確認することもできます。**[問題のあるスライス]** リスト内のスライスをクリックします。 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	**[問題のあるスライス]** (特定の問題ではない) をクリックすると、**[データ スライス]** ブレードが表示されるので、**[特定の問題のあるスライス]** をクリックして選択したデータ スライスの**[データ スライス]** スライドを表示させます。

6. **EmpSQLTable** の **[データ スライス]** ブレードには、ブレード下部のリスト内のスライスに対して、すべての**[アクティビティの実行]** が表示されています。リスト内の [失敗] になっている **[アクティビティの実行]** をクリックします。

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. 選択したアクティビティの実行に対する**[アクティビティの実行の詳細]** ブレードには、エラーの詳細が表示されているはずです。このシナリオ内には、**"Invalid object name 'emp'"** と表示されます。

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

この問題を解決するには、「[Data Factory を使ってみる][adfgetstarted]」の記事にある SQL スクリプトを使用して **emp** テーブルを作成します。


### Azure PowerShell コマンドレットを使用して、エラーのトラブルシューティングを行う
1.	**Azure PowerShell** を起動します。 
2.	Data Factory コマンドレットは **AzureResourceManager** モードでのみ使用できるので、このモードに切り替えます。

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice コマンドを実行してスライスとその状態を確認します。[状態] が [失敗] になっているスライスが表示されるはずです。	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] **StartDateTime** を **Set-AzureDataFactoryPipelineActivePeriod** 用に指定した StartDateTime の値に置き換えます。 

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

	**StartDateTime** の値は、前の手順でメモした エラーまたは問題のあるスライスの [開始] の日時です。日時は二重引用符で囲む必要があります。
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

 

## <a name="pighavewalkthrough"></a>Hive/Pig 処理で発生するエラーのトラブルシューティング
このチュートリアルでは、Azure プレビュー ポータルと Azure PowerShell の両方を使用して、Hive/Pig 処理で発生するエラーのトラブルシューティングを行う手順を説明します。 


### Azure Portal を使用して Pig/Hive 処理で発生するエラーのトラブルシューティングを行う
このシナリオでは、HDInsight クラスターでの Hive 処理で発生するエラーが原因で、データ セットがエラー状態になっています。

1. **[データ ファクトリ]** のホーム ページから**[データセット]** タイル上の**[エラーあり]**をクリックします。

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. **[エラーありデータセット]** ブレードで、興味のある **[テーブル]** をクリックします。

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. **[テーブル]** ブレードで、**[状態]** が **[失敗]** になっている **[問題のあるスライス]** をクリックします。

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. **[データ スライス]** ブレードで、失敗している **[アクティビティの実行]** をクリックします。

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. **[アクティビティの実行の詳細]** ブレードでは、HDInsight 処理に関連するファイルがダウンロードできます。**[状態/stderr]** の **[ダウンロード]** をクリックし、エラーの詳細を含むエラー ログ ファイルをダウンロードします。

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### Azure PowerShell を使用して Pig/Hive 処理で発生するエラーのトラブルシューティングを行う。
1.	**Azure PowerShell** を起動します。 
2.	Data Factory コマンドレットは **AzureResourceManager** モードでのみ使用できるので、このモードに切り替えます。

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice コマンドを実行してスライスとその状態を確認します。[状態] が [失敗] になっているスライスが表示されるはずです。	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] **StartDateTime** を **Set-AzureDataFactoryPipelineActivePeriod** 用に指定した StartDateTime の値に置き換えます。 

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

	**StartDateTime** の値は、前の手順でメモした エラーまたは問題のあるスライスの [開始] の日時です。日時は二重引用符で囲む必要があります。
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

## トラブルシューティングのヒント

### オンプレミス SQL Server に接続できない 
ゲートウェイがインストールされているコンピューターから SQL Server に接続できることを確認します。


1. SQL Server がインストールされているコンピューターに Ping を送信
2. ゲートウェイがインストールされているコンピューターで SQL Server Management Studio (SSMS) を使用し、Azure ポータルで指定した資格情報で SQL Server のインスタンスに接続できるか試してください。

### コピー操作が失敗する
1. ゲートウェイがインストールされているコンピューターの Data Management Gateway Configuraiton Manager を起動します。**ゲートウェイ名**が **Azure ポータル**の論理ゲートウェイ名に設定されており、**[ゲートウェイ キーの状態]** が **[登録済み]** かつ **[サービスの状態]** が **[開始]** になっていることを確認します。 
2. **イベント ビューアー**を起動します。**アプリケーションとサービス ログ**を展開し、**[Data Management Gateway]** をクリックします。Data Management Gateway に関連するエラーがあるか確認します。 



## 関連項目

記事 | 説明
------ | ---------------
[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources] | この記事には、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[Data Factory で Pig と Hive を使用する][use-pig-and-hive-with-data-factory] | この記事には、HDInsight アクティビティを使用して hive/pig スクリプトを実行し、入力データを処理して出力データを生成する方法を説明したチュートリアルが記載されています。 
[チュートリアル: Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。 
[Azure PowerShell を使用した Azure Data Factory の監視と管理][monitor-manage-using-powershell] | この記事では、Azure PowerShell コマンドレットを使用して Azure Data Factory を監視する方法について説明しています。 
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。エラーを発生させて (Azure SQL データベースのテーブルを削除する)、ADFTutorialDataFactory でこの記事のチュートリアルを試すことができます。 
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 
[Azure Data Factory Cmdlet Reference (Azure Data Factory コマンドレット リファレンス)][cmdlet-reference] | このリファレンスには、すべての **Data Factory コマンドレット**に関する詳細が記載されています。

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
