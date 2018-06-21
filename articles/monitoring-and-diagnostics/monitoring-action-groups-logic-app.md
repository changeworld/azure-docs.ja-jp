---
title: Azure Monitor アラートとアクション グループによって複雑なアクションをトリガーする方法
description: Azure Monitor アラートを処理するためのロジック アプリのアクションを作成する方法を説明します。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: eafb2bcf0175190748c9dd020051cbebfcaee1fd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263886"
---
# <a name="create-a-logic-app-action"></a>ロジック アプリのアクションの作成
## <a name="overview"></a>概要 ##
Azure Monitor アラートは、トリガー時に[アクション グループ](monitoring-action-groups.md)を呼び出します。 アクション グループを使用すると、アラートをユーザーに通知したり、アラートを修復したりするための 1 つまたは複数のアクションをトリガーできます。

この記述では、アラート発生時に Microsoft Teams で会話を作成するために、ロジック アプリをセットアップおよびトリガーする方法を示します。

一般的なプロセスは次のとおりです:

-   それぞれのアラートの種類に対応したロジック アプリを作成する

-   それぞれのアラートの種類のスキーマをロジック アプリにインポートする

-   ロジック アプリの動作を定義する

-   ロジック アプリの HTTP エンドポイントを Azure アクション グループにコピーする

ロジック アプリで別のアクションを実行させる場合も、プロセスは同様です。

## <a name="create-an-activity-log-alert--administrative"></a>アクティビティ ログ アラートの作成 – 管理

1.  Azure Portal の左上隅にある **[リソースの作成]** ボタンをクリックします。

2.  **ロジック アプリ**を検索して選択します。 **[作成]** ボタンをクリックします。

3.  ロジック アプリに名前を付け、リソース グループなどを選択します。

    ![[ロジック アプリの作成] ダイアログ](media/monitoring-action-groups/create-logic-app-dialog.png "[ロジック アプリの作成] ダイアログ")

4.  [作成] ボタンをクリックしてロジック アプリを作成します。 ロジック アプリが作成されると、ポップアップが表示されます。 [Launch Resource] (リソースの起動) ボタンをクリックしてロジック アプリ デザイナーを開きます。

5.  トリガー **[HTTP 要求の受信時]** を選択します。

    ![ロジック アプリのトリガー](media/monitoring-action-groups/logic-app-triggers.png "ロジック アプリのトリガー")

6.  **[編集]** を選択して HTTP 要求トリガーを変更します

    ![HTTP 要求トリガーのシェイプ](media/monitoring-action-groups/http-request-trigger-shape.png "HTTP 要求トリガーのシェイプ")

7.  **[サンプルのペイロードを使用してスキーマを生成する]** を選びます。

    ![[Use sample payload] (サンプル ペイロードの使用) ボタン](media/monitoring-action-groups/use-sample-payload-button.png "[Use sample payload] (サンプル ペイロードの使用) ボタン")

8.  次のサンプル スキーマをコピーしてダイアログ ボックスに貼り付けます。

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. ロジック アプリ デザイナーで、ロジック アプリに送信する要求の Content-Type ヘッダーを application/json に設定する必要があるという注意が表示されます。 確認してダイアログを閉じます。 Azure Monitor アラートはこれを正しく実行します。

    ![Content-Type ヘッダー](media/monitoring-action-groups/content-type-header.png "Content-Type ヘッダー")

10. **[+ 新しいステップ]**、**[アクションの追加]** の順に選択します。

    ![アクションの追加](media/monitoring-action-groups/add-action.png "アクションの追加")

