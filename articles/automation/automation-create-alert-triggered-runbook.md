---
title: "Automation Runbook で Azure のアラートに応答する | Microsoft Docs"
description: "Azure のアラートが発生したときに Runbook の実行をトリガーする方法について説明します。"
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Automation Runbook で Azure のアラートに応答する

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) は、Microsoft Azure のほとんどのサービス向けにベース レベルのメトリックとログを提供します。 [アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)またはクラシック アラートから Azure Automation Runbook を呼び出して、アラートに基づくタスクを自動化することができます。 この記事では、これらのアラートに基づいて Runbook を構成および実行する方法を示します。

## <a name="alert-types"></a>アラートの種類

Runbook は、3 種類のアラートすべてでサポートされる操作です。 アラートが Runbook を呼び出すとき、実際の呼び出しは webhook に対する HTTP POST 要求です。 POST 要求の本文には、アラートに関する有用なプロパティを格納する JSON 形式のオブジェクトが含まれています。 次の表には、各アラートの種類のペイロード スキーマへのリンクが含まれています。

|アラート:  |[説明]|ペイロード スキーマ  |
|---------|---------|---------|
|[従来のメトリック アラート](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |プラットフォームレベルのメトリックが特定の条件 (たとえば、VM の CPU % が過去 5 分間で 90 を超えた、など) と一致する場合に通知を受け取ります。| [ペイロード スキーマ](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[アクティビティ ログ アラート](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Azure アクティビティ ログの新しいイベントが特定の条件 (たとえば、myProductionResourceGroup で "VM の削除" 操作が発生した場合や、状態が "アクティブ" の新しいサービス正常性イベントが表示された場合など) と一致する場合に通知を受け取ります。| [ペイロード スキーマ](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[ほぼリアルタイムのメトリック アラート](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |1 つまたは複数のプラットフォーム レベルのメトリックが指定された条件 (例: 過去 5 分間の VM の CPU % が 90 を超え、Network In が 500 MB を超えている) を満たしている場合に、メトリック アラートよりも早く通知を受け取ります。| [ペイロード スキーマ](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

各アラートで提供されるデータは異なるため、各アラートを異なる方法で処理する必要があります。 次のセクションでは、これらのさまざまな種類のアラートを処理する Runbook を作成する方法について説明します。

## <a name="create-a-runbook-to-handle-alerts"></a>アラートを処理する Runbook を作成する

アラートでオートメーションを使用するには、Runbook に渡されるアラートの JSON ペイロードを管理するためのロジックを持つ Runbook が必要です。 次の例の Runbook が Azure アラートから呼び出される必要があります。 前のセクションで説明したように、アラートの種類ごとにスキーマは異なります。 スクリプトは、アラートから `WebhookData` Runbook 入力パラメーターの webhook データを取り込み、JSON ペイロードを評価して、使用されたアラートの種類を特定します。 次の例は、VM からのアラートに使用されます。 ペイロードから VM のデータを取得し、その情報を使用して VM を停止します。 接続は、Runbook が実行される Automation アカウントで設定する必要があります。

Runbook は、VM に対する管理アクションを実行するために、 **AzureRunAsConnection** [実行アカウント](automation-create-runas-account.md)を使用して Azure を認証します。

次の PowerShell スクリプトは、さまざまなリソースで使用するために変更できます。

次の例を使用して、サンプル PowerShell で **Stop-AzureVmInResponsetoVMAlert** という Runbook を作成します。

1. Automation アカウントを開きます。
1. **[プロセス オートメーション]** の **[Runbook]** をクリックします。 Runbook の一覧が表示されます。
1. 一覧の上部にある **[Runbook の追加]** ボタンをクリックします。 **[Runbook の追加]** ページで **[簡易作成]** を選択します。
1. Runbook の **[名前]** に「Stop-AzureVmInResponsetoVMAlert」と入力し、**[Runbook の種類]** で **[PowerShell]** を選択します。 **Create** をクリックしてください。
1. 編集ウィンドウに、次の PowerShell の例をコピーします。 **[発行]** をクリックして Runbook を保存し、発行します。

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

.NOTES
   AUTHOR: Azure Automation Team
   LASTEDIT: 2017-11-22
#>

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {
        # This is the near-real-time Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $ResourceName = $AlertContext.resourceName
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
        # This is the Activity Log Alert schema
        $AlertContext = [object] (($WebhookBody.data).context).activityLog
        $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq $null) {
        # This is the original Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $status = $WebhookBody.status
    }
    else {
        # Schema not supported
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    Write-Verbose "status: $status" -Verbose
    if ($status -eq "Activated")
    {
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId
        Write-Verbose "resourceType: $ResourceType" -Verbose
        Write-Verbose "resourceName: $ResourceName" -Verbose
        Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
        Write-Verbose "subscriptionId: $SubId" -Verbose

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
            Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
            $ConnectionAssetName = "AzureRunAsConnection"
            Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Verbose "Authenticating to Azure with service principal." -Verbose
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>アクション グループを作成する

アクション グループとは、アラートに基づいて実行されるアクションのコレクションです。 Runbook は、アクション グループで提供されるさまざまなアクションの 1 つにすぎません。

1. ポータルで、**[監視]** を選択します。

1. **[設定]** で **[アクション グループ]** を選択します。

1. **[アクション グループの追加]** を選択し、フィールドに入力します。

1. [アクション グループ名] ボックスおよび [短い名前] ボックスに名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。

1. [サブスクリプション] ボックスには、現在のサブスクリプションが自動入力されます。 このサブスクリプションにアクション グループが保存されます。
アクション グループが保存されるリソース グループを選択します。

この例では、Runbook アクションと通知アクションの 2 つのアクションを作成します。

### <a name="runbook-action"></a>Runbook アクション

次の手順では、アクション グループ内に Runbook アクションを作成します。

1. **[アクション]** で、アクションの名前を指定します。

1. **[アクションの種類]** で **[Automation Runbook]** を選択します。

1. **[詳細]** で、**[詳細の編集]** を選択します。

1. **[Runbook の構成]** ページの **[Runbook ソース]**.で **[ユーザー]** を選択します。

1. **[サブスクリプション]** と **[Automation アカウント]** を選択し、最後に、前の手順で作成した "Stop-AzureVmInResponsetoVMAlert" という Runbook を選択します。

1. 完了したら、**[OK]** をクリックします。

### <a name="notification-action"></a>通知アクション

次の手順では、アクション グループ内に 通知アクションを作成します。

1. **[アクション]** で、アクションの名前を指定します。

1. **[アクションの種類]** で **[電子メール]** を選択します。

1. **[詳細]** で、**[詳細の編集]** を選択します。

1. **[電子メール]** ページで、通知を受ける電子メール アドレスを入力し、**[OK]** をクリックします。 Runbook だけでなく電子メール アドレスもアクションとして追加すると、Runbook が起動されたときに通知を受け取れるので便利です。 アクション グループは次の図のようになります。

   ![[アクション グループの追加] ページ](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. **[OK]** をクリックしてアクション グループを作成します。

アクション グループが作成されたら、[アクティビティ ログ アラート](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)または[ほぼリアルタイムのアラート](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)を作成して、作成したアクション グループを使用できます。

## <a name="classic-alert"></a>クラシック アラート

クラシック アラートはメトリックに基づいており、アクション グループを使用しませんが、クラシック アラートに基づく Runbook アクションがあります。 クラシック アラートを作成するには、次の手順に従います。

1. **[+ メトリック アラートの追加]** を選択します。

1. メトリック アラートに 'myVMCPUAlert' という名前を付け、そのアラートの簡単な説明を入力します。

1. メトリック アラートの [条件] を 'より大きい' に設定し、[しきい値] を '10' に設定して、[期間] を '過去 5 分間' に設定します。

1. **[アクションの実行]** で、**[このアラートから Runbook を実行します]** を選択します。

1. **[Runbook の構成]** ページの **[Runbook ソース]**.で **[ユーザー]** を選択します。 Automation アカウントを選択し、**Stop-AzureVmInResponsetoVMAlert** Runbook を選択します。 **[OK]** をクリックし、もう一度 **[OK]** をクリックしてアラート ルールを保存します。

## <a name="next-steps"></a>次の手順

* webhook を使用して Automation Runbook を開始する方法の詳細については、[webhook を使用した Runbook の開始](automation-webhooks.md)に関する記事をご覧ください。
* Runbook を開始するさまざまな方法の詳細については、[Runbook の開始](automation-starting-a-runbook.md)に関する記事をご覧ください。
* アクティビティ ログ アラートの構成方法については、「[アクティビティ ログ アラートの作成](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)」をご覧ください。
* ほぼリアルタイムのアラートの作成方法については、[Azure Portal でのアラート ルールの作成](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)に関する記事をご覧ください。