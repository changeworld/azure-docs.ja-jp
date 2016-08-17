<properties
pageTitle="ロジック アプリでの Azure Service Bus コネクタの使用方法 | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。Azure Service Bus に接続して、メッセージを送受信します。キューに送信、トピックに送信、キューから受信、サブスクリプションから受信などのアクションを実行できます。"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/02/2016"
ms.author="deonhe"/>

# Azure Service Bus コネクタの使用

Azure Service Bus に接続して、メッセージを送受信します。キューに送信、トピックに送信、キューから受信、サブスクリプションから受信などのアクションを実行できます。

[任意のコネクタ](./apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。ロジック アプリの作成方法については、[こちら](../app-service-logic/app-service-logic-create-a-logic-app.md)をご覧ください。

## Azure Service Bus への接続

ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成する必要があります。[接続](./connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。

### Azure Service Bus への接続を作成する

>[AZURE.INCLUDE [Azure Service Bus への接続を作成する手順](../../includes/connectors-create-api-servicebus.md)]

## Azure Service Bus トリガーを使用する

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらをご覧ください](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

>[AZURE.INCLUDE [Service Bus トリガーを作成する手順](../../includes/connectors-create-api-servicebus-trigger.md)]

## Azure Service Bus アクションを使用する

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらをご覧ください](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

[AZURE.INCLUDE [Service Bus アクションを作成する手順](../../includes/connectors-create-api-servicebus-action.md)]

## 技術的な詳細

ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## Azure Service Bus トリガー

Azure Service Bus には、次のトリガーがあります。

|トリガー | 説明|
|--- | ---|
|[When a message is received in a queue (キューでメッセージを受け取ったとき)](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|この操作では、キューでメッセージを受け取ったときにフローをトリガーします。|
|[When a message is received in a topic subscription (トピック サブスクリプションでメッセージを受け取ったとき)](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|この操作では、トピック サブスクリプションでメッセージを受け取ったときにフローをトリガーします。|


## Azure Service Bus アクション

Azure Service Bus には、次のアクションがあります。


|アクション|説明|
|--- | ---|
|[メッセージを送信する](connectors-create-api-servicebus.md#send-message)|この操作では、キューまたはトピックにメッセージを送信します。|
### アクションの詳細

ここでは、このコネクタのアクションとトリガー、および応答について詳しく説明します。



### メッセージを送信する
この操作では、キューまたはトピックにメッセージを送信します。


|プロパティ名| 表示名|説明|
| ---|---|---|
|ContentData*|コンテンツ|メッセージのコンテンツ|
|ContentType|コンテンツの種類|メッセージのコンテンツの種類|
|プロパティ|プロパティ|各仲介型プロパティのキーと値のペア|
|entityName*|キュー/トピック名|キューまたはトピックの名前|

他にも次の詳細パラメーターが用意されています。

|プロパティ名| 表示名|説明|
| ---|---|---|
|MessageId|メッセージ ID|これは、有効になっている場合に重複するメッセージを識別するために Service Bus が使用できる、ユーザー定義の値です。|
|To|To|送信先アドレス|
|ReplyTo|返信|返信するキューのアドレス|
|ReplyToSessionId|返信セッション ID|返信するセッションの識別子|
|ラベル|ラベル|アプリケーション固有のラベル|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|メッセージがキューに追加される日付と時刻 (UTC)|
|SessionId|セッション ID|セッションの識別子|
|CorrelationId|関連付け ID|関連付けの識別子|
|TimeToLive|有効期限|これは、メッセージが有効な期間です (ティック単位)。期間は、メッセージが Service Bus に送信されたときから開始します。|



* は、必須のプロパティを示します。




### When a message is received in a queue (キューでメッセージを受け取ったとき)
この操作では、キューでメッセージを受け取ったときにフローをトリガーします。


|プロパティ名| 表示名|説明|
| ---|---|---|
|queueName*|キュー名|キューの名前|


* は、必須のプロパティを示します。

#### 出力の詳細

ServiceBusMessage: このオブジェクトには、Service Bus メッセージのコンテンツとプロパティが含まれます。


| プロパティ名 | データ型 | 説明 |
|---|---|---|
|ContentData|string|メッセージのコンテンツ|
|ContentType|string|メッセージのコンテンツの種類|
|プロパティ|オブジェクト|各仲介型プロパティのキーと値のペア|
|MessageId|string|これは、有効になっている場合に重複するメッセージを識別するために Service Bus が使用できる、ユーザー定義の値です。|
|To|string|送信先アドレス|
|ReplyTo|string|返信するキューのアドレス|
|ReplyToSessionId|string|返信するセッションの識別子|
|ラベル|string|アプリケーション固有のラベル|
|ScheduledEnqueueTimeUtc|string|メッセージがキューに追加される日付と時刻 (UTC)|
|SessionId|string|セッションの識別子|
|CorrelationId|string|関連付けの識別子|
|TimeToLive|string|これは、メッセージが有効な期間です (ティック単位)。期間は、メッセージが Service Bus に送信されたときから開始します。|




### When a message is received in a topic subscription (トピック サブスクリプションでメッセージを受け取ったとき)
この操作では、トピック サブスクリプションでメッセージを受け取ったときにフローをトリガーします。


|プロパティ名| 表示名|説明|
| ---|---|---|
|topicName*|トピック名|トピックの名前|
|subscriptionName*|Topic subscription name (トピック サブスクリプション名)|トピック サブスクリプションの名前|


* は、必須のプロパティを示します。

#### 出力の詳細

ServiceBusMessage: このオブジェクトには、Service Bus メッセージのコンテンツとプロパティが含まれます。


| プロパティ名 | データ型 | 説明 |
|---|---|---|
|ContentData|string|メッセージのコンテンツ|
|ContentType|string|メッセージのコンテンツの種類|
|プロパティ|オブジェクト|各仲介型プロパティのキーと値のペア|
|MessageId|string|これは、有効になっている場合に重複するメッセージを識別するために Service Bus が使用できる、ユーザー定義の値です。|
|To|string|送信先アドレス|
|ReplyTo|string|返信するキューのアドレス|
|ReplyToSessionId|string|返信するセッションの識別子|
|ラベル|string|アプリケーション固有のラベル|
|ScheduledEnqueueTimeUtc|string|メッセージがキューに追加される日付と時刻 (UTC)|
|SessionId|string|セッションの識別子|
|CorrelationId|string|関連付けの識別子|
|TimeToLive|string|これは、メッセージが有効な期間です (ティック単位)。期間は、メッセージが Service Bus に送信されたときから開始します。|



## HTTP 応答

上記のアクションとトリガーは、次の HTTP 状態コードを 1 つ以上返す場合があります。

|名前|説明|
|---|---|
|200|OK|
|202|承認済み|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました。|
|default|操作に失敗しました。|

## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->