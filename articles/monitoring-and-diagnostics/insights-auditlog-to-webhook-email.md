---
title: "Azure アクティビティ ログ アラートでの webhook の呼び出し | Microsoft Docs"
description: "カスタム アクションのためにアクティビティ ログ イベントを他のサービスにルーティングします。 たとえば、チャット/メッセージング サービスを使用して SMS の送信、バグの記録、またはチームへの通知を行います。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>Azure アクティビティ ログ アラートでの webhook の呼び出し
webhook を使用すると、後処理やカスタム アクションのために、Azure アラート通知を他のシステムにルーティングすることができます。 アラートで webhook を使用することで、SMS の送信、バグのログ記録、チャット/メッセージング サービスを介したチームへの通知、またはその他のさまざまなアクションを実行するサービスに、アラートをルーティングできます。 この記事では、webhook が Azure アクティビティ ログ アラートの発生時に呼び出されるように設定する方法について説明します。 また、webhook に対する HTTP POST のペイロードの概要についても説明します。 Azure メトリック アラートの設定とスキーマについては、[こちらのページをご覧ください](insights-webhooks-alerts.md)。 また、起動時に電子メールを送信するようにアクティビティ ログ アラートを設定することもできます。

> [!NOTE]
> この機能は現在プレビュー段階で、今後ある時点で削除されます。
>
>

アクティビティ ログ アラートは、[Azure PowerShell コマンドレット](insights-powershell-samples.md#create-metric-alerts)、[クロスプラットフォーム CLI](insights-cli-samples.md#work-with-alerts)、[Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) のいずれかを使用して設定できます。 現時点では、Azure Portal を使用してアラートを設定することはできません。

## <a name="authenticating-the-webhook"></a>webhook の認証
webhook は、次の方法のいずれかを使用して認証できます。

1. **トークンベースの認証** - webhook URI は、`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue` のようなトークン ID を使用して保存されます。
2. **基本認証** - webhook URI は、`https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar` のようにユーザー名とパスワードを使用して保存されます。

## <a name="payload-schema"></a>ペイロード スキーマ
POST 操作には、すべてのアクティビティ ログベースのアラートについて以下の JSON ペイロードとスキーマが含まれます。 このスキーマは、メトリックベースのアラートによって使用されるものと似ています。

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| 要素名 | [説明] |
| --- | --- |
| status |メトリック アラートで使用されます。 アクティビティ ログ アラートでは常に "Activated" に設定されます。 |
| context |イベントのコンテキスト。 |
| activityLog | イベントのログ プロパティ。|
| authorization |イベントの RBAC プロパティ。 これらには、通常、"action"、"role"、"scope" が含まれます。 |
| action | アラートによってキャプチャされたアクション。 |
| scope | アラートのスコープ (つまり、 リソース)。|
| channels | 操作 |
| claims | 要求に関連する情報のコレクション。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの GUID またはユーザー名。 一部のシステム呼び出しでは、null の場合があります。 |
| correlationId |通常は GUID (文字列形式)。 correlationId を含むイベントは、より大きな同じアクションに属し、通常は correlationId を共有します。 |
| 説明 |アラートの作成時に設定したアラートの説明。 |
| eventSource |イベントを生成した Azure サービスまたはインフラストラクチャの名前。 |
| eventTimestamp |イベントが発生した時間。 |
| eventDataId |イベントの一意識別子。 |
| level |"Critical"、"Error"、"Warning"、"Informational"、"Verbose" のいずれかの値。 |
| operationName |操作の名前。 |
| operationId |通常、単一の操作に対応する複数のイベントで共有される GUID。 |
| ResourceId |影響を受けるリソースのリソース ID。 |
| resourceGroupName |影響を受けるリソースのリソース グループの名前。 |
| resourceProviderName |影響を受けるリソースのリソース プロバイダー。 |
| status |文字列 をオンにします。 操作の状態。 一般的な値は "Started"、"In Progress"、"Succeeded"、"Failed"、"Active"、"Resolved" です。 |
| subStatus |通常、対応する REST 呼び出しの HTTP 状態コードが含まれます。 また、subStatus を説明する他の文字列を含めることもできます。 一般的な subStatus の値は、OK (HTTP 状態コード: 200)、Created (HTTP 状態コード: 201)、Accepted (HTTP 状態コード: 202)、No Content (HTTP 状態コード: 204)、Bad Request (HTTP 状態コード: 400)、Not Found (HTTP 状態コード: 404)、Conflict (HTTP 状態コード: 409)、Internal Server Error (HTTP 状態コード: 500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504) です。 |
| subscriptionId |Azure サブスクリプション ID。 |
| submissionTimestamp |要求を処理した Azure サービスによってイベントが生成された時刻。 |
| resourceType | イベントを生成したリソースの種類。|
| プロパティ |イベントの詳細を含む `<Key, Value>` ペア (つまり、`Dictionary<String, String>`) のセット。 |

## <a name="next-steps"></a>次の手順
* [アクティビティ ログについて詳しく学習します](monitoring-overview-activity-logs.md)
* [Azure アラートで Azure Automation スクリプト (Runbook) を実行します](http://go.microsoft.com/fwlink/?LinkId=627081)
* [ロジック アプリを使用して、Azure アラートから Twilio 経由で SMS を送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Slack メッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Azure キューにメッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
