---
title: Azure Automation で Runbook を管理する
description: この記事では、Azure Automation で Runbook を管理する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 99f86065b9a33125e3ca4cf72af5b8ec442e3cc0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455459"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Automation で Runbook を管理する

Azure Automation には、新しい Runbook を作成するか、ファイルまたは [Runbook ギャラリー](automation-runbook-gallery.md)から既存の Runbook をインポートすることによって Runbook を追加することができます。 この記事では、Runbook の管理に関する情報と、Runbook の設計に関する推奨パターンとベスト プラクティスについて説明します。 コミュニティによって提供されている Runbook やモジュールの利用について詳しくは、[Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](automation-runbook-gallery.md)に関するページを参照してください。

## <a name="create-a-runbook"></a>Runbook を作成する

Azure portal または PowerShell を使用して、Azure Automation に新しい Runbook を作成します。 作成した Runbook は、次の情報を使用して編集できます。

* [Azure Automation でのテキスト形式の Runbook の編集](automation-edit-textual-runbook.md)
* [Automation Runbook 向けの PowerShell ワークフローの基本的な概念の説明](automation-powershell-workflow.md)
* [Azure Automation で Python 2 パッケージを管理する](python-packages.md)
* [Azure Automation で Python 3 パッケージ (プレビュー) を管理する](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure portal で Runbook を作成する

1. Azure [Portal](https://portal.azure.com) にサインインします。
1. **Automation アカウント** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. Automation アカウントで **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
1. **[Runbook の作成]** をクリックします。
    1. Runbook に名前を付けます。
    1. **[Runbook の種類]** ドロップダウンから [種類](automation-runbook-types.md)を選択します。 Runbook 名は、先頭を英字にする必要があり、英字、数字、アンダースコア、ダッシュを使用できます
    1. **[ランタイム バージョン]** を選択します
    1. 該当する **[説明]** を入力します
1. **[作成]** をクリックして、Runbook を作成します。

### <a name="create-a-runbook-with-powershell"></a>PowerShell で Runbook を作成する

空の Runbook を作成するには、[New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) コマンドレットを使用します。 `Type` パラメーターを使用して、`New-AzAutomationRunbook` に対して定義されている Runbook の種類の 1 つを指定します。

次の例は、新しい空の Runbook を作成する方法を示しています。

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Runbook をインポートする

PowerShell または PowerShell ワークフロー ( **.ps1**) スクリプト、グラフィカル Runbook ( **.graphrunbook**)、Python 2 または Python 3 スクリプト ( **.py**) をインポートして独自の Runbook を作成することができます。 インポート時に作成される [Runbook の種類](automation-runbook-types.md)を指定します。その際、次のことを考慮します。

* ワークフローが含まれていない **.ps1** ファイルは、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) または [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) のどちらにもインポートすることができます。 それを PowerShell ワークフロー Runbook にインポートすると、ワークフローに変換されます。 この場合、Runbook には、加えられた変更を説明するコメントが追加されます。

* PowerShell ワークフローを含む **.ps1** ファイルは、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) にのみインポートできます。 ファイルに複数の PowerShell ワークフローが含まれている場合、インポートは失敗します。 各ワークフローを個別のファイルに保存し、それぞれを個別にインポートする必要があります。

* PowerShell ワークフローを含む **.ps1** ファイルは PowerShell スクリプト エンジンでは認識できないため、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) にインポートしないでください。

* **.graphrunbook** ファイルは、新しい [グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) にのみインポートします。

### <a name="import-a-runbook-from-the-azure-portal"></a>Azure portal から Runbook をインポートする

次の手順を使用して、スクリプト ファイルを Azure Automation にインポートできます。

> [!NOTE]
> このポータルを使用して PowerShell ワークフロー Runbook にインポートできるのは **.ps1** ファイルだけです。

1. Azure portal で **[Automation アカウント]** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. Automation アカウントで **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
1. **[Runbook のインポート]** をクリックします。 次のいずれかのオプションを選択できます。
    1. **[ファイルの参照]** - ローカル コンピューターからファイルを選択します。
    1. **[ギャラリーで参照]** - ギャラリーから既存の Runbook を参照して選択できます。
