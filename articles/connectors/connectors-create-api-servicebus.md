<properties
pageTitle="Logic Apps での Azure Service Bus API の使用 | Microsoft Azure"
description="Microsoft Azure App Service Logic Apps で Azure Service Bus API (コネクタ) の使用を開始する"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="03/16/2016"
ms.author="deonhe"/>

# Azure Service Bus API の概要

Azure Service Bus に接続して、メッセージを送受信します。キューに送信、トピックに送信、キューから受信、サブスクリプションから受信などのアクションを実行できます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、「[Azure Service Bus コネクタの使用開始とロジック アプリへの追加](../app-service-logic/app-service-logic-connector-azureservicebus.md)」をご覧ください。

Azure Service Bus では、次の操作を実行できます。

* ロジック アプリの構築  

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション

Azure Service Bus API はアクションとして使用できます。これにはトリガーがあります。すべての API は、JSON および XML 形式のデータに対応します。

 Azure Service Bus API では、次のアクションやトリガーを使用できます。

### Azure Service Bus アクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|SendMessage|メッセージを Azure Service Bus キューまたはトピックに送信します。|
### Azure Service Bus トリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|
|GetMessageFromQueue|Azure Service Bus キューから新しいメッセージを取得します。|
|GetMessageFromTopic|Azure Service Bus トピック サブスクリプションから新しいメッセージを取得します。|


## Azure Service Bus への接続を作成する
Azure Service Bus API を使用するには、最初に**接続**を作成し、以下のプロパティの詳細を指定します。

|プロパティ| 必須|説明|
| ---|---|---|
|ConnectionString|あり|Azure Service Bus 接続文字列の指定|  

次の手順に従って、ロジック アプリで使用できる Service Bus **接続**を作成します。

1. **[繰り返し]** を選択します。
2. **[頻度]** を選択し、**[間隔]** を入力します。![Service Bus の構成][1] 
3. **[アクションの追加]** を選択します。![Service Bus の構成][2]   
4. 検索ボックスに「**Service Bus**」と入力し、名前に Service Bus が含まれたすべてのエントリが返されるまで待ちます。
5. **[Service Bus - メッセージの送信]** を選択します。![Service Bus の構成][3]
7. **接続名**と**接続文字列**を入力し、**[接続の作成]** をクリックします。![Service Bus の構成][4]
7. 接続が作成されると、**[メッセージの送信]** ダイアログが表示されます。メッセージを送信するために必要な情報をすべて入力します。![Service Bus の構成][5]
8. 上部のメニューにある **[保存]** ボタンをクリックして、作業内容を保存します。    

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Azure Service Bus REST API リファレンス
#### このドキュメントはバージョン 1.0 を対象としています。


### メッセージを Azure Service Bus キューまたはトピックに送信します。
**```POST: /{entityName}/messages```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|message| |○|body|なし|Service Bus メッセージ|
|entityName|string|○|path|なし|キューまたはトピックの名前|


### 考えられる応答は以下のとおりです。

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|
------



### Azure Service Bus キューから新しいメッセージを取得します。
**```GET: /{queueName}/messages/head```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|queueName|string|○|path|なし|キューの名前。|


### 考えられる応答は以下のとおりです。

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|
------



### Azure Service Bus トピック サブスクリプションから新しいメッセージを取得します。
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|topicName|string|○|path|なし|トピックの名前。|
|subscriptionName|string|○|path|なし|トピック サブスクリプションの名前。|


### 考えられる応答は以下のとおりです。

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|
------



## オブジェクト定義: 

 **ServiceBusMessage**: メッセージはコンテンツとプロパティで構成されます

ServiceBusMessage の必須プロパティ:

ContentTransferEncoding

**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|ContentData|string|
|ContentType|string|
|ContentTransferEncoding|string|
|プロパティ|オブジェクト|


## 次のステップ
[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

[1]: ./media/connectors-create-api-servicebus/connectionconfig1.png
[2]: ./media/connectors-create-api-servicebus/connectionconfig2.png
[3]: ./media/connectors-create-api-servicebus/connectionconfig3.png
[4]: ./media/connectors-create-api-servicebus/connectionconfig4.png
[5]: ./media/connectors-create-api-servicebus/connectionconfig5.png
[6]: ./media/connectors-create-api-servicebus/connectionconfig6.png

<!---HONumber=AcomDC_0323_2016-->