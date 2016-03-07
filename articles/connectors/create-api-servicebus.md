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
ms.date="02/18/2016"
ms.author="deonhe"/>

# Azure Service Bus API の概要

Azure Service Bus に接続して、メッセージを送受信します。キューに送信、トピックに送信、キューから受信、サブスクリプションから受信などのアクションを実行できます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、[Azure Service Bus] (../app-service-logic/app-service-logic-connector-Azure Service Bus.md) をクリックしてください。

Azure Service Bus では、次の操作を実行できます。

* ロジック アプリの構築  

ロジック アプリでの操作の追加については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」をご覧ください。

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

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Azure Service Bus REST API リファレンス
#### このドキュメントはバージョン 1.0 を対象としています。


### メッセージを Azure Service Bus キューまたはトピックに送信します。
**```POST: /{entityName}/messages```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|message| |○|本文|なし|Service Bus メッセージ|
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

<!---HONumber=AcomDC_0224_2016-->