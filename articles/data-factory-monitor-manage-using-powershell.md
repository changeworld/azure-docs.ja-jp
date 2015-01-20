<properties title="Monitor and manage Azure Data Factory using Azure PowerShell" pageTitle="Azure PowerShell を使用した Azure Data Factory の監視と管理" description="Azure PowerShell を使用して、作成した Azure データ ファクトリを監視および管理する方法について説明します。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Azure PowerShell を使用した Azure Data Factory の監視と管理
次の表は、Azure PowerShell を使用した Azure Data Factory の監視と管理に使用できるコマンドレットの一覧です。 

> [WACOM.NOTE] Data Factory コマンドレットに関する包括的なドキュメントについては、[Data Factory コマンドレット リファレンス][cmdlet-reference]を参照してください。 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
特定の Data Factory または指定されたリソース グループに含まれている Azure サブスクリプションのすべての Data Factory に関する情報を取得します。
 
###例 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

このコマンドは、リソース グループ ADFTutorialResourceGroup 内のすべての Data Factory を返します。
 
###例 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

このコマンドは、リソース グループ ADFTutorialResourceGroup に含まれた ADFTutorialDataFactory という Data Factory に関する詳細情報を返します。 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService ##
Get-AzureDataFactoryLinkedService コマンドレットは、Azure Data Factory 内の特定のリンクされたサービスやすべてのリンクされたサービスに関する情報を取得します。

### 例 1 ###

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
このコマンドは、ADFTutorialDataFactory という Azure Data Factory 内のすべてのリンクされたサービスに関する情報を返します。


DataFactoryName パラメーターと ResourceGroupName パラメーターの代わりに -DataFactory パラメーターを使用できます。このパラメーターを使用すると、リソース グループ名と Data Factory 名を 1 回入力するだけで、ResourceGroupName と DataFactoryName の両方をパラメーターとして受け取るすべてのコマンドレットに対して Data Factory オブジェクトをパラメーターとして使用できます。

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### 例 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

このコマンドは、ADFTutorialDataFactory という Azure Data Factory 内の MyBlobStore というリンクされたサービスに関する情報を返します。 

次に示すように、-ResourceGroup パラメーターと -DataFactoryName パラメーターの代わりに -DataFactory パラメーターを使用できます。 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
Get-AzureDataFactoryTable コマンドレットは、Azure Data Factory 内の特定のテーブルまたはすべてのテーブルに関する情報を取得します。 

### 例 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

このコマンドは、ADFTutorialDataFactory という Azure Data Factory 内のすべてのテーブルに関する情報を返します。

次に示すように、-ResourceGroup パラメーターと -DataFactoryName パラメーターの代わりに -DataFactory パラメーターを使用できます。 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### 例 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

このコマンドは、ADFTutorialDataFactory という Azure Data Factory 内の EmpTableFromBlob というテーブルに関する情報を返します。



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
Get-AzureDataFactoryPipeline コマンドレットは、Azure Data Factory 内の特定のパイプラインまたはすべてのパイプラインに関する情報を取得します。

### 例 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

このコマンドは、ADFTutorialDataFactory という Azure Data Factory 内のすべてのパイプラインに関する情報を返します。

### 例 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

ADFTutorialDataFactory という Azure Data Factory 内の ADFTutorialPipeline というパイプラインに関する情報を返します。

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
Get-AzureDataFactorySlice コマンドレットは、Azure Data Factory 内のテーブルで StartDateTime から EndDateTime までの間に生成されたすべてのスライスを取得します。ステータスが "Ready" のデータ スライスは、依存するスライスで使用する準備が整っています。

次の表は、スライスのすべてのステータスとその説明の一覧です。

<table border="1">	
	<tr>
		<th align="left">ステータス</th>
		<th align="left">説明</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>データ処理がまだ開始されていません。</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>データ処理は進行中です。</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>データ処理が完了し、データ スライスの準備が整っています。</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>スライス生成の実行が失敗しました。</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>スライスの処理をスキップします。</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>スライス生成の実行を再試行しています。</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>スライスのデータ処理がタイムアウトになりました。</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>データ スライスが処理前の検証ポリシーに対する検証を待機しています。</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>スライスの検証を再試行します。</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>スライスの検証に失敗しました。</td>
	</tr>

	<tr>
		<td>LongRetry</td>
		<td>テーブル JSON で LongRetry が指定されている場合にスライスがこのステータスになります。スライスに対する通常の再試行は失敗します。</td>
	</tr>

	<tr>
		<td>ValidationInProgress</td>
		<td>(テーブル JSON で定義されているポリシーに基づいて) スライスの検証が実行されています。</td>
	</tr>

