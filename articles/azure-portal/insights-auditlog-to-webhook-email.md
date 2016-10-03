<properties
	pageTitle="Azure アクティビティ ログ アラートでの webhook の構成 | Microsoft Azure"
	description="アクティビティ ログ アラートを使用して webhook を呼び出す方法について説明します。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="ashwink"/>

# Azure アクティビティ ログ アラートでの webhook の構成

webhook を使用すると、後処理やカスタム アクションのために、Azure アラート通知を他のシステムにルーティングすることができます。アラートで webhook を使用することで、SMS の送信、バグのログ記録、チャット/メッセージング サービスを介したチームへの通知、またはその他のさまざまなアクションを実行するサービスに、アラートをルーティングできます。この記事では、Azure アクティビティ ログ アラートで webhook を設定する方法のほか、webhook に対する HTTP POST のペイロードの内容について説明します。Azure メトリック アラートの設定とスキーマについては、[こちらのページをご覧ください](./insights-webhooks-alerts.md)。また、起動時に電子メールを送信するようにアクティビティ ログ アラートを設定することもできます。

>[AZURE.NOTE] この機能は現在プレビューの段階であり、品質とパフォーマンスの変更が予想されます。

アクティビティ ログ アラートは、[Azure PowerShell コマンドレット](./insights-powershell-samples.md#create-alert-rules)、[クロスプラットフォーム CLI](./insights-cli-samples.md#work-with-alerts)、[Insights REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) のいずれかを使用して設定できます。

## webhook の認証
webhook は、次の方法のいずれかを使用して認証できます。

1. **トークンベースの認証** - webhook URI は次のようなトークン ID を使用して保存されます。 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.	**基本認証** - webhook URI は次のようなユーザー名とパスワードを使用して保存されます。 `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## ペイロード スキーマ
POST 操作には、すべてのアクティビティ ログベースのアラートについて以下の JSON ペイロードとスキーマが含まれます。このスキーマは、メトリックベースのアラートによって使用されるものと似ています。

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|要素名|	Description|
|---|---|
|status |メトリック アラートで使用されます。アクティビティ ログ アラートでは常に "Activated" に設定されます。|
|context|イベントのコンテキスト。|
|resourceProviderName|影響を受けるリソースのリソース プロバイダー。|
|conditionType |常に "Event" です。|
|name |アラート ルールの名前。|
|id |アラートのリソース ID。|
|description|	アラートの作成時に設定したアラートの説明。|
|subscriptionId |Azure サブスクリプション ID。|
|timestamp|	要求を処理した Azure サービスによってイベントが生成された時刻。|
|resourceId |影響を受けるリソースのリソース ID。|
|resourceGroupName|影響を受けるリソースのリソース グループの名前。|
|properties |イベントの詳細を含む `<Key, Value>` ペア (つまり、`Dictionary<String, String>`) のセット。|
|event|イベントに関するメタデータを含む要素。|
|authorization|イベントの RBAC プロパティ。通常、"action"、"role"、"scope" が含まれます。|
|カテゴリ | イベントのカテゴリ。サポートされる値は Administrative、Alert、Security、ServiceHealth、Recommendation です。|
|caller|操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの電子メール アドレス。一部のシステム呼び出しでは、null の場合があります。|
|correlationId|	通常は GUID (文字列形式)。correlationId を含むイベントは、より大きな同じアクションに属し、通常は correlationId を共有します。|
|eventDescription |イベントを説明する静的テキスト。|
|eventDataId|イベントの一意識別子。|
|eventSource |イベントを生成した Azure サービスまたはインフラストラクチャの名前。|
|httpRequest|	通常、“clientRequestId”、“clientIpAddress”、“method” (HTTP メソッド、たとえば PUT) が含まれます。|
|level|"Critical"、"Error"、"Warning"、"Informational"、"Verbose" のいずれかの値。|
|operationId|通常、単一の操作に対応する複数のイベントで共有される GUID。|
|operationName|操作の名前。|
|properties |イベントのプロパティ。|
|status|文字列] をオンにします。操作の状態。一般的な値は "Started"、"In Progress"、"Succeeded"、"Failed"、"Active"、"Resolved" です。|
|subStatus|	通常、対応する REST 呼び出しの HTTP 状態コードが含まれます。また、subStatus を説明する他の文字列を含めることもできます。一般的な subStatus の値は、OK (HTTP 状態コード: 200)、Created (HTTP 状態コード: 201)、Accepted (HTTP 状態コード: 202)、No Content (HTTP 状態コード: 204)、Bad Request (HTTP 状態コード: 400)、Not Found (HTTP 状態コード: 404)、Conflict (HTTP 状態コード: 409)、Internal Server Error (HTTP 状態コード: 500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504) です。|

## 次のステップ
- [アクティビティ ログについて詳しく学習します](./monitoring-overview-activity-logs.md)。
- [Azure アラートで Azure Automation スクリプト (Runbook) を実行します](http://go.microsoft.com/fwlink/?LinkId=627081)。
- [ロジック アプリを使用して、Azure アラートから Twilio 経由で SMS を送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
- [ロジック アプリを使用して、Azure アラートから Slack メッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
- [ロジック アプリを使用して、Azure アラートから Azure キューにメッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。

<!---HONumber=AcomDC_0921_2016-->