1. ファイルを選択します。
1. **[名前]** フィールドが有効になっている場合は、必要に応じて Runbook 名を変更することができます。 名前は文字で始める必要があり、文字、数字、アンダースコア、およびダッシュを含めることができます。
1. [ **[Runbook の種類]** ](automation-runbook-types.md) は自動的に設定されますが、該当する制限を考慮して種類を変更することもできます。
1. **[ランタイム バージョン]** は、自動的に設定されるか、またはドロップダウン リストからバージョンを選択します。
1. **[インポート]** をクリックします。 Automation アカウントの Runbook の一覧に新しい Runbook が表示されます。
1. 実行する [Runbook は前もって発行しておく](#publish-a-runbook)必要があります。

> [!NOTE]
> グラフィカル Runbook は、インポート後、他の種類に変換することができます。 ただし、グラフィカル Runbook をテキスト形式の Runbook に変換することはできません。

### <a name="import-a-runbook-with-powershell"></a>PowerShell で Runbook をインポートする

Runbook のドラフトとしてスクリプト ファイルをインポートするには、[Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) コマンドレットを使用します。 Runbook が既に存在する場合、コマンドレットで `Force` パラメーターを使用しないと、インポートは失敗します。

次の例では、スクリプト ファイルを Runbook にインポートする方法を示します。

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>リソースを処理する

Runbook で[リソース](automation-runbook-execution.md#resources)を作成する場合、リソースの作成を試みる前に、リソースが既に存在するかどうかをスクリプトで確認する必要があります。 基本的な例を次に示します。

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>アクティビティ ログから詳細を取得する

Runbook を開始したユーザーやアカウントなど、Runbook に関する詳細を、Automation アカウントの[アクティビティ ログ](automation-runbook-execution.md#activity-logging)から取得できます。 次の PowerShell の例では、指定した Runbook を最後に実行したユーザーが提供されます。

```powershell-interactive
$rgName = 'MyResourceGroup'
$accountName = 'MyAutomationAccount'
$runbookName = 'MyRunbook'
$startTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $rgName
    StartTime         = $startTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $runbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $rgName
            AutomationAccountName = $accountName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $runbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName, $Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>進捗状況の追跡

簡単に再利用や再起動ができるロジックを使用したモジュールの形式で Runbook を作成することをお勧めします。 問題が発生しても、Runbook の進行状況が追跡されていれば、Runbook のロジックを正常に実行することができます。

Runbook の進行状況は、ストレージ アカウント、データベース、ファイル共有などの外部ソースを使用して追跡できます。 最後に実行されたアクションの状態をまずチェックするロジックを、Runbook に作成します。 その後、チェックの結果に基づいて、Runbook 内の特定のタスクをスキップまたは続行できます。

## <a name="prevent-concurrent-jobs"></a>同時実行ジョブの防止

一部の Runbook は、同時に複数のジョブで実行されると、おかしな動作をすることがあります。 この場合は、既に実行中のジョブがあるかどうかをチェックするロジックを Runbook に実装することが重要です。 基本的な例を次に示します。

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

# Check for already running or new runbooks
$runbookName = "runbookName"
$resourceGroupName = "resourceGroupName"
$automationAccountName = "automationAccountName"

$jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName `
    -AutomationAccountName $automationAccountName `
    -RunbookName $runbookName `
    -DefaultProfile $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook $runbookName is already running"
    exit 1
} else {
    # Insert Your code here
    Write-Output "Runbook $runbookName is not running"
}
```

Runbook をシステム割り当てマネージド ID で実行する場合は、コードをそのままにしておきます。 ユーザー割り当てマネージド ID を使用する場合は、次のようにします。

1. 行 5 から `$AzureContext = (Connect-AzAccount -Identity).context` を削除します。
1. それを `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` に置き換えた後、
1. クライアント ID を入力します。

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>時間に依存するスクリプトの一時的なエラーを処理する

Runbook は堅牢であること、そして再起動や失敗を引き起こす可能性のある[エラー](automation-runbook-execution.md#errors) (一時的なエラーを含む) を処理できることが必要です。 Runbook が失敗した場合、Azure Automation によって再試行されます。

Runbook が時間制約内で普通に実行される場合は、スクリプトで実行時間をチェックするロジックを実装します。 このチェックにより、特定の時間帯にのみ、起動、シャットダウン、スケールアウトなどの操作が実行されることが保証されます。

> [!NOTE]
> Azure サンドボックス プロセス上のローカル時刻は UTC に設定されます。 Runbook での日付と時刻の計算では、この事実を考慮する必要があります。

## <a name="work-with-multiple-subscriptions"></a>複数のサブスクリプションの操作

Runbook は、[サブスクリプション](automation-runbook-execution.md#subscriptions)を操作できることが必要です。 たとえば、複数のサブスクリプションを処理するために、Runbook には [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) コマンドレットが使用されます。 このコマンドレットを使用するのは、同じサンドボックスで実行されている別の Runbook から認証コンテキストが取得されないようにするためです。 

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

$childRunbookName = 'childRunbookDemo'
$resourceGroupName = "resourceGroupName"
$automationAccountName = "automationAccountName"

$startParams = @{
    ResourceGroupName     = $resourceGroupName
    AutomationAccountName = $automationAccountName
    Name                  = $childRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

Runbook をシステム割り当てマネージド ID で実行する場合は、コードをそのままにしておきます。 ユーザー割り当てマネージド ID を使用する場合は、次のようにします。

1. 行 5 から `$AzureContext = (Connect-AzAccount -Identity).context` を削除します。
1. それを `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` に置き換えた後、
1. クライアント ID を入力します。

## <a name="work-with-a-custom-script"></a>カスタム スクリプトを使用する

> [!NOTE]
> 通常、Log Analytics エージェントがインストールされているホストでカスタム スクリプトと Runbook を実行することはできません。

カスタム スクリプトを使用するには、次の手順に従います。

1. Automation アカウントを作成します。
2. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ロールを展開します。
3. Linux コンピューターでは通常より高い権限が必要です。 サインインして[署名の確認を無効にします](automation-linux-hrw-install.md#turn-off-signature-validation)。

## <a name="test-a-runbook"></a>Runbook をテストする

Runbook のテスト時には [ドラフト バージョン](#publish-a-runbook) が実行され、そのバージョンで行われるすべてのアクションが完了します。 ジョブ履歴は作成されませんが、 **[テスト出力]** ペインに [出力](automation-runbook-output-and-messages.md#use-the-output-stream)および [警告とエラー](automation-runbook-output-and-messages.md#working-with-message-streams)のストリームが表示されます。 [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) 変数が `Continue` に設定されている場合のみ、[詳細ストリーム](automation-runbook-output-and-messages.md#write-output-to-verbose-stream)に対するメッセージが [出力] ペインに表示されます。

ドラフト バージョンを実行している場合も、Runbook は通常どおり実行され、環境中のリソースに対するすべての操作が実行されます。 このため、Runbook をテストするのは非運用環境のリソースのみにしてください。

[Runbook の各種類](automation-runbook-types.md)をテストする手順はいずれも同じです。 Azure portal のテキスト エディターとグラフィカル エディターではテスト方法に違いはありません。

1. [テキスト エディター](automation-edit-textual-runbook.md)または[グラフィカル エディター](automation-graphical-authoring-intro.md)のどちらかで、ドラフト バージョンの Runbook を開きます。
1. **[テスト]** をクリックして **[テスト]** ページを開きます。
1. Runbook にパラメーターがある場合は左ペインに表示され、そこでテストに使用する値を指定することができます。
1. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) に対してテストを実施する場合は、 **[実行設定]** を **[ハイブリッド worker]** に変更してターゲット グループの名前を選択します。 それ以外の場合は、既定の **[Azure]** をそのまま使用してクラウドでテストを実行します。
1. **[開始]** をクリックしてテストを開始します。
1. テスト中の [Powershell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) および [グラフィカル](automation-runbook-types.md#graphical-runbooks) Runbook は、 **[出力]** ペインのボタンで停止および一時停止できます。 Runbook は、中断される場合は中断前に現在のアクティビティを完了します。 Runbook を中断した後で、停止または再開できます。
1. **[出力]** ペインで Runbook の出力を調べます。

## <a name="publish-a-runbook"></a>Runbook を発行する

新しく作成またはインポートした Runbook は、実行する前に発行しておく必要があります。 Azure Automation の各 Runbook には、ドラフト バージョンと発行バージョンがあります。 実行できるのは発行バージョンのみで、編集できるのはドラフト バージョンのみです。 ドラフト バージョンを変更しても発行バージョンに影響はありません。 ドラフト バージョンを使用できるようにする必要がある場合は、それを発行します。これにより、現在発行されているバージョンがドラフト バージョンで上書きされます。

### <a name="publish-a-runbook-in-the-azure-portal"></a>Azure portal で Runbook を発行する

1. Azure portal で **[Automation アカウント]** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. お使いの Automation アカウントで Runbook を開きます。
1. **[編集]** をクリックします。
1. **[発行]** をクリックしてから、確認メッセージに対して **[はい]** を選択します。

### <a name="publish-a-runbook-using-powershell"></a>PowerShell を使用して Runbook を発行する

[Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) コマンドレットを使用して Runbook を発行します。 

```azurepowershell-interactive
$accountName = "MyAutomationAccount"
$runbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $accountName
    ResourceGroupName     = $rgName
    Name                  = $runbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Azure portal で Runbook をスケジュールする

Runbook が発行済みであるときに、操作のスケジュールを設定できます。

1. Azure portal で **[Automation アカウント]** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. Runbook の一覧から Runbook を選択します。
1. **[リソース]** の下の **[スケジュール]** を選択します。
1. **[スケジュールの追加]** を選択します。
1. **[Runbook のスケジュール設定]** ウィンドウで、 **[スケジュールを Runbook にリンクします]** を選択します。
1. **[スケジュール]** ウィンドウで、 **[新しいスケジュールを作成します]** を選択します。
1. **[新しいスケジュール]** ウィンドウで、名前、説明、その他のパラメーターを入力します。
1. スケジュールを作成したら、それを強調表示し、 **[OK]** をクリックします。 これで、Runbook にリンクされるようになります。
1. メールボックスの電子メールで、Runbook の状態通知を探します。

## <a name="obtain-job-statuses"></a>ジョブの状態を取得する

### <a name="view-statuses-in-the-azure-portal"></a>Azure portal で状態を表示する

Azure Automation におけるジョブの処理について詳しくは、「[ジョブ](automation-runbook-execution.md#jobs)」を参照してください。 Runbook ジョブを見る準備が整ったら、Azure portal を使用して Automation アカウントにアクセスします。 右側にある **[ジョブの統計情報]** で、すべての Runbook ジョブの概要を確認できます。

![[ジョブの統計情報] タイル](./media/manage-runbooks/automation-account-job-status-summary.png)

概要では、実行された各ジョブの数を確認できるほか、その状態がグラフィカルに表示されます。

タイルをクリックすると、実行されたすべてのジョブの概要を示す一覧が含まれた [ジョブ] ページが表示されます。 このページには、各ジョブの状態、Runbook 名、開始時刻、完了時間が表示されます。

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="[ジョブ] ページのスクリーンショット。":::

**[ジョブのフィルター]** を選択すると、ジョブの一覧をフィルター処理すできます。 特定の Runbook、ジョブの状態、またはドロップダウン リストから選択したものでフィルター処理し、検索の時間範囲を指定します。

![ジョブの状態でフィルター処理する](./media/manage-runbooks/automation-account-jobs-filter.png)

また、Automation アカウントの [Runbook] ページで Runbook を選択してから、 **[ジョブ]** を選択することで、特定の Runbook のジョブ概要の詳細を表示することができます。 この操作により、[ジョブ] ページが表示されます。 ここから、ジョブ レコードをクリックして、その詳細と出力を表示できます。

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="[エラー] ボタンが強調表示された [ジョブ] ページのスクリーンショット。":::

### <a name="retrieve-job-statuses-using-powershell"></a>PowerShell を使用してジョブの状態を取得する

Runbook 用に作成されたジョブと、特定のジョブの詳細を取得するには、[Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) コマンドレットを使用します。 `Start-AzAutomationRunbook` を使用して Runbook を開始すると、結果として作成されたジョブが返されます。 ジョブの出力を取得するには [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) を使用します。

次の例では、サンプル Runbook の最後のジョブが取得されて、その状態、Runbook パラメーターに指定された値、およびジョブの出力が表示されます。

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

次の例では、特定のジョブの出力が取得されて、各レコードが返されます。 いずれかのレコードで[例外](automation-runbook-execution.md#exceptions)が発生した場合、スクリプトでは値ではなく例外が書き出されます。 この動作は役に立ちます。出力時に通常はログに記録されない追加情報が、例外によって提供される可能性があるためです。

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>次のステップ

* Runbook の管理の詳細については「[Azure Automation での Runbook の実行](automation-runbook-execution.md)」を参照してください。
* PowerShell Runbook の準備については、「[Azure Automation でのテキスト形式の Runbook の編集](automation-edit-textual-runbook.md)」を参照してください。
* Runbook の実行に関する問題のトラブルシューティングについては、[Runbook の問題のトラブルシューティング](troubleshoot/runbooks.md)に関するページを参照してください。
