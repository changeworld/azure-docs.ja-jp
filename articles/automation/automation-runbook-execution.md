---
title: Azure Automation での Runbook の実行
description: Azure Automation で Runbook が処理される方法の詳細について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b1a6e2921fdaf9ede1184cfc02c3f61f63c60ac
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393765"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation での Runbook の実行

Azure Automation で runbook を開始するときに、ジョブが作成されます。 ジョブは、Runbook の単一の実行インスタンスです。 各ジョブを実行する Azure Automation ワーカーが割り当てられます。 ワーカーは多数の Azure アカウントで共有されますが、異なる Automation アカウントからのジョブは互いに分離されます。 ジョブに対する要求をどのワーカーで処理するかを、制御することはできません。 1 つの Runbook で、多数のジョブを同時に実行することができます。 同じ Automation アカウントからのジョブの実行環境を再利用できます。 Azure Portal で Runbook の一覧を表示すると、各 Runbook に対して起動されたすべてのジョブの状態が一覧表示されます。 Runbook ごとにジョブの一覧を表示して、それぞれの状態を追跡できます。 ジョブのさまざまな状態の説明については、「[ジョブの状態](#job-statuses)」をご覧ください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

次の図に、[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) と [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) のための Runbook ジョブのライフサイクルを示します。

![ジョブの状態 - PowerShell Workflow](./media/automation-runbook-execution/job-statuses.png)

次の図に、 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)のための Runbook ジョブのライフサイクルを示します。

![ジョブの状態 - PowerShell スクリプト](./media/automation-runbook-execution/job-statuses-script.png)

ジョブは、Azure サブスクリプションに接続することにより Azure リソースにアクセスします。 データ センター内のリソースにパブリック クラウドからアクセスできる場合、ジョブはそれらのリソースにのみアクセスします。

## <a name="job-statuses"></a>ジョブの状態

次の表には、ジョブが取り得るさまざまな状態を説明します。 PowerShell には、終了するエラーと終了しないエラーという、2 種類のエラーがあります。 終了するエラーは、発生した場合に Runbook の状態を **[失敗]** に設定します。 終了しないエラーの場合、エラー発生後もスクリプトを継続できます。 終了しないエラーの例では、実在しないパスで `Get-ChildItem` コマンドレットを使用しています。 PowerShell では、パスが存在しないことを確認して、エラーをスローし、次のフォルダーへと処理を継続します。 このエラーでは、Runbook の状態を **[失敗]** に設定せず、**[完了]** とマークすることが可能です。 終了しないエラー時に Runbook を強制的に停止するには、コマンドレットで `-ErrorAction Stop` を使用できます。