</table>

各スライスに対して、さらに詳しいドリルダウンを実行し、Get-AzureDataFactoryRun コマンドレットと Save-AzureDataFactoryLog コマンドレットを使用してスライスを生成している実行の詳細を参照できます。

### 例

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

このコマンドは、2014-05-20T10:00:00 (GMT) 以降に生成された ADFTutorialDataFactory という Azure Data Factory に含まれている EmpSQLTable というテーブルのすべてのスライスを取得します。日付は、Set-AzureDataFactoryPipelineActivePeriod を実行したときに指定した開始日時に置き換えてください。

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

Get-AzureDataFactoryRun コマンドレットは、Azure Data Factory 内のテーブルのデータ スライスに関するすべての実行を取得します。Azure Data Factory 内のテーブルは、時間軸のスライスで構成されます。スライスの幅は、スケジュール (毎時/毎日) によって決まります。この実行とは、スライスの処理単位です。再試行する場合や、エラー発生時にスライスを再実行する場合に、1 つのスライスを 1 回以上実行できます。スライスは、開始時刻によって識別されます。そのため、Get-AzureDataFactoryRun コマンドレットには、Get-AzureDataFactorySlice コマンドレットの結果から取得したスライスの開始時刻を渡す必要があります。

たとえば、次のスライスの実行を取得するには、2015-04-02T20:00:00 を使用します。 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### 例

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

このコマンドは、2014 年 5 月 21 日午後 4 時 (GMT) に開始された ADFTutorialDataFactory という Azure Data Factory に含まれている EmpSQLTable テーブルのスライスのすべての実行を取得します。

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
Save-AzureDataFactoryLog コマンドレットは、Pig または Hive プロジェクトの Azure HDInsight 処理またはカスタム アクティビティに関連するログ ファイルをローカル ハード ドライブにダウンロードします。最初に、Get-AzureDataFactoryRun コマンドレットを実行して、データ スライスのアクティビティ実行の ID を取得します。次に、その ID を使用して、HDInsight クラスターに関連付けられたバイナリ ラージ オブジェクト (BLOB) ストレージからログ ファイルを取得します。 

**-DownloadLogs** パラメーターを指定しない場合は、コマンドレットは、ログ ファイルの場所だけを返します。 

出力ディレクトリ (**-Output **パラメーター) を指定せずに **-DownloadLogs** パラメーターを指定すると、ログ ファイルは既定の **Documents** フォルダーにダウンロードされます。 

出力フォルダー (**-Output**) を指定して **-DownloadLogs** パラメーターを指定すると、ログ ファイルは指定されたフォルダーにダウンロードされます。 


### 例 1
このコマンドは、ID が 841b77c9-d56c-48d1-99a3-8c16c3e77d39 で、アクティビティが ADF という名前のリソース グループに含まれている LogProcessingFactory という Data Factory 内のパイプラインに属するアクティビティ実行のログ ファイルを保存します。ログ ファイルは C:\Test フォルダーに保存されます。 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### 例 2
このコマンドは、Documents フォルダー (既定) にログ ファイルを保存します。


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### 例 3
このコマンドは、ログ ファイルの場所を返します。- DownloadLogs パラメーターは指定されていないことに注意してください。 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
Get-AzureDataFactoryGateway コマンドレットは、Azure Data Factory 内の特定のゲートウェイまたはすべてのゲートウェイに関する情報を取得します。内部設置型の SQL Server をリンクされたサービスとして Data Factory に追加するには、内部設置型コンピューター上にゲートウェイをインストールする必要があります。

### 例 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
このコマンドは、ADFTutorialDataFactory という Azure Data Factory 内のすべてのゲートウェイに関する情報を返します。

### 例 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

このコマンドは、ADFTutorialDataFactory という Azure Data Factory 内の ADFTutorialGateway というゲートウェイに関する情報を返します。

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
このコマンドレットは、パイプラインで処理されるデータ スライスの有効期間を設定します。Set-AzureDataFactorySliceStatus を使用する場合は、必ず、スライスの開始日と終了日をパイプラインの有効期間内にします。