11. Microsoft Teams コネクタを検索して選択します。 **[Microsoft Teams – メッセージの投稿]** アクションを選択します。

    ![Microsoft Teams アクション](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams アクション")

12. Microsoft Teams アクションを構成します。 ロジック アプリ デザイナーにより、Office365 アカウントを認証するよう求められます。 メッセージ送信先の **[Team Id] (チーム ID)** と **[Channel Id] (チャネル ID)** を選択します。

13. 静的テキストと、動的コンテンツ内の\<フィールド\>への参照の組み合わせを使用して**メッセージ**を構成します。 次のテキストを切り取って [メッセージ] フィールドに貼り付けます。

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    次に、\<フィールド\>を検索して、同じ名前の動的コンテンツ タグに置き換えます。

    **[注意!]** **status** という名前の 2 つの動的フィールドがあります。 両方の status フィールドをメッセージに追加します。 activityLog プロパティ バッグにあるほうを使用し、もう一方を削除します。 **status** フィールドにマウス ポインターを重ねると、スクリーンショットに示すように、完全修飾のフィールド参照が表示されます

    ![Teams アクション - メッセージの投稿](media/monitoring-action-groups/teams-action-post-message.png "Teams アクション - メッセージの投稿")

14. デザイナーの上部にある [保存] ボタンをクリックして、ロジック アプリを保存します

15. HTTP 要求のシェイプをクリックして展開します。 HTTP POST URL をコピーします。

    ![HTTP POST URL](media/monitoring-action-groups/http-post-url.png "HTTP POST URL")

16. 既存のアクション グループを開き、ロジック アプリを参照するためのアクションを追加します。 既存のアクション グループがない場合、<https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> を参照して作成します。 忘れずに変更を保存してください。

    ![アクション グループの更新](media/monitoring-action-groups/update-action-group.png "アクション グループの更新")

アラートが次にアクション グループを起動するときに、ロジック アプリが呼び出されます。

## <a name="create-a-service-health-alert"></a>サービス正常性アラートの作成

サービス正常性のエントリはアクティビティ ログの一部であるため、プロセスは同様ですが次の変更点があります

1.  手順 1 ～ 7 は同じです。
2.  手順 8 の HTTP トリガーには次のサンプル スキーマを使用します。

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  手順 9 ～ 10 は同じです。
4.  手順 11 からは次のプロセスを使用します。
5.  **[+ 新しいステップ]** ボタンをクリックして **[条件の追加]** を選択します。 次の条件を設定して、入力データがこれらの値と一致する場合にのみロジック アプリが実行されるようにします
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    !["サービス正常性のペイロード条件"](media/monitoring-action-groups/service-health-payload-condition.png "サービス正常性のペイロード条件")

6. **if true** 条件で、前の例の手順 11 ～ 13 を使用して Microsoft Teams アクションを追加します。

7. HTML と [動的コンテンツ] の組み合わせを使用してメッセージを定義します。 次の内容をコピーしてメッセージ フィールドに貼り付けます。 [incidentType]、[trackingID]、[title]、および [communication] の各フィールドを、同じ名前の動的コンテンツ タグで置き換えます

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    !["サービス正常性の true 条件の事後アクション"](media/monitoring-action-groups/service-health-true-condition-post-action.png "サービス正常性の true 条件の事後アクション")

8. **If false** 条件には有用なメッセージを指定します

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    !["サービス正常性の false 条件の事後アクション"](media/monitoring-action-groups/service-health-false-condition-post-action.png "サービス正常性の false 条件の事後アクション")

9.  前の例の手順 15 ～ 16 に従って、ロジック アプリを保存し、アクション グループを更新します

## <a name="metric-alert"></a>メトリック アラート

1.  手順 1 ～ 7 は最初の例と同じです
2.  手順 8 の HTTP トリガーには次のサンプル スキーマを使用します。

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  手順 9 ～ 10 は同じです。
4.  手順 11 からは次のプロセスを使用します。
5.  **[+ 新しいステップ]** ボタンをクリックして **[条件の追加]** を選択します。 次の条件を設定して、入力データがこれらの値と一致する場合にのみロジック アプリが実行されるようにします

    - schemaId == AzureMonitorMetricAlert
    - version == 2.0

    !["メトリック アラートのペイロード条件"](media/monitoring-action-groups/metric-alert-payload-condition.png "メトリック アラートのペイロード条件")

6.  **if true** 条件では、**For each** シェイプと Microsoft Teams アクションを追加し、HTML と [動的コンテンツ] の組み合わせを使用してメッセージを定義します

    !["メトリック アラートの true 条件の事後アクション"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "メトリック アラートの true 条件の事後アクション")

7.  **If false** シェイプで、メトリック アラートがロジック アプリの期待に一致しないことを知らせる Microsoft Teams アクションを定義し、JSON ペイロードを含めます。 json() 式で triggerBody 動的コンテンツを参照する方法に注意してください。

    !["メトリック アラートの false 条件の事後アクション"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "メトリック アラートの false 条件の事後アクション")

8.  最初の例の手順 15 ～ 16 に従って、ロジック アプリを保存し、アクション グループを更新します

## <a name="next-steps"></a>次の手順 ##
* [アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法について学習します。
* [アクション グループ](monitoring-action-groups.md)の詳細については、こちらをご覧ください。