---
title: Azure Automation Runbook をトリガーするアラートを使用する
description: この記事では、Azure のアラートが発生したときに Runbook の実行をトリガーする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2584390610aaef5ddd8364d33f3c065ecf3bd321
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443834"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Azure Automation Runbook をトリガーするアラートを使用する

[Azure Monitor](../azure-monitor/overview.md) を使用して Azure のほとんどのサービスのベース レベルのメトリックとログを監視します。 [アクション グループ](../azure-monitor/alerts/action-groups.md)を使用して Azure Automation Runbook を呼び出し、アラートに基づくタスクを自動化することができます。 この記事では、アラートを使用して Runbook を構成および実行する方法を示します。


## <a name="prerequisites"></a>前提条件

* 少なくとも 1 つのユーザー割り当てマネージド ID を持つ Azure Automation アカウント。 詳細については、「[Azure Automation アカウントのユーザー割り当てマネージド ID を使用する](./add-user-assigned-identity.md)」を参照してください。
* Az モジュール: `Az.Accounts` および `Az.Compute` が Automation アカウントにインポートされている。 詳細については、「[Az モジュールをインポートする](./shared-resources/modules.md#import-az-modules)」を参照してください。
* [Azure 仮想マシン](../virtual-machines/windows/quick-create-powershell.md)。
* [Azure Az PowerShell モジュール](/powershell/azure/new-azureps-module-az)がマシンにインストールされている。 インストールまたはアップグレードするには、[Azure Az PowerShell モジュールをインストールする方法](/powershell/azure/install-az-ps)に関するページを参照してください。
* [Automation Runbook](./manage-runbooks.md) に関する一般的な理解。

## <a name="alert-types"></a>アラートの種類

Automation Runbook は、次の 3 つの種類のアラートで使用できます。

* 共通アラート
* アクティビティ ログ アラート
* ほぼリアルタイムのメトリック アラート

> [!NOTE]
> 共通アラート スキーマを使用すると、Azure のアラート通知の使用エクスペリエンスを標準化できます。 これまで、Azure の 3 種類のアラート (メトリック、ログ、アクティビティ ログ) には、独自のメール テンプレートや Webhook スキーマ などがありました。詳細については、「[共通アラート スキーマ](../azure-monitor/alerts/alerts-common-schema.md)」を参照してください

アラートが Runbook を呼び出すとき、実際の呼び出しは webhook に対する HTTP POST 要求です。 POST 要求の本文には、アラートに関する有用なプロパティを格納する JSON 形式のオブジェクトが含まれています。 次の表は、各アラートの種類のペイロード スキーマへのリンクを示しています。

|アラート:  |説明|ペイロード スキーマ  |
|---------|---------|---------|
|[共通アラート](../azure-monitor/alerts/alerts-common-schema.md)|今日の Azure でのアラート通知の使用エクスペリエンスを標準化する共通アラート スキーマ。|共通アラートのペイロード スキーマ。|
|[アクティビティ ログ アラート](../azure-monitor/alerts/activity-log-alerts.md) |Azure のアクティビティ ログ内の新しいイベントのいずれかが特定の条件と一致する場合に、通知が送信されます。 たとえば、`Delete VM` 操作が **myProductionResourceGroup** で発生した場合、または状態が [アクティブ] の新しい Azure Service Health イベントが表示されている場合などです。| [アクティビティ ログ アラートのペイロード スキーマ](../azure-monitor/alerts/activity-log-alerts-webhook.md)     |
|[ほぼリアルタイムのメトリック アラート](../azure-monitor/alerts/alerts-metric-near-real-time.md) | 1 つまたは複数のプラットフォーム レベルのメトリックが指定した条件を満たす場合に、メトリック アラートよりも速く通知が送信されます。 たとえば、過去 5 分間の VM の **CPU %** の値が 90 より大きく、 **[ネットワーク入力]** の値が 500 MB より大きい場合などです。| [ほぼリアルタイムのメトリック アラートのペイロード スキーマ](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

種類ごとに提供されるデータは異なるため、アラートは種類ごとに異なる方法で処理されます。 次のセクションでは、さまざまな種類のアラートを処理する Runbook を作成する方法について説明します。

## <a name="assign-permissions-to-managed-identities"></a>マネージド ID にアクセス許可を割り当てる

適切な[マネージド ID](./automation-security-overview.md#managed-identities) にアクセス許可を割り当てて、仮想マシンを停止できるようにします。 Runbook では、Automation アカウントのシステム割り当てマネージド ID またはユーザー割り当てマネージド ID のいずれかを使用できます。 以下に、各 ID にアクセス許可を割り当てる手順を示します。 この手順では PowerShell を使用します。 ポータルを使用する場合は、「[Azure portal を使用して Azure ロールを割り当てる](./../role-based-access-control/role-assignments-portal.md)」を参照してください。

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 下の変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "AutomationAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    ```

1. PowerShell コマンドレット [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用して、システム割り当てマネージド ID にロールを割り当てます。

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. ユーザー割り当てマネージド ID にロールを割り当てます。

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity)
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. システム割り当てマネージド ID の場合は、`ClientId` を表示し、後で使用するためにその値を記録します。

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-a-runbook-to-handle-alerts"></a>アラートを処理する Runbook を作成する

アラートで Automation を使用するには、Runbook に渡されるアラートの JSON ペイロードを管理する Runbook が必要です。 次の例の Runbook が Azure アラートから呼び出される必要があります。

前のセクションで説明したように、アラートの種類ごとにスキーマは異なります。 このスクリプトでは、`WebhookData` Runbook 入力パラメーターのアラートから Webhook データを取得します。 次に、スクリプトによって JSON ペイロードが評価され、使用されているアラートの種類が判断されます。

この例では、Azure 仮想マシン (VM) からのアラートを使用します。 ペイロードから VM のデータを取得し、その情報を使用して VM を停止します。 接続は、Runbook が実行される Automation アカウントで設定する必要があります。 アラートを使用して Runbook をトリガーする場合は、トリガーされる Runbook でアラートの状態を確認することが重要です。 アラートの状態が変化するたびに、Runbook がトリガーされます。 アラートには複数の状態があります。最も一般的な 2 つの状態は "アクティブ" と "解決済み" です。 Runbook が複数回実行されないことを確実にするために、Runbook ロジックの状態を確認します。 この記事の例では、状態がアクティブのアラートのみを検索する方法を示しています。

Runbook では、VM に対する管理アクションの実行を Azure で認証するために、Automation アカウントの[システム割り当てマネージド ID](./automation-security-overview.md#managed-identities) が使用されます。 Runbook は、ユーザー割り当てマネージド ID を使用するように簡単に変更することができます。

この例を使用して **Stop-AzureVmInResponsetoVMAlert** と呼ばれる Runbook を作成します。 PowerShell スクリプトは変更して、多くのさまざまなリソースで使用することができます。

1. [Azure portal](https://portal.azure.com/) にサインインし、お使いの Automation アカウントに移動します。

1. **[プロセス オートメーション]** の **[Runbook]** を選択します。

1. **[+ Runbook の作成]** を選択します。
    1. Runbook に `Stop-AzureVmInResponsetoVMAlert` という名前を付けます。
    1. **[Runbook の種類]** ドロップダウン リストから、 **[PowerShell]** を選択します。
    1. **［作成］** を選択します

1. Runbook エディターで、次のコードを貼り付けます。

    ```powershell
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
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }
    
        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose
    
            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose
    
                # Ensures you do not inherit an AzContext in your runbook
                Disable-AzContextAutosave -Scope Process
     
                # Connect to Azure with system-assigned managed identity
                $AzureContext = (Connect-AzAccount -Identity).context
  
                # set and store context
                $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
    
                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -DefaultProfile $AzureContext -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

1. Runbook をシステム割り当てマネージド ID で実行する場合は、コードをそのままにしておきます。 ユーザー割り当てマネージド ID を使用する場合は、次のようにします。
    1. 行 78 から `$AzureContext = (Connect-AzAccount -Identity).context` を削除します。
    1. それを `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` に置き換えた後、
    1. 前に取得したクライアント ID を入力します。

1. **[保存]** 、 **[発行]** の順に選択し、確認を求められたら **[はい]** を選択します。

1. **[Runbook]** ページを閉じて、 **[Automation アカウント]** ページに戻ります。

## <a name="create-the-alert"></a>アラートを作成する

アラートでは、アクション グループが使用されます。アクション グループは、アラートによってトリガーされるアクションのコレクションです。 Runbook は、アクション グループで使用できるさまざまなアクションの 1 つにすぎません。

1. Automation アカウントで、 **[監視]** の下の **[アラート]** を選択します。

1. **[+ 新しいアラート ルール]** を選択して **[アラート ルールの作成]** ページを開きます。

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-alert-rule-portal.png" alt-text="[アラート ルールの作成] ページとサブセクション。":::

1. **[スコープ]** で **[リソースの編集]** を選択します。 

1. **[リソースの選択]** ページで、 **[リソースの種類でフィルター]** ドロップダウン リストから **[仮想マシン]** を選択します。

1. 監視する仮想マシンの横にあるチェック ボックスをオンにします。 次に、 **[完了]** を選択して、 **[アラート ルールの作成]** ページに戻ります。

1. **[条件]** で、 **[条件の追加]** を選択します。

1. **[Select a signal]\(シグナルの選択\)** ページで、検索テキスト ボックスに「`Percentage CPU`」と入力し、結果から **[CPU 使用率]** を選択します。

1. **[シグナル ロジックの構成]** ページの **[しきい値]** の下に、テスト用に初期の低い値 (`5` など) を入力します。 アラートが想定どおりに動作することを確認したら、戻ってこの値を更新することができます。 次に、 **[完了]** を選択して、 **[アラート ルールの作成]** ページに戻ります。

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-signal-logic-portal.png" alt-text="CPU の割合のしきい値の入力。":::
 
1. **[アクション]** の **[アクション グループの追加]** 、 **[+ アクション グループの作成]** の順に選択します。

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-action-group-portal.png" alt-text="[基本] タブが開いている [アクション グループの作成] ページ。":::

1. **[アクション グループの作成]** ページで次の手順を実行します。
    1. **[基本]** タブで、 **[アクション グループ名]** と **[表示名]** を入力します。
    1. **[アクション]** タブの **[名前]** テキスト ボックスに名前を入力します。 次に **[アクションの種類]** ドロップダウン リストから **[Automation Runbook]** を選択し、 **[Runbook の構成]** ページを開きます。
        1. **[Runbook ソース]** の項目で **[ユーザー]** を選択します。  
        1. **[サブスクリプション]** ドロップダウン リストからサブスクリプションを選択します。
        1. **[Automation アカウント]** ドロップダウン リストから、Automation アカウントを選択します。
        1. **[Runbook]** ドロップダウン リストから **[Stop-AzureVmInResponsetoVMAlert]** を選択します。
        1. **[一般的なアラート スキーマを有効にします]** 項目で **[はい]** を選択します。
        1. **[OK]** を選択し、 **[アクション グループの作成]** ページに戻ります。
        
            :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-runbook-portal.png" alt-text="値を使用して Runbook ページを構成します。":::

    1. **[確認と作成]** を選択し、 **[作成]** を選択すると、 **[アラート ルールの作成]** ページに戻ります。

1. **[アラート ルールの詳細]** の **[アラート ルール名]** テキスト ボックス。

1. **[アラート ルールの作成]** を選択します。  このアクション グループは作成した[アクティビティ ログ アラート](../azure-monitor/alerts/activity-log-alerts.md)と[準リアルタイムのアラート](../azure-monitor/alerts/alerts-overview.md)で使用することができます。

## <a name="verification"></a>検証

VM が実行されていることを確認します。 Runbook **Stop-AzureVmInResponsetoVMAlert** に移動し、生成される **[最近のジョブ]** 一覧を確認します。 完了したジョブが表示されたら、そのジョブを選択し、出力を確認します。 また、VM が停止していないかどうかも確認します。

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/job-result-portal.png" alt-text="ジョブからの出力の表示。":::


## <a name="next-steps"></a>次のステップ

* Runbook を開始するさまざまな方法については、[Runbook の開始](./start-runbooks.md)に関する記事をご覧ください。
* アクティビティ ログ アラートの構成方法は、「[アクティビティ ログ アラートの作成](../azure-monitor/alerts/activity-log-alerts.md)」をご覧ください。
* ほぼリアルタイムのアラートの作成方法については、「[Azure Portal でアラート ルールを作成する](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)」をご覧ください。

