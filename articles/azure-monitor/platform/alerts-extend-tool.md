---
title: Log Analytics から Azure へのアラートの拡張
description: この記事では、アラートを Log Analytics から Azure アラートに拡張するためのツールと API について説明します。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: bdc3646116dfd5f16c0c039c4fb95d11c6593adf
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120995"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>アラートを Log Analytics から Azure アラートに拡張する
Azure Log Analytics のアラート機能は、Azure アラートに置き換わりつつあります。 この移行の一環として、Log Analytics で最初に構成したアラートは Azure に拡張されます。 アラートが自動的に Azure に移動されるのを待ちたくない場合は、次の方法でプロセスを開始できます。

- Operations Management Suite ポータルから手動で開始する。 
- AlertsVersion API を使ってプログラムによって開始する。  

> [!NOTE]
> Microsoft は 2018 年 5 月 14 日より、完了するまで反復される一連の処理で、Log Analytics のパブリック クラウド インスタンスで作成されたアラートを自動的に Azure アラートに拡張します。 [アクション グループ](../../azure-monitor/platform/action-groups.md)の作成でなんらかの問題がある場合は、[これらの修復手順](alerts-extend-tool.md#troubleshooting)を使用して、アクション グループを自動作成させます。 2018 年 7 月 5日まで、これらの手順を使用できます。 *Log Analytics の Azure Government およびソブリン クラウド ユーザーには適用されません*。 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>選択肢 1: Operations Management Suite ポータルから開始する
次の手順では、Operations Management Suite ポータルからワークスペースのアラートを拡張する方法について説明します。  

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
2. Log Analytics サブスクリプション ウィンドウで、ワークスペースを選択して **[OMS ポータル]** タイルを選択します。
![[OMS ポータル] タイルが強調表示されている Log Analytics サブスクリプション ウィンドウのスクリーンショット](media/alerts-extend-tool/azure-portal-01.png) 
3. Operations Management Suite ポータルにリダイレクトされたら、**[設定]** アイコンを選択します。
![[設定] アイコンが強調表示されている Operations Management Suite ポータルのスクリーンショット](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. **[設定]** ページで **[アラート]** を選択します。  
5. **[Azure に拡張]** を選択します。
![[Azure に拡張] が強調表示されている Operations Management Suite ポータルの [アラートの設定] ページのスクリーンショット](media/alerts-extend-tool/ExtendInto.png)
6. **[アラート]** ウィンドウに 3 つのステップで構成されるウィザードが表示されます。 概要を読み、**[次へ]** を選択します。
![ウィザードのステップ 1 のスクリーンショット](media/alerts-extend-tool/ExtendStep1.png)  
7. 2 番目のステップでは、アラートに対して提案される変更の概要が、適切な[アクション グループ](../../azure-monitor/platform/action-groups.md)の一覧により示されます。 複数のアラートに同様のアクションがある場合は、それらのすべてに 1 つのアクション グループを関連付けることが提案されます。  名前付け規則は次のとおりです。*WorkspaceName_AG_#Number* という形式を使用します。 続行するには、**[次へ]** を選択します。
![ウィザードのステップ 2 のスクリーンショット](media/alerts-extend-tool/ExtendStep2.png)  
8. ウィザードの最後のステップで、**[完了]** を選択し、プロセスを開始するよう求められたら確認します。 必要に応じて、メールアドレスを指定できます。これにより、プロセスが完了し、すべてのアラートが適切に Azure アラートに移動された時点で通知を受け取ることができます。
![ウィザードのステップ 3 のスクリーンショット](media/alerts-extend-tool/ExtendStep3.png)

ウィザードが終了すると、**[アラートの設定]** ページから、Azure にアラートを拡張するためのオプションが削除されます。 対象のアラートはバックグラウンドで Azure に移動されますが、これには少し時間がかかることがあります。 処理中、Operations Management Suite ポータルからアラートを変更することはできません。 現在の状態は、ポータル上部のバナーで確認できます。 前にメールアドレスを指定した場合は、プロセスが適切に完了した時点でメールが届きます。  


アラートは、Azure に適切に移動された後も、引き続き Operation Management Suite ポータルに表示されます。
![Operations Management Suite ポータルの [アラートの設定] ページのスクリーンショット](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>選択肢 2: AlertsVersion API を使用する
Log Analytics AlertsVersion API を使用して、REST API を呼び出すことができる任意のクライアントから、アラートを Log Analytics から Azure アラートに拡張できます。 PowerShell から API にアクセスするには、オープンソースのコマンドライン ツール [ARMClient](https://github.com/projectkudu/ARMClient) を使用します。 結果は JSON に出力できます。  

API を使用するには、最初に GET 要求を作成します。 これにより、POST 要求を使用して実際に拡張を試みる前に、評価が実施され、変更提案の概要が返されます。 この結果には、対象のアラートと、提案された[アクション グループ](../../azure-monitor/platform/action-groups.md)の一覧が JSON 形式で表示されます。 複数のアラートに同様のアクションがある場合は、それらのすべてに 1 つのアクション グループを関連付けることが提案されます。 名前付け規則は次のとおりです。*WorkspaceName_AG_#Number* という形式を使用します。

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

GET 要求が成功すると、HTTP 状態コード 200 と、アラートおよび提案されたアクション グループの一覧が JSON データで返されます。 応答の例を次に示します。

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
                            "serviceUri": "https://test.com"
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
指定したワークスペースに警告ルールが定義されていない場合は、JSON データにより次が返されます。

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

指定したワークスペース内のすべての警告ルールが Azure に既に拡張されている場合は、GET 要求に対する応答は次のようになります。

```json
{
    "version": 2
}
```

Azure へのアラートの移行を開始するには、POST 応答を開始します。 この POST 応答によって、アラートを Log Analytics から Azure アラートに拡張することについて、ユーザーの意図と同意が確認されます。 アクティビティのスケジュール設定とアラートの処理は、前に GET 応答を実行したときの結果に基づいて指定の通りに行われます。 必要に応じて、スケジュールされたアラート移行のバックグラウンド プロセスが正常に完了したときに Log Analytics によるレポート送信先となるメールアドレスを指定できます。 次のような要求の例を使用できます。

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Azure アラートへのアラートの移行の結果は、GET 応答で提供された概要によって異なる場合があります。 スケジュールされている場合、Log Analytics のアラートは、一時的に Operation Management Suite ポータルで変更できなくなります。 ただし、新しいアラートを作成することはできます。 

POST 要求が成功すると、HTTP 200 OK 状態と次の応答が返されます。

```json
{
    "version": 2
}
```

この応答は、アラートが Azure アラートに適切に拡張されたことを示します。 version プロパティは、アラートが Azure に拡張されたかどうかを確認するためだけのもので、[Log Analytics Search API](../../azure-monitor/platform/api-alerts.md) とは無関係です。 アラートが Azure に正常に拡張されると、POST 要求で指定したすべてのメール アドレスにレポートが送信されます。 指定したワークスペースのすべてのアラートの拡張が既にスケジュールされている場合、POST 要求に対して "禁止" の応答が返されます (403 状態コード)。 エラー メッセージを表示したり、プロセスが停止しているかどうかを確認したりするために、GET 要求を送信できます。 エラー メッセージがある場合は、そのメッセージが概要情報と共に返されます。

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
                            "serviceUri": "https://test.com"
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


## <a name="option-3-use-a-custom-powershell-script"></a>選択肢 3: カスタム PowerShell スクリプトを使用する
 Microsoft によって対象のアラートが Operation Management Suite ポータルから Azure にまだ拡張されていない場合、2018 年 7 月 5日まではご自身で手動で拡張できます。 2 つの手動拡張オプションについては、前の 2 つのセクションで説明しました。

2018 年 7 月 5日を過ぎると、すべてのアラートが Operations Management Suite ポータルから Azure に拡張されます。 [提案された必要な修復手順](#troubleshooting)をまだ実行していないユーザーのアラートは、関連付けられた[アクション グループ](../../azure-monitor/platform/action-groups.md)がないため、アクションまたは通知なしで実行されます。 

Log Analytics でアラートの[アクション グループ](../../azure-monitor/platform/action-groups.md)を手動で作成するには、次のサンプル スクリプトを使用します。
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occurred while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>カスタム PowerShell スクリプトについて 
スクリプトの使用に関する重要な情報を次に示します。
- 前提条件として、Azure Resource Manager API の呼び出しを簡略化する、オープン ソースのコマンドライン ツールである [ARMclient](https://github.com/projectkudu/ARMClient) をインストールする必要があります。
- スクリプトを実行するには、Azure サブスクリプションの共同作成者または所有者のロールが必要です。
- 次のパラメーターを指定する必要があります。
    - $subscriptionId: Operations Management Suite Log Analytics ワークスペースに関連付けられている Azure サブスクリプション ID。
    - $resourceGroup: Operations Management Suite Log Analytics ワークスペースの Azure リソース グループ。
    - $workspaceName: Operations Management Suite Log Analytics ワークスペースの名前。

### <a name="output-of-the-custom-powershell-script"></a>カスタム PowerShell スクリプトの出力
スクリプトには詳細な情報が示されており、実行中に手順が出力されます。 
- 概要が表示されます。この概要には、ワークスペース内の既存の Operations Management Suite Log Analytics アラートに関する情報が含まれます。 また、概要には、アクションに対して作成され、そのアクションに関連付けられた Azure アクション グループに関する情報も含まれます。 
- 拡張を続行するように求められます。概要の表示後に処理を終了することもできます。
- 拡張を続行すると、新しい Azure アクション グループが作成され、既存のアラートすべてがそのアクション グループに関連付けられます。 
- スクリプトが完了すると、"拡張が完了" したことを示すメッセージが表示されます。 途中で障害が発生した場合は、その後にスクリプトによってエラーが表示されます。

## <a name="troubleshooting"></a>トラブルシューティング 
アラートを拡張するプロセスの途中で問題が発生し、必要な[アクション グループ](../../azure-monitor/platform/action-groups.md)を作成できないことがあります。 このような場合は、エラー メッセージが Operation Management Suite ポータルの **[アラート]** セクションのバナーに表示されます。または、API に対する GET 呼び出しの完了時に表示されます。

> [!IMPORTANT]
> Azure パブリック クラウド ベースの Log Analytics ユーザーが 2018 年 7 月 5日までに次の修復手順を実行しない場合、アラートは Azure で実行されますが、アクションや通知は実行されません。 アラートの通知を取得するには、[アクション グループ](../../azure-monitor/platform/action-groups.md)を手動で編集して追加するか、前述の[カスタム PowerShell スクリプト](#option-3---using-custom-powershell-script)を使用する必要があります。

各エラーの修復手順を次に示します。
- **エラー: Scope Lock is present at subscription/resource group level for write operations (サブスクリプション/リソース グループ レベルに書き込み操作のスコープ ロックが存在します)**:  ![スコープ ロックのエラー メッセージが強調表示されている Operations Management Suite ポータルの [アラートの設定] ページのスクリーンショット](media/alerts-extend-tool/ErrorScopeLock.png)

    スコープ ロックが有効になっている場合、Log Analytics (Operation Management Suite) ワークスペースを含むサブスクリプションまたはリソース グループ内での新しい変更がすべて制限されます。 システムによる Azure へのアラートの拡張は行われず、必要なアクション グループが作成されません。
    
    解決するには、ワークスペースを含むサブスクリプションまたはリソース グループの *ReadOnly* ロックを削除します。 この操作は Azure portal、PowerShell、Azure CLI、または API から実行できます。 詳細については、[リソース ロックの使用](../../azure-resource-manager/resource-group-lock-resources.md)に関するページをご覧ください。 
    
    この記事で説明されている手順を使用して問題を解決すると、スケジュールされている翌日の実行が行われている間に、Operation Management Suite によって対象のアラートが Azure に拡張されます。 他の操作を行ったり、何かを開始したりする必要はありません。

- **エラー: Policy is present at subscription/resource group level (サブスクリプション/リソース グループ レベルにポリシーが存在します)**:  ![ポリシーのエラー メッセージが強調表示されている Operations Management Suite ポータルの [アラートの設定] ページのスクリーンショット](media/alerts-extend-tool/ErrorPolicy.png)

    [Azure Policy](../../governance/policy/overview.md) が適用されている場合、Log Analytics (Operation Management Suite) ワークスペースを含むサブスクリプションまたはリソース グループの新しいリソースがすべて制限されます。 システムによる Azure へのアラートの拡張は行われず、必要なアクション グループが作成されません。
    
    これを解決するには、ワークスペースを含むサブスクリプションまたはリソース グループでの新しいリソースの作成を妨げている *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* エラーの原因であるポリシーを編集します。 この操作は Azure portal、PowerShell、Azure CLI、または API から実行できます。 アクションを監査して、障害の原因になっている適切なポリシーを見つけることができます。 詳細については、[アクションを監査するアクティビティ ログの表示](../../azure-resource-manager/resource-group-audit.md)に関するページをご覧ください。 
    
    この記事で説明されている手順を使用して問題を解決すると、スケジュールされている翌日の実行が行われている間に、Operation Management Suite によって対象のアラートが Azure に拡張されます。 他の操作を行ったり、何かを開始したりする必要はありません。


## <a name="next-steps"></a>次の手順

* 新しい [Azure アラートのエクスペリエンス](../../azure-monitor/platform/alerts-overview.md)の詳細について学習する。
* [Azure Alerts のログ アラート](alerts-unified-log.md)について学習する。
