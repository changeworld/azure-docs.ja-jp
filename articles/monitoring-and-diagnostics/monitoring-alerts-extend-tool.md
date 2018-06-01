---
title: OMS ポータルから Azure にアラートを拡張 (コピー) する方法 | Microsoft Docs
description: OMS から Azure アラートへのアラートの拡張をユーザーが自主的に行うことのできるツールと API について説明します。
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 241ac027a0606f901f51d6a20b9a48a2cf7a9fcf
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166184"
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>OMS から Azure にアラートを拡張 (コピー) する方法
**2018 年 5 月 14 日**以降、[Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) で構成されているアラートを使っているすべてのユーザーは、Azure に拡張される予定です。 Azure に拡張されたアラートは、OMS と同じように動作します。 監視機能も変わりません。 OMS で作成したアラートを Azure に拡張すると、多くのメリットがあります。 OMS から Azure へのアラートの拡張の利点とプロセスについて詳しくは、「[OMS から Azure にアラートを拡張する](monitoring-alerts-extend.md)」をご覧ください。

> [!NOTE]
> 2018 年 5 月 14 日以降、Microsoft は Azure にアラートを自動拡張するプロセスを開始します。 この日にすべてのワークスペースとアラートが拡張されるわけではありません。今後数週間かけて段階的に自動的にアラートを拡張します。 そのため、2018 年 5 月 14 日にすぐに OMS ポータルのアラートが Azure に自動拡張されず、まだ以下のオプションを使ってアラートを手動拡張する場合があります。

OMS から Azure にアラートをすぐに移行することを希望するユーザーは、以下で説明するオプションのいずれかを使って実行できます。

## <a name="option-1---using-oms-portal"></a>オプション 1 - OMS ポータルを使用する
OMS ポータルから Azure へのアラートの拡張を自主的に開始するには、次の手順のようにします。

1. OMS ポータルの [概要] ページで、[設定]、[アラート] セクションの順に移動します。 下図で強調表示されている [Azure に拡張] ボタンをクリックします。

    ![拡張オプションが含まれる OMS ポータルのアラート設定ページ](./media/monitor-alerts-extend/ExtendInto.png)