| Status | 説明 |
|:--- |:--- |
| 完了 |ジョブは正常に完了しました。 |
| 失敗 |[グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md)では、Runbook のコンパイルが失敗しました。 [PowerShell スクリプト Runbook](automation-runbook-types.md) では、Runbook の開始に失敗したか、ジョブで例外が発生しました。 |
| 失敗、リソースを待機中 |ジョブは [fair share](#fair-share) の限界に 3 回到達し、毎回、同じチェックポイントから、または Runbook の先頭から起動したために、失敗しました。 |
| キューに登録済み |ジョブは Automation ワーカー上のリソースが使用できるようになるのを待機しています。そうなれば、ジョブを起動できます。 |
| 開始中 |ジョブがワーカーに割り当てられており、システムがジョブを起動しています。 |
| 再開中 |システムは、ジョブが停止された後、そのジョブを再開しています。 |
| 実行中 |ジョブは実行中です。 |
| 実行中、リソースを待機中 |ジョブは [fair share](#fair-share) 制限に達したためにアンロードされました。 ジョブは最後のチェックポイントからすぐに再開します。 |
| 停止済み |ジョブは完了した後、ユーザーによって停止されました。 |
| 停止中 |システムは、ジョブを停止しています。 |
| Suspended |ユーザーか、システムか、または Runbook 内のコマンドによってジョブは中断されました。 Runbook にチェックポイントがない場合は、Runbook の先頭から開始されます。 チェックポイントがある場合は、最後のチェックポイントからもう一度再開できます。 Runbook は、例外が発生した場合にシステムによってのみ中断されます。 既定では、ErrorActionPreference は、エラーでもジョブの実行を継続することを意味する **Continue** に設定されています。 このユーザー設定変数を **Stop** に設定すると、エラー発生時にジョブは中断します。 [グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md) のみに適用されます。 |
| 中断中 |ユーザーの要求を受けてシステムはジョブを中断しようとしています。 Runbook は、次のチェックポイントに到達してからでないと、中断できません。 Runbook は、次のチェックポイントに到達してからでないと、中断できません。 [グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md) のみに適用されます。 |

## <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal を使用したジョブの状態の表示

すべての Runbook ジョブの状態の概要を表示したり、Azure portal での特定の Runbook ジョブの詳細にドリルダウンしたりできます。 また、Log Analytics ワークスペースとの統合を構成して、Runbook ジョブの状態およびジョブ ストリームを転送することも可能です。 Log Analytics との統合の詳細については、「[Automation から Log Analytics へのジョブの状態とジョブ ストリームの転送](automation-manage-send-joblogs-log-analytics.md)」を参照してください。

### <a name="automation-runbook-jobs-summary"></a>Automation Runbook ジョブの概要

選択した [Automation アカウント] の右にある **[ジョブの統計情報]** タイルで、すべての Runbook ジョブの概要を確認できます。

![[ジョブの統計情報] タイル](./media/automation-runbook-execution/automation-account-job-status-summary.png)

このタイルでは、実行されたすべてのジョブの数を確認できるほか、その状態がグラフィカルに表示されます。

タイルをクリックすると、実行されているすべてのジョブを総括する一覧を示した **[ジョブ]** ページが表示されます。 このページには、状態、開始時刻、および完了時刻が表示されます。

![Automation アカウントの [ジョブ] ページ](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

ジョブのリストをフィルター処理するには、**[ジョブのフィルター]** を選択します。特定の Runbook、ジョブの状態でフィルターしたり、検索対象の時刻の範囲をドロップダウン リストから選択したりできます。

![[フィルター] の [ジョブの状態]](./media/automation-runbook-execution/automation-account-jobs-filter.png)

また、特定の Runbook について、ジョブ概要の詳細情報を表示することもできます。それには、Automation アカウントで **[Runbook]** ページからその Runbook を選択し、**[ジョブ]** タイルを選択します。 このアクションにより、**[ジョブ]** ページが表示され、そのページでジョブ レコードをクリックすると、そのジョブの詳細と出力が表示されます。

![Automation アカウントの [ジョブ] ページ](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>ジョブの概要

特定の Runbook 用に作成されたすべてのジョブと、それらのジョブの最新の状態を一覧にして表示できます。 この一覧は、ジョブの状態とジョブに最後に変更を加えた日付の範囲とによってフィルター処理することができます。 詳細な情報と出力を表示するには、ジョブの名前をクリックします。 ジョブの詳細表示には、そのジョブに指定された Runbook パラメーターの値が含まれます。

次の手順を使用して Runbook のジョブを表示します。

1. Azure Portal で、**[Automation]** を選択し、次に Automation アカウントの名前を選択します。
2. ハブから **[Runbook]** を選択し、**[Runbook]** ページで、一覧から 1 つの Runbook を選択します。
3. 選択した Runbook のページで、**[ジョブ]** タイルをクリックします。
4. 一覧にあるいずれかのジョブをクリックすると、Runbook の [ジョブの詳細] ページで詳細と出力を確認できます。

## <a name="retrieving-job-status-using-windows-powershell"></a>Windows PowerShell を使用したジョブの状態の取得

[Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) を使用して、Runbook 用に作成されたジョブと、特定のジョブの詳細を取得できます。 [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) を使用して、Windows PowerShell で Runbook を開始すると、結果として作成されたジョブが返されます。 [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) を使用して、ジョブの出力を取得します。

次のサンプル コマンドは、サンプル Runbook の最後のジョブを取得し、その状態、Runbook パラメーターに指定された値、およびジョブの出力を表示します。

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

次の例では、特定のジョブの出力を取得し、各レコードを返します。 いずれかのレコードで例外が発生した場合、値ではなく、その例外が書き出されます。 出力中に正常にログに記録されない可能性がある追加情報を例外で提供できるため、この動作は有用です。

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="get-details-from-activity-log"></a>アクティビティ ログから詳細を取得する

Runbook を開始したユーザーやアカウントなどの他の詳細情報は、Automation アカウントのアクティビティ ログから取得できます。 次の PowerShell の例では、対象の Runbook を最後に実行したユーザーが提供されます。

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>fair share

クラウド内のすべての Runbook 間でリソースを共有するために、Azure Automation は 3 時間以上実行されている任意のジョブを一時的にアンロードまたは停止します。 [PowerShell ベースの Runbook](automation-runbook-types.md#powershell-runbooks) および [Python の Runbook](automation-runbook-types.md#python-runbooks) に対するジョブは、停止されて再起動されずに、ジョブの状態には [停止済み] と表示されます。

実行時間が長いタスクの場合は、[Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior) を使うことをお勧めします。 Hybrid Runbook Worker はフェア シェアによって制限されず、Runbook が実行できる時間に制限はありません。 その他のジョブの[制限](../azure-subscription-service-limits.md#automation-limits)は、Azure サンドボックスと Hybrid Runbook Worker の両方に適用されます。 Hybrid Runbook Worker は 3 時間の fair share 制限では制限されていないものの、このワーカー上で実行される Runbook は、予期しないローカル インフラストラクチャの問題からの再起動の動作をサポートするように、今後も開発していく必要があります。

もう 1 つのオプションは、子 Runbook を使用して Runbook を最適化することです。 Runbook によって多数のリソース上で同じ関数をループ処理する場合 (複数のデータベースでデータベース操作を行うなど)、その関数を[子 Runbook](automation-child-runbooks.md) に移動して、[Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) コマンドレットで呼び出すことができます。 これらの各子 Runbook は、別々のプロセスで並列に実行されるため、親 Runbook が完了するまでの合計時間が減ります。 お使いの Runbook で [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) コマンドレットを使用すると、子 Runbook の完了後に実行が必要な操作がある場合、各子のジョブの状態を確認できます。

## <a name="next-steps"></a>次の手順

* 「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」で、Runbook を開始するさまざまな方法を確認します
