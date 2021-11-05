---
title: Azure Monitor REST API を使用して診断ログを設定する
description: Azure Monitor REST API を使用して、Azure Data Factory の診断ログを設定する方法について説明します。
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 41b7a817b35b15ad18d962f5fb073fac76118444
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842836"
---
# <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API を使用して診断ログを設定する

この記事では、Azure Monitor REST API を使用して、Azure Data Factory の診断ログを設定する方法について説明します。

## <a name="diagnostic-settings"></a>診断設定

診断設定を使用して、非コンピューティング リソースの診断ログを構成します。 リソース コントロールの設定には、次の機能があります。

* 診断ログを送信する場所を指定します。 たとえば、Azure ストレージ アカウント、Azure イベント ハブ、Monitor ログなどがあります。
* 送信するログ カテゴリを指定します。
* ログの各カテゴリをストレージ アカウントに保持する期間を指定します。
* リテンション期間が 0 日の場合、ログは永続的に保持されます。 または、1 日から 2,147,483,647 日の間の任意の日数を値として指定できます。
* 保持ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合、保持ポリシーは無効になります。 たとえば、この状態は、イベント ハブまたは Monitor ログのオプションのみが選択されている場合に生じる可能性があります。
* 保持ポリシーは日単位で適用されます。 日と日の間の境界は、協定世界時 (UTC) の午前 0 時になります。 1 日の終わりに、保持ポリシーの期間を超えた日のログが削除されます。 たとえば、保持ポリシーが 1 日の場合、今日が始まった時点で、昨日より前のログは削除されます。

## <a name="enable-diagnostic-logs-via-the-monitor-rest-api"></a>Monitor REST API を使用して診断ログを有効にする

診断ログを有効にするには、Monitor REST API を使用します。

### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Monitor REST API の診断設定を作成または更新する

#### <a name="request"></a>Request

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>ヘッダー

* `{api-version}` を `2016-09-01` で置き換え
* `{resource-id}` を、診断設定を編集するリソースの ID に置き換えます。 詳細については、 [リソース グループを使用した Azure リソースの管理](../azure-resource-manager/management/manage-resource-groups-portal.md)に関するページを参照してください。
* `Content-Type` ヘッダーを `application/json` に設定します。
* Azure Active Directory (Azure AD) から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、[要求の認証](../active-directory/develop/authentication-vs-authorization.md)に関するページを参照してください。

#### <a name="body"></a>Body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| プロパティ | Type | 説明 |
| --- | --- | --- |
| **storageAccountId** |String | 診断ログを送信するストレージ アカウントのリソース ID。 |
| **serviceBusRuleId** |String | 診断ログのストリーミングのためにイベント ハブを作成するサービス バス名前空間のサービス バス ルール ID。 ルール ID の形式は、`{service bus resource ID}/authorizationrules/{key name}` です。|
| **workspaceId** | String | ログが保存されるワークスペースのワークスペース ID。 |
|**メトリック**| 呼び出されたパイプラインに渡されるパイプライン実行のパラメーター値| パラメーター名を引数値にマップする JSON オブジェクト。 |
| **logs**| 複合型| リソースの種類に対応する診断ログ カテゴリの名前。 リソースの診断ログ カテゴリの一覧を取得するには、診断設定の取得操作を実行します。 |
| **category**| String| ログ カテゴリとその保持ポリシーの配列。 |
| **timeGrain** | String | ISO 8601 期間形式でキャプチャされるメトリックの粒度。 プロパティ値は、1 分を指定する `PT1M` でなければなりません。 |
| **有効**| Boolean | このリソースに対して、メトリックまたはログ カテゴリの収集を有効にするどうかを指定します。 |
| **retentionPolicy**| 複合型| メトリックまたはログのカテゴリの保持ポリシーを示します。 このプロパティは、ストレージ アカウントのみに使用されます。 |
|**days**| int| メトリックまたはログを保持する日数。 プロパティ値が 0 の場合、ログは永続的に保持されます。 このプロパティは、ストレージ アカウントのみに使用されます。 |

#### <a name="response"></a>Response

200 OK。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Monitor REST API の診断設定に関する情報を取得する

#### <a name="request"></a>Request

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>ヘッダー

* `{api-version}` を `2016-09-01` で置き換え
* `{resource-id}` を、診断設定を編集するリソースの ID に置き換えます。 詳細については、 [リソース グループを使用した Azure リソースの管理](../azure-resource-manager/management/manage-resource-groups-portal.md)に関するページを参照してください。
* `Content-Type` ヘッダーを `application/json` に設定します。
* Azure AD から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、[要求の認証](../active-directory/develop/authentication-vs-authorization.md)に関するページを参照してください。

#### <a name="response"></a>Response

200 OK。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
詳細については、[診断の設定](/rest/api/monitor/diagnosticsettings)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Monitor による SSIS 操作の監視](monitor-ssis.md)
