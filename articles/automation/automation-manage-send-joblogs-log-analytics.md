---
title: Azure Monitor ログに Azure Automation のジョブ データを転送する
description: この記事では、ジョブの状態と Runbook ジョブ ストリームを Azure Monitor ログに送信して、追加の分析情報や補助的な管理を提供する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: 54f77f55a127cd712d43419eb6a85fd5d93a478c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652177"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Automation から Azure Monitor ログにジョブの状態とジョブ ストリームを転送する

Automation からは、Runbook ジョブの状態とジョブ ストリームを Log Analytics ワークスペースに送信できます。 このプロセスは、ワークスペースのリンク設定を伴わず、完全に独立しています。 ジョブ ログとジョブ ストリームは、Azure Portal または PowerShell を使用してジョブごとに表示できます。これを使用して、簡単な調査を行うことができます。 Azure Monitor ログを使用すると、次のことを行うことができます。

* Automation ジョブに関する情報を得る。
* Runbook ジョブの状態 (失敗、中断など) に基づいて電子メールまたはアラートをトリガーする。
* ジョブ ストリームをまたぐ高度なクエリを記述する。
* Automation アカウントをまたいでジョブどうしを関連付ける。
* ジョブの履歴を時系列で視覚化する。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>前提条件とデプロイに関する考慮事項

Azure Monitor ログへの Automation ログの送信を開始するには、次のものが必要です。

* [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) の最新リリース。
* Log Analytics ワークスペース。 詳細については、[Azure Monitor ログの使用](../log-analytics/log-analytics-get-started.md)に関するページを参照してください
* Azure Automation アカウントのリソース ID。

自分の Azure Automation アカウントのリソース ID を調べるには、次のコマンドを使用します。

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

自分の Log Analytics ワークスペースのリソース ID を調べるには、次の PowerShell コマンドを実行します。

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

複数の Automation アカウントまたはワークスペースがある場合、前のコマンドの出力で、構成する必要がある名前を見つけて、リソース ID の値をコピーします。

1. Azure portal の **[Automation アカウント]** ブレードで Automation アカウントを選択し、 **[すべての設定]** を選択します。 
2. **[すべての設定]** ブレードで、 **[アカウント設定]** の **[プロパティ]** を選択します。  
3. **[プロパティ]** ブレードで、以下の値を記録します。<br> ![Automation アカウントのプロパティ](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)。


## <a name="azure-monitor-log-records"></a>Azure Monitor のログ レコード

Azure Automation の診断により、`AzureDiagnostics` というタグの付いた 2 種類のレコードが Azure Monitor ログに作成されます。 次のセクションの表では、Azure Automation によって生成されるレコードの例と、ログの検索結果に表示されるデータの種類を示します。

### <a name="job-logs"></a>ジョブ ログ

| プロパティ | 説明 |
| --- | --- |
| TimeGenerated |Runbook ジョブが実行された日付と時刻。 |
| RunbookName_s |Runbook の名前。 |
| Caller_s |操作を開始した呼び出し元。 スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。 |
| Tenant_g | 呼び出し元のテナントを識別する GUID。 |
| JobId_g |Runbook ジョブを識別する GUID。 |
| ResultType |Runbook ジョブの状態。 次のいずれかの値になります。<br>- 新規<br>- 作成済み<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 完了 |
| カテゴリ | データの種類の分類。 Automation の場合、値は JobLogs です。 |
| OperationName | Azure で実行された操作の種類。 Automation の場合、値は Job です。 |
| リソース | Automation アカウントの名前 |
| SourceSystem | データを収集するために Azure Monitor ログによって使用されるシステム。 Azure Diagnostics の場合、値は常に "Azure" です。 |
| ResultDescription |Runbook ジョブの結果の状態。 次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました |
| CorrelationId |Runbook ジョブの関連付け GUID。 |
| ResourceId |Runbook の Azure Automation アカウントのリソース ID。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション GUID。 |
| ResourceGroup | Automation アカウントのリソース グループの名前。 |
| ResourceProvider | リソース プロバイダー。 値は MICROSOFT.AUTOMATION です。 |
| ResourceType | リソースの種類。 値は AUTOMATIONACCOUNTS です。 |

### <a name="job-streams"></a>ジョブ ストリーム
| プロパティ | 説明 |
| --- | --- |
| TimeGenerated |Runbook ジョブが実行された日付と時刻。 |
| RunbookName_s |Runbook の名前。 |
| Caller_s |操作を開始した呼び出し元。 スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。 |
| StreamType_s |ジョブ ストリームの種類。 次のいずれかの値になります。<br>\- 進行状況<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細 |
| Tenant_g | 呼び出し元のテナントを識別する GUID。 |
| JobId_g |Runbook ジョブを識別する GUID。 |
| ResultType |Runbook ジョブの状態。 次のいずれかの値になります。<br>- In Progress |
| カテゴリ | データの種類の分類。 Automation の場合、値は JobStreams です。 |
| OperationName | Azure で実行された操作の種類。 Automation の場合、値は Job です。 |
| リソース | Automation アカウントの名前。 |
| SourceSystem | データを収集するために Azure Monitor ログによって使用されるシステム。 Azure Diagnostics の場合、値は常に "Azure" です。 |
| ResultDescription |Runbook からの出力ストリームが含まれる説明。 |
| CorrelationId |Runbook ジョブの関連付け GUID。 |
| ResourceId |Runbook の Azure Automation アカウントのリソース ID。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション GUID。 |
| ResourceGroup | Automation アカウントのリソース グループの名前。 |
| ResourceProvider | リソース プロバイダー。 値は MICROSOFT.AUTOMATION です。 |
| ResourceType | リソースの種類。 値は AUTOMATIONACCOUNTS です。 |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Azure Monitor ログとの統合の設定