パイプラインを作成したら、データ処理を実行する期間を指定できます。パイプラインの有効期間を指定することで、各 ADF テーブルに対して定義された Availability プロパティに基づいてデータ スライスが処理される期間を定義します。

### 例

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

このコマンドレットは、パイプライン ADFTutoiralPipeline によって処理されるデータ スライスの有効期間を 5/21/2014 4 PM GMT から 5/22/2014 4 PM GMT に設定します。

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
テーブルのスライスのステータスを設定します。スライスの開始日と終了日はパイプラインの有効期間内である必要があります。

### サポートされるステータスの値
テーブルの各データ スライスは、さまざまな段階を経て実行されます。これらの段階は、認証ポリシーが指定されているかどうかによって若干異なります。


- 認証ポリシーが指定されていない場合:PendingExecution -> InProgress -> Ready
- 認証ポリシーが指定されている場合:PendingExecution -> Pending Validation -> InProgress -> Ready

次の表には、表示される可能性のあるスライスのステータスの説明と、Set-AzureDataFactorySliceStatus を使用してステータスを設定できるかどうかを記載しています。

<table border="1">	
	<tr>
		<th>ステータス</th>
		<th>説明</th>
		<th>コマンドレットを使用して設定できるか</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>データ処理がまだ開始されていません。</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>データ処理は進行中です。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>データ処理が完了し、データ スライスの準備が整っています。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>スライス生成の実行が失敗しました。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>スライスの処理をスキップします。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>スライス生成の実行を再試行しています。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>データ処理がタイムアウトになりました。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>データ スライスが処理前の検証ポリシーに対する検証を待機しています。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>スライスの検証を再試行します。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>スライスの検証に失敗しました。</td>
		<td>N</td>
	</tr>
	</table>


### サポートされている値 - 更新の種類
Azure Data Factory の各テーブルでは、スライスのステータスを設定する際、ステータスの更新をテーブルのみに適用するか、影響を受けるすべてのスライスに反映するかを指定する必要があります。

<table border="1">	
	<tr>
		<th>更新の種類</th>
		<th>説明</th>
		<th>コマンドレットを使用して設定できるか</th>
	</tr>

	<tr>
		<td>Individual</td>
		<td>指定した時間範囲内のテーブルの各スライスに対してステータスを設定します。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>パイプライン内のアクティビティに対する入力テーブルとして使用されるテーブルとすべての依存 (アップストリーム) テーブルの各スライスに対してステータスを設定します。</td>
		<td>Y</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
Suspend-AzureDataFactoryPipeline コマンドレットは、Azure Data Factory 内の指定されたパイプラインを中断します。後でパイプラインを再開する際は Resume-AzureDataFactoryPipeline コマンドレットを使用します。

### 例

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

このコマンドは、ADFTutorialDataFactory という Azure Data Factory 内の ADFTutorialPipeline パイプラインを中断します。

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
Resume-AzureDataFactoryPipeline コマンドレットは、Azure Data Factory 内にある現在中断された状態のパイプラインを再開します。 

### 例

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

このコマンドは、以前に Suspend-AzureDataFactoryPipeline コマンドを使用して中断された、ADFTutorialDataFactory という Azure Data Factory 内にある ADFTutorialPipeline パイプラインを再開します。

## 関連項目

記事 | 説明
------ | ---------------
[Azure プレビュー ポータルを使用した Azure Data Factory の監視と管理][monitor-manage-using-portal] | この記事では、Azure プレビュー ポータルを使用して Azure Data Factory の監視と管理を実行する方法について説明しています。
[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources] | この記事には、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[Data Factory で Pig と Hive を使用する][use-pig-and-hive-with-data-factory] | この記事には、HDInsight アクティビティを使用して hive/pig スクリプトを実行し、入力データを処理して出力データを生成する方法を説明したチュートリアルが記載されています。
[チュートリアル:Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 
[Azure Data Factory Cmdlet Reference (Azure Data Factory コマンドレット リファレンス)][cmdlet-reference] | このリファレンスには、すべての **Data Factory コマンドレット**に関する詳細が記載されています。

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--HONumber=35.2-->
