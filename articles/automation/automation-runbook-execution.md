---
title: Azure Automation での Runbook の実行
description: Azure Automation で Runbook が処理される方法の詳細について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3dfe16cc09f0453aef8adf8bf87a00aebd2054bc
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214637"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation での Runbook の実行

Azure Automation で runbook を開始するときに、ジョブが作成されます。 ジョブは、Runbook の単一の実行インスタンスです。 各ジョブを実行する Azure Automation ワーカーが割り当てられます。 ワーカーは複数の Azure アカウントで共有されるが、さまざまな Automation アカウントからのジョブは互いに分離されます。 ジョブに対する要求をどのワーカーで処理するかを制御することはできません。 1 つの Runbook で、複数のジョブを同時に実行することができます。 同じ Automation アカウントからのジョブの実行環境を再利用できます。 Azure Portal で Runbook の一覧を表示すると、各 Runbook に対して起動されたすべてのジョブの状態が一覧表示されます。 それぞれの状態を追跡するために、Runbook ごとにジョブの一覧を表示できます。 ジョブのさまざまな状態の説明については、「[ジョブの状態](#job-statuses)」をご覧ください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

次の図に、[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) と [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) のための Runbook ジョブのライフサイクルを示します。

![ジョブの状態 - PowerShell Workflow](./media/automation-runbook-execution/job-statuses.png)

次の図に、 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)のための Runbook ジョブのライフサイクルを示します。

![ジョブの状態 - PowerShell スクリプト](./media/automation-runbook-execution/job-statuses-script.png)

ジョブは、Azure サブスクリプションに接続することにより Azure リソースにアクセスします。 データ センター内のリソースにパブリック クラウドからアクセスできる場合、ジョブはそれらのリソースにのみアクセスします。

## <a name="job-statuses"></a>ジョブの状態

次の表には、ジョブが取り得るさまざまな状態を説明します。

