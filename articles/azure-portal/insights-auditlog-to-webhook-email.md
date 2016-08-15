<properties
	pageTitle="Azure Insights: Azure Insights で監査ログを使用して電子メールと Webhook アラート通知を送信する | Microsoft Azure"
	description="サービス監査ログのエントリを使用して Web URL を呼び出したり、Azure Insights で電子メール通知を送信したりする方法について説明します。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring"
	documentationCenter="monitoring"/>

<tags
	ms.service="monitoring"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Azure Insights で監査ログを使用して電子メールと Webhook アラート通知を送信する

この記事では、監査ログ イベントが Webhook をトリガーした場合のペイロード スキーマと、監査ログ イベントを使用して電子メールを送信する方法について説明します。

>[AZURE.NOTE] 現在、この機能はプレビュー段階にあります。今後数か月をかけて、イベント発生時のアラートのインフラストラクチャとパフォーマンスを改善する予定です。このプレビューでこの機能にアクセスするには、Azure PowerShell と CLI を使用する必要があります。今後、Azure ポータルを使用してこの機能にアクセスできるようになる予定です。

## Webhook
Webhook を使用すると、後処理やカスタム通知のために、Azure アラート通知を他のシステムにルーティングすることができます。たとえば、受信 Web 要求を処理して SMS を送信する、バグをログに記録する、チャットやメッセージング サービスを使用して誰かに通知するなどのサービスにアラートをルーティングすることができます。Webhook URI は有効な HTTP または HTTPS エンドポイントである必要があります。

## 電子メール
電子メールは、任意の有効な電子メール アドレスに送信できます。このルールが実行されているサブスクリプションの管理者と共同管理者にも通知されます。

### 電子メール ルールの例
電子メール ルール、Webhook ルールを設定してから、監査ログ イベントが発生したときに開始されるようにルールに指示する必要があります。PowerShell を使用した例については、[Azure Insights PowerShell のクイックスタート サンプル](insights-powershell-samples.md#alert-on-audit-log-event)のページを参照してください。


## 認証
認証 URI には、2 つの形式があります。

1. クエリ パラメーターとしてトークン ID を使用して Webhook URI を保存するトークンベースの認証。たとえば、https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue のように指定します。
2. ユーザー ID とパスワードを使用する基本認証。たとえば、https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value のように指定します。

## 監査ログ イベント通知の Webhook ペイロード スキーマ
新しいイベントが使用できるようになると、監査ログ イベント発生時のアラートによって、Webhook ペイロードのイベント メタデータを使用して構成済みの Webhook が実行されます。次に、Webhook ペイロード スキーマの例を示します。

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

|要素名|	説明|
|---|---|
|status |常に "activated" に設定します。|
|context|イベントのコンテキスト|
|resourceProviderName|影響を受けるリソースのリソース プロバイダー|
|conditionType |"Event"|
|name |アラート ルールの名前|
|id |アラートのリソース ID|
|description|	アラートの作成者がアラートに設定した説明|
|subscriptionId |Azure サブスクリプションの GUID|
|timestamp|	イベントに対応する要求を処理した Azure サービスによって、イベントが生成されたときのタイムスタンプ|
|resourceId |リソースを一意に識別するリソース ID の URI|
|resourceGroupName|影響を受けるリソースのリソース グループ名|
|properties |イベントの詳細を含む <キー, 値> ペアのセット (つまり、ディクショナリ <文字列, 文字列>)|
|event|イベントに関するメタデータを含む要素|
|authorization|イベントの RBAC プロパティをキャプチャします。通常、“action”、“role”、“scope” が含まれます。|
|カテゴリ | イベントのカテゴリ。サポートされる値: Administrative、Alert、Security、ServiceHealth、Recommendation|
|caller|可用性に基づいた、操作、UPN 要求、または SPN 要求を実行したユーザーの電子メール アドレス。一部のシステム呼び出しでは、null の場合があります。|
|correlationId|	通常は GUID (文字列形式)。correlationId を含むイベントは、より大きな 1 つのアクションに属し、通常は同じ correlationId を共有します。|
|eventDescription |イベントを説明する静的テキスト|
|eventDataId|イベントの一意識別子|
|eventSource |イベントを生成した Azure サービスまたはインフラストラクチャの名前|
|httpRequest|	通常、“clientRequestId”、“clientIpAddress”、“method” (HTTP メソッド、たとえば PUT) が含まれます。|
|level|次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose”|
|operationId|通常、単一の操作に対応する複数のイベントで共有される GUID|
|operationName|操作の名前|
|properties |event 要素内の要素には、イベントのプロパティが含まれています。|
|status|操作の状態を説明する文字列。一般的な値: Started、In Progress、Succeeded、Failed、Active、Resolved|
|subStatus|	通常、対応する REST 呼び出しの HTTP 状態コードが含まれます。また、subStatus を説明する他の文字列を含めることもできます。一般的な subStatus の値: OK (HTTP Status Code: 200)、Created (HTTP Status Code: 201)、Accepted (HTTP Status Code: 202)、No Content (HTTP Status Code: 204)、Bad Request (HTTP Status Code: 400)、Not Found (HTTP Status Code: 404)、Conflict (HTTP Status Code: 409)、Internal Server Error (HTTP Status Code: 500)、Service Unavailable (HTTP Status Code:503)、Gateway Timeout (HTTP Status Code: 504)|

<!---HONumber=AcomDC_0803_2016-->