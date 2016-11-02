<properties
    pageTitle="Automation から Log Analytics (OMS) へのジョブの状態とジョブ ストリームの転送 | Microsoft Azure"
    description="この記事では、ジョブの状態と Runbook ジョブ ストリームを Microsoft Operations Management Suite Log Analytics に送信して、詳細な情報の入手ときめ細かい管理を実現する方法について説明します。"
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="magoedte" />


# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-(oms)"></a>Automation から Log Analytics (OMS) へのジョブの状態とジョブ ストリームの転送

Automation からは、Runbook ジョブの状態とジョブ ストリームを Microsoft Operations Management Suite (OMS) Log Analytics ワークスペースに送信できます。  この情報は、Azure ポータルまたは PowerShell を使って、特定の Automation アカウントの個々のジョブの状態別に、またはすべてのジョブを対象に確認できますが、運用上の要件を満たすには、カスタム PowerShell スクリプトを作成する必要があります。  現時点で Log Analytics でできることは以下のとおりです。

- Automation ジョブに関する情報を得る 
- Runbook ジョブの状態 (失敗、中断など) に基づいて電子メールまたはアラートをトリガーする 
- ジョブ ストリームをまたぐ高度なクエリを記述する 
- Automation アカウントをまたいでジョブどうしを関連付ける 
- ジョブの履歴を時系列で視覚化する     

## <a name="prerequisites-and-deployment-considerations"></a>前提条件とデプロイに関する考慮事項

Log Analytics への Automation ログの送信を開始するには、次のものが必要です。

1. OMS サブスクリプション。 詳細については、 [Log Analytics の概要](../log-analytics/log-analytics-get-started.md)に関するページを参照してください。  

    >[AZURE.NOTE]この構成を正常に機能させるためには、OMS ワークスペースおよび Automation アカウントが同じ Azure サブスクリプション内にある必要があります。 
  
2. [Azure ストレージ アカウント](../storage/storage-create-storage-account.md)。  
   
    >[AZURE.NOTE]ストレージ アカウントは、Automation アカウントと同じリージョンにある " *必要があります* "。 
 
