---
title: Azure Automation での Runbook の実行
description: Azure Automation で Runbook が処理される方法の詳細について説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c8968eb72b29b004d94e25433da65d3262287147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367144"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation での Runbook の実行

Runbook は、その内部で定義されているロジックに基づいて実行されます。 Runbook は、中断された場合、先頭から再開されます。 この動作では、一時的な問題が発生した場合の再起動をサポートするように Runbook を作成する必要があります。

Azure Automation で Runbook を開始すると、ジョブが作成されます。 ジョブは、Runbook の単一の実行インスタンスです。 ジョブごとに、Azure サブスクリプションに接続することによって Azure リソースにアクセスします。 データ センター内のリソースにパブリック クラウドからアクセスできる場合、ジョブはそれらのリソースにのみアクセスします。

Azure Automation では、Runbook の実行中に各ジョブを実行するための worker が割り当てられます。 ワーカーは多数の Azure アカウントで共有されますが、異なる Automation アカウントからのジョブは互いに分離されます。 ジョブの要求を処理する worker を制御することはできません。

Azure portal で Runbook の一覧を表示すると、各 Runbook に対して開始された各ジョブの状態が示されます。 Azure Automation には、ジョブのログが最大 30 日間保存されます。 

次の図では、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)、[グラフィック Runbook](automation-runbook-types.md#graphical-runbooks)、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) に対する Runbook ジョブのライフサイクルを示します。

![ジョブの状態 - PowerShell Workflow](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="where-to-run-your-runbooks"></a>Runbook を実行する場所

Azure Automation の Runbook は、Azure サンドボックスまたは [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) のいずれかで実行できます。 ほとんどの Runbook は、複数のジョブで使用できる共有環境である Azure サンドボックスで簡単に実行できます。 同じサンドボックスを使用するジョブは、サンドボックスのリソース制限に縛られます。

Hybrid Runbook Worker を使用すると、ロールがホストされているコンピューター上で、環境内のリソースに対して、Runbook を直接実行できます。 Azure Automation によって Runbook が格納および管理された後、1 つ以上の割り当てられたコンピューターに配信されます。

次の表では、いくつかの Runbook 実行タスクと、それぞれに対して推奨される実行環境を示します。

|タスク|最適な選択肢|Notes|
|---|---|---|
|Azure リソースと統合する|Azure サンドボックス|Azure でホストされ、認証がより簡単です。 Azure VM で Hybrid Runbook Worker を使用している場合は、[Azure リソース用のマネージド ID](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) を使用できます。|
|Azure リソースを管理するための最適なパフォーマンスを得る|Azure サンドボックス|スクリプトは同じ環境内で実行され、待ち時間が短くなります。|
|運用コストを最小限に抑える|Azure サンドボックス|コンピューティングのオーバーヘッドがなく、VM の必要がありません。|
|実行時間の長いスクリプトを実行する|Hybrid Runbook Worker|Azure のサンドボックスには[リソースの制限があります](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|ローカル サービスと対話する|Hybrid Runbook Worker|ホスト コンピューターに直接アクセスできます。|
|サードパーティのソフトウェアと実行可能ファイルが必要である|Hybrid Runbook Worker|自分でオペレーティング システムを管理し、ソフトウェアをインストールできます。|
|Runbook でファイルまたはフォルダーを監視する|Hybrid Runbook Worker|Hybrid Runbook Worker で [Watcher タスク](automation-watchers-tutorial.md)を使用します。|
|リソースを大量に消費するスクリプトを実行する|Hybrid Runbook Worker| Azure のサンドボックスには[リソースの制限があります](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|特定の要件を持つモジュールを使用する| Hybrid Runbook Worker|いくつかの例を次に示します。</br> WinSCP - winscp.exe への依存関係 </br> IISAdministration - IIS の有効化に対する依存関係。|
|インストーラでモジュールをインストールする|Hybrid Runbook Worker|サンドボックス用のモジュールでは、コピーがサポートされている必要があります。|
|4\.7.2 以外のバージョンの .NET Framework が必要な Runbook またはモジュールを使用する|Hybrid Runbook Worker|Automation のサンドボックスには .NET Framework 4.7.2 が備わっており、それをアップグレードする方法はありません。|
|昇格が必要なスクリプトを実行する|Hybrid Runbook Worker|サンド ボックスでは、昇格は許可されません。 Hybrid Runbook Worker を使用すると、UAC をオフにして、昇格が必要なコマンドを実行するときに **Invoke-Command** を使用できます。|
|WMI へのアクセスが必要なスクリプトを実行する|Hybrid Runbook Worker|クラウドのサンドボックスで実行されているジョブでは、WMI にアクセスできません。 |

## <a name="runbook-behavior"></a>Runbook の動作

### <a name="creating-resources"></a>リソースの作成

Runbook でリソースを作成する場合、リソースの作成を試みる前に、リソースが既に存在するかどうかをスクリプトで確認する必要があります。 基本的な例を次に示します。

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>時間に依存するスクリプトのサポート

Runbook は、堅牢であり、再起動または失敗する可能性がある一時的なエラーを処理できる必要があります。 Runbook が失敗した場合、Azure Automation によって再試行されます。

Runbook が時間制約内で普通に実行される場合は、スクリプトで実行時間をチェックするロジックを実装します。 このチェックにより、特定の時間帯にのみ、起動、シャットダウン、スケールアウトなどの操作が実行されることが保証されます。

> [!NOTE]
> Azure サンドボックス プロセス上のローカル時刻は UTC に設定されます。 Runbook での日付と時刻の計算では、この事実を考慮する必要があります。

### <a name="tracking-progress"></a>進行状況の追跡

Runbook をモジュール形式で作成し、簡単に再利用したり再起動したりできるように Runbook のロジックを構成することをお勧めします。 Runbook の進行状況を追跡することは、問題がある場合に Runbook のロジックが正常に実行されることを保証する適切な方法です。 ストレージ アカウント、データベース、ファイル共有などの外部ソースを使用することで、Runbook の進行状況を追跡できます。 最後に実行されたアクションの状態をまずチェックするロジックを、Runbook に作成できます。 その後、チェックの結果に基づいて、Runbook 内の特定のタスクをスキップまたは続行できます。

### <a name="preventing-concurrent-jobs"></a>同時実行ジョブの防止

一部の Runbook は、同時に複数のジョブで実行されると、おかしな動作をすることがあります。 この場合は、既に実行中のジョブがあるかどうかをチェックするロジックを Runbook に実装することが重要です。 基本的な例を次に示します。

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>複数のサブスクリプションの操作

複数のサブスクリプションを扱うには、Runbook で [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) コマンドレットを使用して、同じサンドボックスで実行されている別の Runbook から認証コンテキストが取得されないようにする必要があります。 また、Runbook は、Az モジュール コマンドレットで `AzContext` パラメーターを使用して、適切なコンテキストを渡します。

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>例外の処理

ここでは、Runbook で例外または断続的な問題を処理するいくつかの方法について説明します。

#### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 変数により、終了しないエラーに対して PowerShell が対応する方法が決まります。 終了するエラーは常に終了し、*ErrorActionPreference* の影響を受けません。

Runbook で `ErrorActionPreference` を使用すると、`Get-ChildItem` コマンドレットの **PathNotFound** など、通常は強制終了にならないエラーによって Runbook の完了が停止されます。 次の例は、`ErrorActionPreference` の使用方法を示しています。 スクリプトが停止するため、最後の `Write-Output` コマンドは実行されません。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) は、終了するエラーを処理するために PowerShell スクリプトで使用されます。 スクリプトでは、このメカニズムを使用して、特定の例外または一般的な例外をキャッチできます。 エラーを追跡または処理するには、`catch` ステートメントを使用する必要があります。 次の例は、存在しないファイルをダウンロードしようとしています。 これは、`System.Net.WebException` 例外をキャッチして、その他の例外の最後の値を返します。

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) を使用すると、終了するエラーを生成できます。 このメカニズムは、Runbook で独自のロジックを定義するときに役立ちます。 スクリプトが停止の必要な条件を満たしている場合は、`throw` ステートメントを使用して停止できます。 次の例では、このステートメントを使用して、必要な関数パラメーターを示します。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>実行可能ファイルの使用またはプロセスの呼び出し

Azure サンドボックスで実行される Runbook では、実行可能ファイル ( **.exe** ファイル) やサブプロセスなどの、プロセスの呼び出しはサポートされていません。  その理由は、Azure サンドボックスは、基になるすべての API にアクセスできない可能性があるコンテナーで実行される共有プロセスであるためです。 サードパーティー製ソフトウェアやサブプロセスの呼び出しが必要なシナリオの場合は、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) で Runbook を実行することをお勧めします。

### <a name="accessing-device-and-application-characteristics"></a>デバイスとアプリケーションの特性へのアクセス

Azure サンドボックスで実行される Runbook ジョブには、デバイスまたはアプリケーションの特性に対するアクセス権はありません。 Windows でパフォーマンス メトリック (一般的なものはメモリや CPU の使用率など) のクエリを実行するために使用される最も一般的な API は WMI です。 ただし、クラウドで実行されているジョブでは Web-Based Enterprise Management (WBEM) の Microsoft による実装にアクセスできないため、どの API が使用されるかは問題ではありません。 このプラットフォームは、デバイスとアプリケーションの特性を定義するための業界標準を提供する Common Information Model (CIM) に基づいて構築されています。

## <a name="handling-errors"></a>エラーの処理

Runbook はエラーを処理できる必要があります。 PowerShell には、終了するエラーと終了しないエラーの 2 種類があります。 終了するエラーが発生すると、Runbook の実行は停止されます。 Runbook は、[失敗] のジョブ状態で停止します。

終了しないエラーの場合は、エラー発生後もスクリプトを継続できます。 終了しないエラーの例は、Runbook で、存在しないパスを指定して `Get-ChildItem` コマンドレットを使用したときに発生するものです。 PowerShell では、パスが存在しないことを確認して、エラーをスローし、次のフォルダーへと処理を継続します。 この場合のエラーでは、Runbook のジョブ状態は [失敗] に設定されず、ジョブが完了する可能性もあります。 終了しないエラー時に Runbook を強制的に停止するには、コマンドレットで `-ErrorAction Stop` を使用できます。

## <a name="handling-jobs"></a>ジョブの処理

同じ Automation アカウントからジョブの実行環境を再利用できます。 1 つの Runbook で、多数のジョブを同時に実行することができます。 同時に実行するジョブが多いほど、同じサンドボックスにディスパッチする頻度が上がります。

同じサンドボックス プロセスで実行するジョブは、お互いに影響し合う可能性があります。 1 つの例は、`Disconnect-AzAccount` コマンドレットの実行です。 このコマンドレットを実行すると、共有サンドボックス プロセス内の各 Runbook ジョブが切断されます。

Azure サンドボックスで実行されている Runbook から開始された PowerShell ジョブは、完全言語モードで実行されないことがあります。 PowerShell 言語モードの詳細については、「[PowerShell 言語モード](/powershell/module/microsoft.powershell.core/about/about_language_modes)」を参照してください。 Azure Automation でのジョブの操作について詳しくは、「[PowerShell を使用したジョブの状態の取得](#retrieving-job-status-using-powershell)」を参照してください。

### <a name="job-statuses"></a>ジョブの状態

次の表では、ジョブで可能性のある状態について説明します。

| Status | 説明 |
|:--- |:--- |
| 完了 |ジョブは正常に完了しました。 |
| 失敗 |グラフィック Runbook または PowerShell ワークフロー Runbook のコンパイルが失敗しました。 PowerShell スクリプト Runbook を開始できなかったか、ジョブで例外が発生しました。 「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。|
| 失敗、リソースを待機中 |ジョブは [fair share](#fair-share) の限界に 3 回到達し、毎回、同じチェックポイントから、または Runbook の先頭から起動したために、失敗しました。 |
| キューに登録済み |ジョブは、開始できるように、Automation ワーカー上のリソースが使用可能になるのを待機しています。 |
| 開始中 |ジョブがワーカーに割り当てられており、システムがジョブを起動しています。 |
| 再開中 |システムは、ジョブが停止された後、そのジョブを再開しています。 |
| 実行中 |ジョブは実行中です。 |
| 実行中、リソースを待機中 |ジョブはフェア シェア制限に達したためにアンロードされました。 ジョブは最後のチェックポイントからすぐに再開します。 |
| 停止済み |ジョブは完了した後、ユーザーによって停止されました。 |
| 停止中 |システムは、ジョブを停止しています。 |
| Suspended |[グラフィック Runbook と PowerShell ワークフロー Runbook](automation-runbook-types.md) のみに適用されます。 ユーザーか、システムか、または Runbook 内のコマンドによってジョブは中断されました。 Runbook にチェックポイントがない場合は、先頭から開始されます。 チェックポイントがある場合は、最後のチェックポイントからもう一度再開できます。 システムは、例外が発生した場合にのみ Runbook を中断します。 既定で、`ErrorActionPreference` 変数は、エラーでもジョブの実行を継続することを示す [Continue] に設定されます。 このユーザー設定変数を [Stop] に設定すると、エラーが発生したときにジョブは中断されます。  |
| 中断中 |[グラフィック Runbook と PowerShell ワークフロー Runbook](automation-runbook-types.md) のみに適用されます。 ユーザーの要求を受けてシステムはジョブを中断しようとしています。 Runbook は、次のチェックポイントに到達してからでないと、中断できません。 最後のチェックポイントに既に到達している場合は、完了してからでないと中断できません。 |

### <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal を使用したジョブの状態の表示

すべての Runbook ジョブの状態の概要を表示したり、Azure portal での特定の Runbook ジョブの詳細にドリルダウンしたりできます。 Log Analytics ワークスペースとの統合を構成し、Runbook のジョブの状態やジョブ ストリームを転送することも可能です。 Azure Monitor ログとの統合の詳細については、「[Automation から Azure Monitor ログにジョブの状態とジョブ ストリームを転送する](automation-manage-send-joblogs-log-analytics.md)」を参照してください。

選択した Automation アカウントの右にある **[ジョブの統計情報]** タイルでは、すべての Runbook ジョブの概要を確認できます。

![[ジョブの統計情報] タイル](./media/automation-runbook-execution/automation-account-job-status-summary.png)

このタイルでは、実行された各ジョブの数を確認できるほか、その状態がグラフィカルに表示されます。

タイルをクリックすると、実行されたすべてのジョブの概要を示す一覧が含まれた [ジョブ] ページが表示されます。 このページには、各ジョブの状態、Runbook 名、開始時刻、完了時間が表示されます。

![Automation アカウントの [ジョブ] ページ](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

**[ジョブのフィルター]** を選択すると、ジョブの一覧をフィルター処理すできます。 特定の Runbook、ジョブの状態、またはドロップダウン リストから選択したものでフィルター処理し、検索の時間範囲を指定します。

![ジョブの状態でフィルター処理する](./media/automation-runbook-execution/automation-account-jobs-filter.png)

また、Automation アカウントの [Runbook] ページで Runbook を選択してから、 **[ジョブ]** タイルを選択することで、特定の Runbook のジョブ概要の詳細を表示することができます。 この操作により、[ジョブ] ページが表示されます。 ここから、ジョブ レコードをクリックして、その詳細と出力を表示できます。

![Automation アカウントの [ジョブ] ページ](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>ジョブの概要の表示

上で説明したジョブの概要を使用すると、特定の Runbook 用に作成されたすべてのジョブと、それらのジョブの最新の状態を、一覧で表示できます。 ジョブの詳細情報と出力を見るには、一覧でその名前をクリックします。 ジョブの詳細表示には、そのジョブに指定された Runbook パラメーターの値が含まれます。

次の手順を使用して Runbook のジョブを表示します。

1. Azure Portal で、 **[Automation]** を選択し、次に Automation アカウントの名前を選択します。
2. ハブから、 **[プロセス オートメーション]** の **[Runbook]** を選択します。
3. [Runbook] ページで、一覧から Runbook を選択します。
3. 選択した Runbook のページで、 **[ジョブ]** タイルをクリックします。
4. 一覧のジョブのいずれかをクリックすると、Runbook ジョブ詳細ページに、その詳細と出力が表示されます。

### <a name="retrieving-job-status-using-powershell"></a>PowerShell を使用したジョブの状態の取得

Runbook 用に作成されたジョブと、特定のジョブの詳細を取得するには、`Get-AzAutomationJob` コマンドレットを使用します。 `Start-AzAutomationRunbook` を使用して PowerShell で Runbook を開始すると、結果として作成されたジョブが返されます。 ジョブの出力を取得するには [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) を使用します。

次の例では、サンプル Runbook の最後のジョブが取得されて、その状態、Runbook パラメーターに指定された値、およびジョブの出力が表示されます。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

次の例では、特定のジョブの出力が取得されて、各レコードが返されます。 いずれかのレコードで例外が発生した場合、スクリプトでは値ではなく例外が書き出されます。 出力中に正常にログに記録されない可能性がある追加情報を例外で提供できるため、この動作は有用です。

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="getting-details-from-the-activity-log"></a>アクティビティ ログからの詳細の取得る

Runbook を開始したユーザーやアカウントなど、Runbook に関する詳細を、Automation アカウントのアクティビティ ログから取得できます。 次の PowerShell の例では、指定した Runbook を最後に実行したユーザーが提供されます。

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Runbook 間でのリソースの共有

クラウド内のすべての Runbook 間でリソースを共有するため、3 時間以上実行されているジョブがあると、Azure Automation はそれらのジョブを一時的にアンロードまたは停止します。 [PowerShell の Runbook](automation-runbook-types.md#powershell-runbooks) と [Python の Runbook](automation-runbook-types.md#python-runbooks) のジョブは停止されて再起動されず、ジョブの状態は [停止済み] になります。

長時間実行されるタスクの場合は、Hybrid Runbook Worker の使用をお勧めします。 Hybrid Runbook Worker はフェア シェアによって制限されず、Runbook が実行できる時間に制限がありません。 その他のジョブの[制限](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)は、Azure サンドボックスと Hybrid Runbook Worker の両方に適用されます。 Hybrid Runbook Worker は 3 時間のフェア シェア制限を受けませんが、予期しないローカル インフラストラクチャの問題からの再起動がサポートされる worker で実行されるように Runbook を開発する必要があります。

もう 1 つのオプションは、子 Runbook を使用して Runbook を最適化することです。 たとえば、複数のデータベースに対するデータベース操作など、複数のリソースに対する同じ機能によって Runbook がループする可能性があります。 この関数を[子 Runbook](automation-child-runbooks.md) に移動し、`Start-AzAutomationRunbook` を使用して、お使いの Runbook でそれを呼び出すことができます。 子 Runbook は別々のプロセスで並列に実行されます。

子 Runbook を使用すると、親 Runbook の完了までにかかる合計時間が短縮されます。 Runbook は、子 Runbook が完了した後にまだ実行する操作がある場合、`Get-AzAutomationJob` コマンドレットを使用して、子 Runbook のジョブ状態を確認できます。

## <a name="next-steps"></a>次のステップ

* Azure Automation で Runbook を開始するために使用できる方法について詳しくは、「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」を参照してください。
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)に関するページを参照してください。
