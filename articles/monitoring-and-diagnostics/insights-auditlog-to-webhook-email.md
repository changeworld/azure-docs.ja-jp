---
title: Azure アクティビティ ログ アラートでの webhook の呼び出し (クラシック)
description: カスタム アクションのためにアクティビティ ログ イベントを他のサービスにルーティングする方法について説明します。 たとえば、SMS メッセージの送信、バグの記録、チャットやメッセージング サービスを使用したチームへの通知を行うことができます。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: e825d0f2487c20c8c7f3d210d7180b07742d7173
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262462"
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Azure アクティビティ ログ アラートでの webhook の呼び出し
後処理やカスタム アクションのために、webhook を使用して Azure アラート通知を他のシステムにルーティングできます。 SMS メッセージを送信するサービスへのアラートのルーティング、バグの記録、チャットやメッセージング サービスを使用したチームへの通知など、さまざまなアクションに対してアラートで webhook を使用できます。 また、アラートがアクティブになったときに電子メールを送信するようにアクティビティ ログ アラートを設定することもできます。

この記事では、Azure アクティビティ ログ アラートの発生時に webhook が呼び出されるように設定する方法について説明します。 また、webhook に対する HTTP POST のペイロードの概要についても説明します。 Azure メトリック アラートの設定とスキーマの詳細については、「[Azure メトリック アラートでの webhook の構成](insights-webhooks-alerts.md)」を参照してください。 

> [!NOTE]
> Azure アクティビティ ログ アラートでの webhook の呼び出しをサポートする機能は、現時点ではプレビュー段階にあります。
>
>

アクティビティ ログ アラートは、[Azure PowerShell コマンドレット](insights-powershell-samples.md#create-metric-alerts)、[クロスプラットフォーム CLI](insights-cli-samples.md#work-with-alerts)、[Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) のいずれかを使用して設定できます。 現時点では、アクティビティ ログ アラートの設定に Azure Portal は使用できません。

## <a name="authenticate-the-webhook"></a>webhook の認証
webhook は、次の方法のいずれかを使用して認証できます。

* **トークンベースの認証**。 webhook URI は、トークン ID を使用して保存されます。 次に例を示します。`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **基本承認**。 webhook URI は、ユーザー名とパスワードを使用して保存されます。 次に例を示します。`https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>ペイロード スキーマ
POST 操作には、すべてのアクティビティ ログベースのアラートについて以下の JSON ペイロードとスキーマが含まれます。 このスキーマは、メトリックベースのアラートで使用されるものと似ています。

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

| 要素名 | 説明 |
| --- | --- |
| status |メトリック アラートで使用されます。 アクティビティ ログ アラートでは常に "Activated" に設定されます。|
| context |イベントのコンテキスト。 |
| activityLog | イベントのログ プロパティ。|
| authorization |イベントのロールベースのアクセス制御 (RBAC) プロパティ。 これらのプロパティには通常、**action**、**role**、**scope** が含まれます。 |
| action | アラートによってキャプチャされたアクション。 |
| scope | アラートのスコープ (つまり、リソース)。|
| channels | 操作。 |
| claims | 要求に関連する情報のコレクション。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの GUID またはユーザー名。 一部のシステム呼び出しでは、null 値の場合があります。 |
| correlationId |通常は GUID (文字列形式)。 **correlationId** を含むイベントは、より大きな同じアクションに属します。 通常は、同じ **correlationId** 値を持ちます。 |
| description  |アラートの作成時に設定されたアラートの説明。 |
| eventSource |イベントを生成した Azure サービスまたはインフラストラクチャの名前。 |
| eventTimestamp |イベントが発生した時刻。 |
| eventDataId |イベントの一意識別子。 |
| level |"Critical"、"Error"、"Warning"、"Informational"、"Verbose" のいずれかの値。 |
| operationName |操作の名前。 |
| operationId |通常、イベント間で共有されている GUID。 GUID は通常、単一の操作に対応します。 |
| ResourceId |影響を受けるリソースのリソース ID。 |
| resourceGroupName |影響を受けるリソースのリソース グループの名前。 |
| resourceProviderName |影響を受けるリソースのリソース プロバイダー。 |
| status |操作の状態を示す文字列値。 一般的な値は Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus |通常、対応する REST 呼び出しの HTTP 状態コードが含まれます。 また、subStatus を説明する他の文字列を含めることもできます。 一般的な subStatus の値は、OK (HTTP 状態コード: 200)、Created (HTTP 状態コード: 201)、Accepted (HTTP 状態コード: 202)、No Content (HTTP 状態コード: 204)、Bad Request (HTTP 状態コード: 400)、Not Found (HTTP 状態コード: 404)、Conflict (HTTP 状態コード: 409)、Internal Server Error (HTTP 状態コード: 500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504) です。 |
| subscriptionId |Azure サブスクリプション ID。 |
| submissionTimestamp |要求を処理した Azure サービスによってイベントが生成された時刻。 |
| resourceType | イベントを生成したリソースの種類。|
| プロパティ |イベントに関する詳細を含むキー/値のペアのセット。 たとえば、「`Dictionary<String, String>`」のように入力します。 |

## <a name="next-steps"></a>次の手順
* [アクティビティ ログ](monitoring-overview-activity-logs.md)の詳細を確認します。
* [Azure アラートで Azure Automation スクリプト (Runbook) を実行する](http://go.microsoft.com/fwlink/?LinkId=627081)方法について確認します。
* [ロジック アプリを使用して、Azure アラートから Twilio 経由で SMS メッセージを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)方法について確認します。 この例はメトリック アラート用のものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Slack メッセージを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)方法について確認します。 この例はメトリック アラート用のものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Azure キューにメッセージを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)方法について確認します。 この例はメトリック アラート用のものですが、変更を加えてアクティビティ ログ アラートで使用できます。