3. Azure PowerShell と、バージョン 1.0.8 以降の Operational Insights コマンドレット。 このリリースとそのインストール方法については、「 [Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。
4. Azure 診断および Log Analytics PowerShell。  このリリースとそのインストール方法については、 [Azure 診断と Log Analytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1)に関するページを参照してください。  
5. PowerShell スクリプト **Enable-AzureDiagnostics.ps1** を [PowerShell ギャラリー](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript)からダウンロードします。 このスクリプトにより、次のものが構成されます。
 - 指定した Automation アカウントの Runbook ジョブの状態とストリーム データを保持するストレージ アカウント。
 - このデータを Automation アカウントから収集し、JSON 形式で Azure Blob ストレージ アカウントに格納できるようにします。
 - BLOB ストレージ アカウントから OMS Log Analytics へのデータの収集を構成します。
 - OMS ワークスペースで Automation Log Analytics ソリューションを有効にします。   

**Enable-AzureDiagnostics.ps1** スクリプトは、実行中に次のパラメーターを必要とします。

- *AutomationAccountName* - Automation アカウントの名前
- *LogAnalyticsWorkspaceName* - OMS ワークスペースの名前

*AutomationAccountName* の値を調べるには、Azure Portal で、**[Automation アカウント]** ブレードから Automation アカウントを選択し、**[すべての設定]** を選択します。  **[すべての設定]** ブレードで、**[アカウント設定]** の **[プロパティ]** を選択します。  **[プロパティ]** ブレードで、値をメモすることができます。<br> ![Automation Account properties](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)に関するページを参照してください。


## <a name="setup-integration-with-log-analytics"></a>Log Analytics との統合のセットアップ

1. コンピューターの**スタート**画面から、**Windows PowerShell** を起動します。  
2. PowerShell コマンドライン シェルから、ダウンロードしたスクリプトが含まれているフォルダーに移動し、スクリプトを実行します。その際、*-AutomationAccountName*パラメーターと *-LogAnalyticsWorkspaceName* パラメーターの値を変更します。

    >[AZURE.NOTE] スクリプトの実行後、Azure での認証が求められます。  サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してログインする **必要があります** 。   
    
        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `

3. このスクリプトを実行すると、新しい診断データがストレージに書き込まれてから約 30 分後に、Log Analytics にレコードが表示されます。  その時間が過ぎてもレコードが表示されない場合は、「 [JSON files in blob storage (Blob Storage の JSON ファイル)](../log-analytics/log-analytics-azure-storage-json.md#troubleshooting-configuration-for-azure-diagnostics-written-to-blob-in-json)」のトラブルシューティングのセクションを参照してください。

### <a name="verify-configuration"></a>構成の確認

スクリプトによって Automation アカウントと OMS ワークスペースが正常に構成されたことを確認するには、PowerShell で次の手順を実行します。  その前に OMS ワークスペース名とリソース グループ名の値を調べるには、Azure Portal から Log Analytics (OMS) に移動し、Log Analytics (OMS) ブレードで、**[名前]** と **[リソース グループ]** の値をメモします。<br> ![OMS Log Analytics ワークスペース一覧](media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png)  PowerShell コマンドレット [Get-AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/library/mt603567.aspx) を使って OMS ワークスペースの構成を確認する際に、これら 2 つの値を使用します。

1.  Azure ポータルからストレージ アカウントに移動し、 *AutomationAccountNameomsstorage*という名前付け規則が使われているストレージ アカウントを検索します。  Runbook ジョブが完了すると、すぐに 2 つの BLOB コンテナー (**insights-logs-joblogs** と **insights-logs-jobstreams**) が作成されたことがわかります。  

2.  PowerShell で次の PowerShell コードを実行します。その際、先ほどコピーまたはメモした **ResourceGroupName** パラメーターと **WorkspaceName** パラメーターの値を変更します。  

    Login-AzureRmAccount Get-AzureRmSubscription -SubscriptionName 'SubscriptionName' | Set-AzureRmContext Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "OMSResourceGroupName" ` -Workspace "OMSWorkspaceName" 

    これにより、指定した OMS ワークスペースのストレージに関する情報が返されます。  先ほど指定した Automation アカウントのストレージに関する情報が存在することと、**State** オブジェクトの値が **OK** であることを確認してください。<br> ![Results from Get-AzureRmOperationalInsightsStorageInsights cmdlet](media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png)に関するページを参照してください。


## <a name="log-analytics-records"></a>Log Analytics のレコード

Automation により 2 種類のレコードが OMS リポジトリに作成されます。

### <a name="job-logs"></a>ジョブ ログ

プロパティ | Description|
----------|----------|
Time | Runbook ジョブが実行された日付と時刻。|
resourceId | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
operationName | Azure で実行された操作の種類を指定します。  Automation の場合、値はジョブになります。|
resultType | Runbook ジョブの状態。  次のいずれかの値になります。<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 成功|
resultDescription | Runbook ジョブの結果の状態について説明します。  次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました|
CorrelationId | GUID。Runbook ジョブの関連付け ID です。|
カテゴリ | データの種類の分類。  Automation の場合、値は JobLogs です。|
RunbookName | Runbook の名前。|
JobId | GUID。Runbook ジョブの ID です。|
Caller |  操作を開始したユーザー。  使用できる値は、スケジュールされたジョブの電子メール アドレスまたはシステムです。|

### <a name="job-streams"></a>ジョブ ストリーム
プロパティ | Description|
----------|----------|
Time | Runbook ジョブが実行された日付と時刻。|
resourceId | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
operationName | Azure で実行された操作の種類を指定します。  Automation の場合、値はジョブになります。|
resultType | Runbook ジョブの状態。  次のいずれかの値になります。<br>- 処理中|
resultDescription | Runbook からの出力ストリームが含まれます。|
CorrelationId | GUID。Runbook ジョブの関連付け ID です。|
カテゴリ | データの種類の分類。  Automation の場合、値は JobStreams です。|
RunbookName | Runbook の名前。|
JobId | GUID。Runbook ジョブの ID です。|
Caller | 操作を開始したユーザー。  使用できる値は、スケジュールされたジョブの電子メール アドレスまたはシステムです。| 
StreamType | ジョブ ストリームの種類。 次のいずれかの値になります。<br>- 進行状況<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細|

## <a name="viewing-automation-logs-in-log-analytics"></a>Log Analytics での Automation ログの確認 

Automation ジョブのログを Log Analytics に送信し始めたので、次は、OMS 内でこれらのログに対して何ができるかを確認しましょう。   

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook ジョブが失敗または中断したときに電子メールを送信する 

顧客からの問い合わせとして多いのは、Runbook ジョブに問題が発生したときに電子メールまたはテキストを送信する機能です。   

アラート ルールを作成するには、まずアラートを呼び出す Runbook ジョブ レコードに対するログ検索を作成します。  それにより、アラート ルールを作成して構成するための **[アラート]** ボタンが使用できるようになります。

1.  OMS の [Overview] (概要) ページで、 **[Log Search]**(ログ検索) をクリックします。
2.  クエリ フィールドに「 `Category=JobLogs (ResultType=Failed || ResultType=Suspended)`」と入力して、アラートに対するログ検索クエリを作成します。  `Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`を使用すれば、RunbookName でグループ化することもできます。   
  
    複数の Automation アカウントまたはサブスクリプションからワークスペースへのログをセットアップしてある場合は、サブスクリプションまたは Automation アカウントごとにアラートをグループ化することも可能です。  Automation アカウント名は JobLogs の検索のリソース フィールドから派生していることもあります。  

3.  ページの上部にある **[アラート]** をクリックして、**[アラート ルールの追加]** 画面を開きます。  アラートの構成オプションの詳細については、「 [Log Analytics のアラート](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule)」を参照してください。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>エラーが発生したすべてのジョブを特定する 

エラーに基づいてアラートを生成するだけでなく、Runbook ジョブで終了しないエラーが発生したタイミングを把握したい場合もあると思われます (PowerShell によってエラー ストリームが生成されるものの、終了しないエラーによってジョブが中断または失敗することはありません)。    

1. OMS ポータルで **[ログ検索]**をクリックします。
2. クエリ フィールドに 「 `Category=JobStreams StreamType_s=Error | measure count() by JobId_g` 」と入力し、 **[検索]**をクリックします。


### <a name="view-job-streams-for-a-job"></a>ジョブのジョブ ストリームを確認する  

ジョブのデバッグを行っているときに、ジョブ ストリームの確認が必要になることもあります。  次のクエリは、GUID が 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 である 1 つのジョブのすべてのストリームを表示します。   

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription` 

### <a name="view-historical-job-status"></a>ジョブの状態の履歴を確認する 

最後に、ジョブの履歴を時系列で視覚化する必要が生じることもあります。  次のクエリを使うと、ジョブの状態を時系列で検索できます。 

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day`  
<br> ![OMS Historical Job Status Chart](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>


## <a name="summary"></a>概要

Automation ジョブの状態とストリーム データを Log Analytics に送信することで、Automation ジョブの状態を詳細に把握できます。そのために、問題が発生したときに通知するアラートを設定したり、Runbook の結果、Runbook のジョブの状態、その他関連する主要な指標やメトリックを視覚化する高度なクエリを使ってカスタム ダッシュボードを設定したりできます。  これにより、運用状況を見える化し、インシデントに迅速に対処可能です。  


## <a name="next-steps"></a>次のステップ

- 各種検索クエリの作成方法と、Log Analytics での Automation ジョブの確認方法の詳細については、「 [Log Analytics におけるログの検索](../log-analytics/log-analytics-log-searches.md)
- Runbook から出力とエラー メッセージを作成および取得する方法については、 [Runbook の出力とメッセージ](automation-runbook-output-and-messages.md) 
- Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、 [Runbook ジョブの追跡](automation-runbook-execution.md)
- OMS Log Analytics とデータ収集ソースの詳細については、 [Log Analytics での Azure Storage データの収集の概要](../log-analytics/log-analytics-azure-storage.md)


<!--HONumber=Oct16_HO2-->