2. ボタンをクリックすると、3 ステップのウィザードが表示され、最初のステップではプロセスの詳細が示されます。 [次へ] をクリックして続行します。

    ![OMS ポータルから Azure にアラートを拡張する - ステップ 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. 2 番目のステップでは、OMS ポータルのアラートに対して提案される変更の概要が、適切な[アクション グループ](monitoring-action-groups.md)の一覧により示されます。 複数のアラートに同様のアクションがある場合は、それらのすべてを 1 つのアクション グループに関連付けることが提案されます。  提案されるアクション グループの名前は、*WorkspaceName_AG_#Number* という名前付け規則に従います。 続行するには、[次へ] をクリックします。
画面の例を次に示します。

    ![OMS ポータルから Azure にアラートを拡張する - ステップ 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. ウィザードの最後のステップでは、前の画面で示したように、新しいアクション グループを作成し、それをアラートと関連付けることによって、Azure へのすべてのアラートの拡張をスケジュールするよう OMS ポータルに指示できます。 続行するには、[完了] をクリックし、プロンプトでプロセスの開始を確認します。 必要に応じて、ユーザーは処理終了時に OMS ポータルがレポートを送信するメール アドレスを指定することもできます。

    ![OMS ポータルから Azure にアラートを拡張する - ステップ 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. ウィザードが終了すると、[アラートの設定] ページに制御が戻り、[Azure に拡張] オプションは表示されなくなります。 バックグラウンドで、OMS ポータルは Log Analytics のアラートを Azure に拡張するスケジュールを設定します。これには少し時間がかかることがあり、操作が始まると、しばらくの間、OMS ポータルのアラートを変更できなくなります。 現在の状態がバナーに表示され、ステップ 4 でメール アドレスを指定した場合は、バックグラウンド プロセスがすべてのアラートを Azure に正常に拡張すると、ユーザーは通知を受け取ります。 

6. Azure に正常に拡張された後でも、アラートは引き続き OMS ポータルに表示されます。

    ![OMS ポータルのアラートを Azure に拡張した後](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>オプション 2 - API を使用する
OMS ポータルのアラートを Azure に拡張するプロセスをプログラムで制御または自動化したいユーザーのため、新しい AlertsVersion API が Log Analytics で提供されています。

Log Analytics の AlertsVersion API は RESTful であり、Azure Resource Manager REST API を使用してアクセスできます。 このドキュメントでは、Azure Resource Manager API の呼び出しを簡略化するオープン ソースのコマンド ライン ツールである [ARMClient](https://github.com/projectkudu/ARMClient) を使用して PowerShell コマンド ラインから API にアクセスする例を示します。 API には、ARMClient や PowerShell を使う以外にもさまざまな方法でアクセスできます。 API の結果は JSON 形式で出力されるため、結果をプログラムによりさまざまな方法で使うことができます。

API で GET を使うことにより、結果に含まれる変更提案の要約を、OMS ポータルのアラートに対する適切な[アクション グループ](monitoring-action-groups.md)のリストとして、JSON 形式で取得できます。 複数のアラートに同様のアクションがある場合は、それらのすべてを 1 つのアクション グループに関連付けを作成することが提案されます。  提案されるアクション グループの名前は、*WorkspaceName_AG_#Number* という名前付け規則に従います。

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> API に対して GET を呼び出しても、OMS ポータルのアラートは Azure に拡張されません。 提案される変更の要約が応答として提供されるだけです。 これらの変更を確認して Azure にアラートを拡張するには、API に対して POST 呼び出しを実行する必要があります。

API への GET 呼び出しが成功すると、200 OK の応答と共に、アラートの JSON 一覧と提案されたアクション グループが提供されます。 応答のサンプルは次のとおりです。

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
指定したワークスペースにアラートがない場合は、GET 操作に対する 200 OK の応答と共に、次のような JSON が返ります。

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

指定したワークスペースのすべてのアラートが Azure に既に拡張されている場合は、GET 呼び出しの応答は次のようになります。
```json
{
    "version": 2
}
```

OMS ポータルのアラートを Azure に拡張するスケジューリングを開始するには、API に対して POST を実行します。 この呼び出し/コマンドを行うと、ユーザーの意図が確認され、OMS ポータルのアラートを Azure に拡張することが承認されて、API への GET 呼び出しの応答で示されている変更が実行されます。 必要に応じて、ユーザーは、OMS ポータルから Azure にアラートを拡張するスケジュールされたバックグラウンド処理が正常に完了したら、OMS ポータルがレポートをメールするメール アドレスの一覧を指定できます。

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> システムで行われた変更によっては、Azure への OMS ポータル アラートの拡張の結果が、GET によって提供される要約と異なる場合があります。 スケジューリングされると、OMS ポータルのアラートは一時的に編集/変更できなくなり、その間に新しいアラートを作成できます。 

POST が成功した場合は、200 OK の応答と共に次の結果が返ります。
```json
{
    "version": 2
}
```
これは、バージョン 2 で示される Azure にアラートが拡張されたことを示します。 このバージョンは、アラートが Azure に拡張されて、[Log Analytics Search API](../log-analytics/log-analytics-api-alerts.md) での使用に影響がないかどうかを確認するためだけのものです。 アラートが Azure に正常に拡張されると、GET の間に指定されたすべてのメール アドレスに、行われた変更の詳細を含むレポートが送信されます。

また、指定したワークスペースのすべてのアラートが、Azure への拡張を既にスケジューリングされている場合は、POST に対する応答が 403 Forbidden になります。 エラー メッセージを表示したり、拡張プロセスが停止しているかどうかを確認したりするには、GET の呼び出しを実行し、概要と共に返されるエラー メッセージを確認します。

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>トラブルシューティング 
OMS から Azure にアラートを拡張するプロセスでは、システムが必要な[アクション グループ](monitoring-action-groups.md)を作成できないことがあります。 このような場合、エラー メッセージは、アラート セクションのバナーを介して OMS ポータルに表示されます。また、API に対する GET の呼び出しの完了時に表示されます。

次に各エラーの修復手順を説明します。
1. **エラー: サブスクリプションが名前空間 'microsoft.insights' を使用するように登録されていません**。![登録エラー メッセージが表示された OMS ポータルの [アラート設定] ページ](./media/monitor-alerts-extend/ErrorMissingRegistration.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 OMS ワークスペースに関連付けられたサブスクリプションは Azure Monitor (microsoft.insights) 機能を使用できるように登録されていません。そのため OMS は Azure Monitor および Azure アラートにアラートを拡張できません。
    
    b. 解決するには、Powershell、Azure CLI、または Azure Portal を使用して、サブスクリプションに microsoft.insights (Azure Monitor および Azure アラート) の使用を登録します。 詳細については、[リソース プロバイダー登録時のエラーの解決](../azure-resource-manager/resource-manager-register-provider-errors.md)に関する記事を参照してください
    
    c. この記事で説明されている手順に従って問題を解決すると、OMS は翌日の予定された実行時間までにアラートを Azure に拡張します。アクションや開始の必要はありません。
2. **エラー: Scope Lock is present at subscription/resource group level for write operations (サブスクリプション/リソース グループ レベルに書き込み操作のスコープ ロックが存在します)**。![ScopeLock エラー メッセージが表示された OMS ポータルの [アラート設定] ページ](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 スコープ ロックを有効にすると、Log Analytics (OMS) ワークスペースを含むサブスクリプションまたはリソース グループの新しい変更がすべて制限されます。システムはアラートを Azure に拡張 (コピー) することができず、必要なアクション グループを作成することができません。
    
    b. 解決するには、Azure Portal、Powershell、Azure CLI、または API を使用して、ワークスペースを含むサブスクリプションまたはリソース グループの *ReadOnly* ロックを削除します。 詳細については、[リソース ロックの使用方法](../azure-resource-manager/resource-group-lock-resources.md)に関する記事を参照してください。 
    
    c. この記事で説明されている手順に従って問題を解決すると、OMS は翌日の予定された実行時間までにアラートを Azure に拡張します。アクションや開始の必要はありません。

3. **エラー: Policy is present at subscription/resource group level (サブスクリプション/リソース グループ レベルにポリシーが存在します)**。![ポリシー エラー メッセージが表示された OMS ポータルの [アラート設定] ページ](./media/monitor-alerts-extend/ErrorPolicy.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 [Azure Policy](../azure-policy/azure-policy-introduction.md) が適用されると、Log Analytics (OMS) ワークスペースを含むサブスクリプションまたはリソース グループの新しいリソースがすべて制限されます。システムはアラートを Azure に拡張 (コピー) することができず、必要なアクション グループを作成することができません。
    
    b. これを解決するには、ワークスペースを含むサブスクリプションまたはリソース グループでの新しいリソースの作成を妨げている *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* エラーの原因であるポリシーを編集します。 Azure portal、Powershell、Azure CLI、または API を使って、障害の原因になっている適切なポリシーを検索するアクションを監査できます。 詳しくは、[アクティビティ ログを見てアクションを監査する](../azure-resource-manager/resource-group-audit.md)方法についての記事をご覧ください。 
    
    c. この記事で説明されている手順に従って問題を解決すると、OMS は翌日の予定された実行時間までにアラートを Azure に拡張します。アクションや開始の必要はありません。


## <a name="next-steps"></a>次の手順

* 新しい[Azure のアラート機能のエクスペリエンス](monitoring-overview-unified-alerts.md)の詳細について学習する。
* [Azure Alerts のログ アラート](monitor-alerts-unified-log.md)について学習する。
