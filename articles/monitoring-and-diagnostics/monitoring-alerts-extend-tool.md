---
title: OMS から Azure へのアラート拡張の開始 | Microsoft Docs
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
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 76b7481223566f16a5da8c08d9d76f2bdb6b542a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="initiate-extending-alerts-from-oms-into-azure"></a>OMS から Azure へのアラート拡張の開始
**2018 年 4 月 23日**以降、[Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) で構成されているアラートを使っているすべてのユーザーは、Azure に拡張される予定です。 Azure に拡張されたアラートは、OMS と同じように動作します。 監視機能も変わりません。 OMS で作成したアラートを Azure に拡張すると、多くのメリットがあります。 OMS から Azure へのアラートの拡張の利点とプロセスについて詳しくは、「[OMS から Azure にアラートを拡張する](monitoring-alerts-extend.md)」をご覧ください。

OMS から Azure にアラートをすぐに移行することを希望するユーザーは、以下で説明するオプションのいずれかを使って実行できます。

## <a name="option-1---using-oms-portal"></a>オプション 1 - OMS ポータルを使用する
OMS から Azure へのアラートの拡張を自主的に開始するには、次の手順のようにします。

1. Operations Management Suite (OMS) の [概要] ページで、[設定]、[アラート] セクションの順に移動します。 下図で強調表示されている [Azure に拡張] ボタンをクリックします。

    ![拡張オプションが含まれる OMS のアラート設定ページ](./media/monitor-alerts-extend/ExtendInto.png)

2. ボタンをクリックすると、3 ステップのウィザードが表示され、最初のステップではプロセスの詳細が示されます。 [次へ] をクリックして続行します。

    ![OMS から Azure にアラートを拡張する - ステップ 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. 2 番目のステップでは、OMS のアラートに対して提案される変更の概要が、適切な[アクション グループ](monitoring-action-groups.md)の一覧により示されます。 複数のアラートに同様のアクションがある場合は、それらのすべてを 1 つのアクション グループに関連付けることが提案されます。  提案されるアクション グループの名前は、*WorkspaceName_AG_#Number* という名前付け規則に従います。 続行するには、[次へ] をクリックします。
画面の例を次に示します。

    ![OMS から Azure にアラートを拡張する - ステップ 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. ウィザードの最後のステップでは、前の画面で示したように、新しいアクション グループを作成し、それをアラートと関連付けることによって、Azure へのすべてのアラートの拡張をスケジュールするよう OMS に指示できます。 続行するには、[完了] をクリックし、プロンプトでプロセスの開始を確認します。 必要に応じて、ユーザーは処理終了時に OMS がレポートを送信するメール アドレスを指定することもできます。

    ![OMS から Azure にアラートを拡張する - ステップ 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. ウィザードが終了すると、[アラートの設定] ページに制御が戻り、[Azure に拡張] オプションは表示されなくなります。 バックグラウンドで、OMS は OMS のアラートを Azure に拡張するスケジュールを設定します。これには少し時間がかかることがあり、操作が始まると、しばらくの間、OMS のアラートを変更できなくなります。 現在の状態がバナーに表示され、ステップ 4 でメール アドレスを指定した場合は、バックグラウンド プロセスがすべてのアラートを Azure に正常に拡張すると、ユーザーは通知を受け取ります。 

6. Azure に正常に拡張された後でも、アラートは引き続き OMS に表示されます。

    ![OMS のアラートを Azure に拡張した後](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>オプション 2 - API を使用する
OMS のアラートを Azure に拡張するプロセスをプログラムで制御または自動化したいユーザーのため、新しい AlertsVersion API が Log Analytics で提供されています。

Log Analytics の AlertsVersion API は RESTful であり、Azure Resource Manager REST API を使用してアクセスできます。 このドキュメントでは、Azure Resource Manager API の呼び出しを簡略化するオープン ソースのコマンド ライン ツールである [ARMClient](https://github.com/projectkudu/ARMClient) を使用して PowerShell コマンド ラインから API にアクセスする例を示します。 API には、ARMClient や PowerShell を使う以外にもさまざまな方法でアクセスできます。 API の結果は JSON 形式で出力されるため、結果をプログラムによりさまざまな方法で使うことができます。

API で GET を使うことにより、結果に含まれる変更提案の要約を、OMS のアラートに対する適切な[アクション グループ](monitoring-action-groups.md)のリストとして、JSON 形式で取得できます。 複数のアラートに同様のアクションがある場合は、それらのすべてを 1 つのアクション グループに関連付けを作成することが提案されます。  提案されるアクション グループの名前は、*WorkspaceName_AG_#Number* という名前付け規則に従います。

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> API に対して GET を呼び出しても、OMS のアラートは Azure に拡張されません。 提案される変更の要約が応答として提供されるだけです。 これらの変更を確認して Azure にアラートを拡張するには、API に対して POST 呼び出しを実行する必要があります。

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

OMS のアラートを Azure に拡張するスケジューリングを開始するには、API に対して POST を実行します。 この呼び出し/コマンドを行うと、ユーザーの意図が確認され、OMS のアラートを Azure に拡張することが承認されて、API への GET 呼び出しの応答で示されている変更が実行されます。 必要に応じて、ユーザーは、スケジュールされているバックグラウンド処理が正常に完了したら OMS がレポートをメールするメール アドレスの一覧を指定できます。

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> システムで行われた変更によっては、処理の結果が GET によって提供された要約と異なる場合があります。 スケジューリングされると、OMS のアラートは一時的に編集/変更できなくなり、その間に新しいアラートを作成できます。 

POST が成功した場合は、200 OK の応答と共に次の結果が返ります。
```json
{
    "version": 2
}
```
これは、バージョン 2 で示される Azure にアラートが拡張されたことを示します。 このバージョンは、アラートが Azure に拡張されて、[Log Analytics Search API](../log-analytics/log-analytics-api-alerts.md) での使用に影響がないかどうかを確認するためだけのものです。 アラートが Azure に正常に拡張されると、ワークスペースで管理者ロールと共同作成者ロールに関連付けられているすべてのユーザーは、行われた変更の詳細についてのメールを受け取ります。


また、指定したワークスペースのすべてのアラートが、Azure への拡張を既にスケジューリングされている場合は、POST に対する応答が 403 Forbidden になります。


## <a name="next-steps"></a>次の手順

* 新しい[Azure のアラート機能のエクスペリエンス](monitoring-overview-unified-alerts.md)の詳細について学習する。
* [Azure Alerts のログ アラート](monitor-alerts-unified-log.md)について学習する。