1. お使いのコンピューターで、 **[スタート]** 画面から Windows PowerShell を起動します。
2. 次の PowerShell コマンドを実行します。`[your resource ID]` と `[resource ID of the log analytics workspace]` の値は、前のセクションで記録した値に置き換えます。

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

このスクリプトを実行した後、Azure Monitor ログに書き込まれた新しい `JobLogs` や `JobStreams` のレコードが表示され始めるまでに、1 時間ほどかかる場合があります。

ログを表示するには、Log Analytics のログ検索で次のクエリを実行します: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>構成の確認

Automation アカウントから Log Analytics ワークスペースにログが送信されていることを確認するには、次の PowerShell コマンドを使用して、Automation アカウントに診断が正しく構成されていることを確認します。

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

出力で次のことを確認します。

* `Logs` の下の `Enabled` の値が True であること。
* `WorkspaceId` が、Log Analytics ワークスペースの `ResourceId` の値に設定されていること。

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Azure Monitor ログでの Automation ログの確認

Azure Monitor ログへの Automation ジョブ ログの送信を開始したので、次は Azure Monitor ログ内でこれらのログに対して何ができるかを確認しましょう。

ログを表示するには、次のクエリを実行します。`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook ジョブが失敗または中断したときに電子メールを送信する

顧客からの問い合わせとして多いのは、Runbook ジョブに問題が発生したときに電子メールまたはテキストを送信する機能です。

アラート ルールを作成するには、まずアラートを呼び出す Runbook ジョブ レコードに対するログ検索を作成します。 **[アラート]** ボタンをクリックし、アラート ルールを作成して構成します。

1. Log Analytics ワークスペースの [概要] ページで、 **[ログの表示]** をクリックします。
2. クエリ フィールドに次の検索クエリを入力して、アラート用のログ検索クエリを作成します。`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>次のクエリを使用して、Runbook 名でグループ化することもできます。`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   複数の Automation アカウントまたはサブスクリプションからワークスペースへのログをセットアップしてある場合は、サブスクリプションおよび Automation アカウントごとにアラートをグループ化することができます。 Automation アカウント名は `JobLogs` の検索の `Resource` フィールドで確認できます。
3. **[ルールの作成]** 画面を開くには、ページの上部にある **[+ New Alert Rule]\(新しいアラート ルール\)** をクリックします。 アラートの構成オプションについて詳しくは、「[Azure Monitor でのログ アラート](../azure-monitor/platform/alerts-unified-log.md)」をご覧ください。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>エラーが発生したすべてのジョブを特定する

エラーに関するアラートだけでなく、Runbook ジョブが終了しないときにもエラーが表示されます。 このような場合、PowerShell ではエラー ストリームが生成されますが、ジョブが終了しないエラーでは、ジョブの中断や失敗は起こりません。

1. Log Analytics ワークスペースで **[ログ]** をクリックします。
2. クエリ フィールドに、「`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`」と入力します。
3. **[検索]** ボタンをクリックします。

### <a name="view-job-streams-for-a-job"></a>ジョブのジョブ ストリームを確認する

ジョブのデバッグを行っているときに、ジョブ ストリームの確認が必要になることもあります。 次のクエリは、GUID が 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 である 1 つのジョブのすべてのストリームを表示します。

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>ジョブの状態の履歴を確認する

最後に、ジョブの履歴を時系列で視覚化することが必要になる場合があります。 次のクエリを使うと、ジョブの状態を時系列で検索できます。

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics でのジョブの状態の履歴を示すグラフ](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>診断設定の削除

Automation アカウントから診断設定を削除するには、次のコマンドを実行します。

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>まとめ

Automation ジョブの状態とストリーム データを Azure Monitor ログに送信し、次の対応を行うと、Automation ジョブの状態をより適確に理解できます。
+ 問題が発生した場合に通知するアラートの設定。
+ カスタム ビューと検索クエリを使用した Runbook の結果、Runbook ジョブの状態、その他の関連する主要な指標やメトリックの視覚化。

Azure Monitor ログによって、Automation ジョブの状態をさらに詳しく把握でき、インシデントにより迅速に対処できるようになります。

## <a name="next-steps"></a>次のステップ

* Log Analytics のトラブルシューティングについては、[Log Analytics がデータを収集しなくなった場合のトラブルシューティング](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)に関する記事を参照してください。
* 各種検索クエリの作成方法と、Azure Monitor ログでの Automation ジョブ ログの確認方法の詳細については、[Azure Monitor ログでのログ検索](../log-analytics/log-analytics-log-searches.md)に関するページを参照してください。
* Runbook から出力とエラー メッセージを作成および取得する方法については、[Runbook の出力とメッセージ](automation-runbook-output-and-messages.md)に関するページを参照してください。
* Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、[Runbook ジョブの追跡](automation-runbook-execution.md)に関するページを参照してください。
* Azure Monitor ログとデータ収集ソースの詳細については、[Azure Monitor ログにおける Azure Storage データの収集の概要](../azure-monitor/platform/collect-azure-metrics-logs.md)に関するページを参照してください。