| Status | 説明 |
|:--- |:--- |
| 完了 |ジョブは正常に完了しました。 |
| 失敗 |[グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md)では、Runbook のコンパイルが失敗しました。 [PowerShell スクリプト Runbook](automation-runbook-types.md)では、Runbook の開始に失敗したか、ジョブで例外が発生しました。 |
| 失敗、リソースを待機中 |ジョブは [fair share](#fair-share) の限界に 3 回到達し、毎回、同じチェックポイントから、または Runbook の先頭から起動したために、失敗しました。 |
| キューに登録済み |ジョブは Automation ワーカー上のリソースが使用できるようになるのを待機しています。そうなれば、ジョブを起動できます。 |
| 開始中 |ジョブがワーカーに割り当てられており、システムがジョブを起動しているところです。 |
| 再開中 |システムは、ジョブが停止された後、そのジョブを再開しているところです。 |
| 実行中 |ジョブは実行中です。 |
| 実行中、リソースを待機中 |ジョブは [fair share](#fair-share) 制限に達したためにアンロードされました。 ジョブは最後のチェックポイントからすぐに再開します。 |
| 停止済み |ジョブは完了した後、ユーザーによって停止されました。 |
| 停止中 |システムがジョブを停止させているところです。 |
| Suspended |ユーザーか、システムか、または Runbook 内のコマンドによってジョブは中断されました。 中断されているジョブは、再度起動することができ、最後のチェックポイントから、チェックポイントがない場合は Runbook の先頭から再開します。 Runbook は、例外が発生した場合にシステムによってのみ中断されます。 既定では、ErrorActionPreference は、エラーでもジョブの実行を継続することを意味する **Continue** に設定されています。 このユーザー設定変数を **Stop** に設定すると、エラー発生時にジョブは中断します。 [グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md) のみに適用されます。 |
| 中断中 |ユーザーの要求を受けてシステムはジョブを中断しようとしています。 Runbook は、次のチェックポイントに到達してからでないと、中断できません。 Runbook は、次のチェックポイントに到達してからでないと、中断できません。 [グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md) のみに適用されます。 |

## <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal を使用したジョブの状態の表示

Azure Portal では、すべての Runbook ジョブの状態の概要や、特定の Runbook ジョブの詳細を表示できます。また、Log Analytics ワークスペースとの統合を構成して、Runbook ジョブの状態やジョブ ストリームを転送することもできます。 Log Analytics との統合の詳細については、「[Automation から Log Analytics へのジョブの状態とジョブ ストリームの転送](automation-manage-send-joblogs-log-analytics.md)」を参照してください。

### <a name="automation-runbook-jobs-summary"></a>Automation Runbook ジョブの概要

選択した [Automation アカウント] の右にある **[ジョブの統計情報]** タイルで、選択した Automation アカウントの Runbook ジョブすべての概要を確認できます。

![[ジョブの統計情報] タイル](./media/automation-runbook-execution/automation-account-job-status-summary.png)が必要です。

このタイルでは、実行されたすべてのジョブの数を確認できるほか、その状態がグラフィカルに表示されます。

タイルをクリックすると、**[ジョブ]** ブレードが表示されます。このブレードでは、実行されたジョブの一覧と、そのジョブの状態、およびジョブの開始時刻と完了時刻を確認できます。

![Automation アカウントの [ジョブ] ブレード](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

ジョブのリストをフィルター処理するには、**[ジョブのフィルター]** を選択します。特定の Runbook、ジョブの状態でフィルターしたり、検索対象の日付/時刻の範囲をドロップダウン リストから選択したりできます。

![[フィルター] の [ジョブの状態]](./media/automation-runbook-execution/automation-account-jobs-filter.png)

また、特定の Runbook について、ジョブ概要の詳細情報を表示することもできます。それには、Automation アカウントで **[Runbook]** ブレードからその Runbook を選択し、**[ジョブ]** タイルを選択します。 これにより、**[ジョブ]** ブレードが表示され、そのブレードでジョブ レコードをクリックすると、そのジョブの詳細と出力が表示されます。

![Automation アカウントの [ジョブ] ブレード](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>ジョブの概要

特定の Runbook 用に作成されたすべてのジョブと、それらのジョブの最新の状態を一覧にして表示できます。 この一覧は、ジョブの状態とジョブに最後に変更を加えた日付の範囲とによってフィルター処理することができます。 詳細な情報と出力を表示するには、ジョブの名前をクリックします。 ジョブの詳細表示には、そのジョブに指定された Runbook パラメーターの値が含まれます。

次の手順を使用して Runbook のジョブを表示します。

1. Azure Portal で、**[Automation]** を選択し、次に Automation アカウントの名前を選択します。
2. ハブから **[Runbook]** を選択し、**[Runbook]** ブレードで、一覧から 1 つの Runbook を選択します。
3. 選択した Runbook のブレードで、**[ジョブ]** タイルをクリックします。
4. 一覧にあるいずれかのジョブをクリックすると、Runbook の [ジョブの詳細] ブレードで詳細と出力を確認できます。

## <a name="retrieving-job-status-using-windows-powershell"></a>Windows PowerShell を使用したジョブの状態の取得

[Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) を使用して、Runbook 用に作成されたジョブと、特定のジョブの詳細を取得できます。 [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) を使用して、Windows PowerShell で Runbook を開始すると、結果として作成されたジョブが返されます。 [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt619440.aspx) を使用して、ジョブの出力を取得します。

次のサンプル コマンドは、サンプル Runbook の最後のジョブを取得し、その状態、Runbook パラメーターに指定された値、およびジョブの出力を表示します。

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

次の例では、特定のジョブの出力を取得し、各レコードを返します。 いずれかのレコードで例外が発生した場合、値ではなく、その例外が書き出されます。 例外は、出力中に正常にログに記録されない可能性がある追加情報を提供できるため有用です。

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

クラウド内のすべての Runbook 間でリソースを共有するために、Azure Automation は任意のジョブが 3 時間実行された後で一時的にそのジョブをアンロードします。 この期間中、[PowerShell ベースの Runbook](automation-runbook-types.md#powershell-runbooks) のジョブは停止され、再開されません。 ジョブの状態には、「**停止**」と表示されます。 この種類の Runbook はチェックポイントをサポートしていないため、常に最初から再開されます。

[PowerShell ワークフローベースの Runbook](automation-runbook-types.md#powershell-workflow-runbooks) は、最後の[チェックポイント](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints)から再開されます。 3 時間の実行後、Runbook ジョブはサービスによって中断され、サービスの状態には「**実行中、リソース待ち**」と表示されます。 サンドボックスが利用可能になると、Automation サービスによって Runbook が自動的に再起動され、最後のチェックポイントから再開されます。 これは、中断/再起動のための通常の PowerShell ワークフローの動作です。 Runbook が再度 3 時間のランタイムを超過した場合、プロセスは最大で 3 回まで繰り返されます。 3 度目の再起動の後、Runbook がまだ 3 時間で完了しない場合は、Runbook ジョブは失敗し、ジョブの状態には「**失敗、リソース待ち**」と表示されます。 この場合は、エラー発生時に次の例外を受信します。

"*ジョブは同じチェックポイントから繰り返し削除されたため、実行を継続できません。時間のかかる操作を実行する場合は、Runbook でその状態が維持されることを確認してください。*"

これは、Runbook が次のチェックポイントに進むことができず再度アンロードされない場合に、Runbook が完了せずに無期限に実行されないようにサービスを保護するためのものです。

Runbook がチェックポイントを持っていないか、またはアンロードされる前にジョブがまだ最初のチェックポイントに達していない場合、ジョブは最初から再開されます。

実行時間が長いタスクの場合は、[Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior) を使うことをお勧めします。 Hybrid Runbook Worker はフェア シェアによって制限されず、Runbook が実行できる時間に制限はありません。

PowerShell Workflow Runbook を Azure で使っている場合は、Runbook を作成するときに、2 つのチェックポイント間で任意のアクティビティを実行するのにかかる時間が 3 時間を超えないことを確認してください。 この 3 時間の制限に達したり、実行に時間のかかる操作を分割したりしないように、Runbook にチェックポイントを追加することが必要な場合があります。 たとえば、Runbook が大規模な SQL データベースで再インデックス化を実行する可能性があります。 この単一処理がフェア シェア制限内で完了しない場合、ジョブはアンロードされ、先頭から再開されます。 この場合は再インデックス化処理を複数のステップに分割します。たとえば、一度に 1 つのテーブルを再インデックス化し、各処理の後にチェックポイントを挿入します。こうすれば、最後の処理が完了した後、ジョブは再開することが可能です。

## <a name="next-steps"></a>次の手順

* 「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」で、Runbook を開始するさまざまな方法を確認します
