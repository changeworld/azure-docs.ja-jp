---
title: PowerShell を使用して Azure サービスのクラシック アラートを作成する | Microsoft Docs
description: 指定した条件が満たされたときに、電子メール、通知、または Websites URL (webhook) の呼び出し、またはオートメーションをトリガーします。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286201"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>PowerShell での Azure サービス アラートの作成
> [!div class="op_single_selector"]
> * [ポータル](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> この記事では、古いクラシック メトリック アラートの作成方法について説明します。 Azure Monitor では、[新しい、より優れたメトリック アラート](monitoring-near-real-time-metric-alerts.md)がサポートされるようになりました。 これらのアラートでは複数のメトリックを監視でき、次元メトリックのアラートが許可されます。 新しいメトリック アラートの PowerShell サポートはまもなく提供されます。
>
>

この記事では、PowerShell を使用して Azure クラシック メトリック アラートを設定する方法について説明します。  

Azure サービスのメトリックに基づいてアラートを受信したり、Azure で発生したイベントのアラートを受信したりすることができます。

* **メトリック値**: アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、その条件を満たさなくなったときの両方でトリガーされます。    
* **アクティビティ ログ イベント**: アラートは*すべて*のイベントに対して、または特定のイベントが発生したときにトリガーされます。 アクティビティ ログ アラートの詳細については、「[アクティビティ ログ アラート (クラシック) の作成](monitoring-activity-log-alerts.md)」を参照してください。

クラシック メトリック アラートがトリガーされたときに実行されるように構成できるタスクは次のとおりです。

* サービスの管理者/共同管理者に電子メール通知を送信する。
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す。
* Azure Runbook の実行を開始する (Azure portal からのみ)。

次の場所からアラート ルールを構成したり、その情報を取得したりすることができます。 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure コマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

詳細については、「```Get-Help```」と入力し、その後にヘルプが必要な PowerShell コマンドを入力してください。

## <a name="create-alert-rules-in-powershell"></a>PowerShell でアラート ルールを作成する
1. Azure にサインインします。   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. 使用できるサブスクリプションの一覧を取得します。 適切なサブスクリプションを使用していることを確認します。 適切なサブスクリプションではない場合は、`Get-AzureRmSubscription` からの出力を使用して適切なサブスクリプションにアクセスします。

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. リソース グループの既存のルールを一覧表示するには、次のコマンドを使用します。

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. ルールを作成するには、最初に重要な情報をいくつか入手する必要があります。

    - アラートを設定するリソースの**リソース ID**。
    - そのリソースに使用できる**メトリック定義**。

     リソース ID は、Azure portal を使用して取得できます。 リソースが既に作成されている場合は、ポータルでリソースを選択します。 次のブレードで **[設定]** セクションの **[プロパティ]** を選択します。 次のブレードに "**リソース ID**" フィールドがあります。 
     
     また、[Azure Resource Explorer](https://resources.azure.com/) を使用してリソース ID を取得することもできます。

     Web アプリのリソース ID の例を次に示します。

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     `Get-AzureRmMetricDefinition` を使用して、特定のリソースのメトリック定義の一覧を表示できます。

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     次の例では、そのメトリックの名前と単位を含むテーブルを生成します。

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Get-AzureRmMetricDefinition の使用できるオプションの一覧を取得するには、`Get-Help Get-AzureRmMetricDefinition -Detailed` を実行します。
5. 次の例では、Web サイト リソースにアラートを設定します。 アラートは、5 分間を通してすべてのトラフィックを受け取り続けたときにトリガーされ、その後 5 分間トラフィックを受け取らなくなった時点で再度トリガーされます。

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. アラートがトリガーされたときに webhook を作成するか、電子メールを送信するには、最初に電子メールまたは webhook を作成します。 次に示すように、その後すぐに -Actions タグを使用して、ルールを作成します。 既に作成されているルールに webhook または電子メールを関連付けることはできません。

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. 個々のルールを見て、アラートが正しく作成されたことを確認します。

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. アラートを削除します。 この記事で作成したルールは、次のコマンドで削除されます。

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>次の手順
* 収集および監視できる情報の種類など、[Azure での監視の概要](monitoring-overview.md)情報を入手します。
* [アラートで webhook を構成する](insights-webhooks-alerts.md)方法を確認します。
* [アクティビティ ログ イベントでアラートを構成する](monitoring-activity-log-alerts.md)方法を確認します。
* [Azure Automation Runbooks](../automation/automation-starting-a-runbook.md) の詳細情報を確認します。
* [診断ログ収集の概要](monitoring-overview-of-diagnostic-logs.md) 情報を入手して、サービスに関する詳細な頻度の高いメトリックを収集します。
* [メトリック収集の概要](insights-how-to-customize-monitoring.md) 情報を入手して、サービスの可用性と応答性を確認します。
