<properties
pageTitle="ロジック アプリに Twilio コネクタを追加する | Microsoft Azure"
description="Twilio コネクタと REST API パラメーターの概要"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="09/19/2016"
ms.author="mandia"/>

# Twilio コネクタの使用

Twilio に接続し、グローバル SMS、MMS、IP メッセージを送受信します。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

Twilio では次のことができます。

- Twilio から取得したデータに基づいてビジネス フローを構築します。
- メッセージを取得したり、メッセージを一覧表示したりするアクションを使用します。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、新しい Twilio メッセージを取得したとき、そのメッセージを Service Bus ワークフローで利用できます。

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
Twilio コネクタには、次のアクションがあります。トリガーはありません。

| トリガー | アクション|
| --- | --- |
|なし| <ul><li>メッセージを取得する</li><li>メッセージを一覧表示する</li><li>メッセージを送信する</li></ul>|

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## Twilio への接続を作成する
このコネクタをロジック アプリに追加するときに、次の Twilio 値を入力します。

|プロパティ| 必須|Description|
| ---|---|---|
|Account ID|はい|Twilio アカウント ID を入力します。|
|Access Token|はい|Twilio アカウント トークンを入力します。|

>[AZURE.INCLUDE [Twilio への接続を作成する手順](../../includes/connectors-create-api-twilio.md)]

アクセス トークンがない場合は、[Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) を参照してアクセス トークンを作成してください。


>[AZURE.TIP] 他のロジック アプリでこの同じ Twilio 接続を使用できます。

## Swagger REST API リファレンス
#### このドキュメントの対象バージョン: 1.0

### メッセージを取得する
入力したメッセージ ID で指定された 1 つのメッセージを返します。```GET: /Messages/{MessageId}.json```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|MessageId|string|○|path|なし|メッセージ ID|

### 応答
|Name|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|404|メッセージが見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### メッセージを一覧表示する
自分のアカウントに関連付けられているメッセージの一覧を返します。```GET: /Messages.json```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|To|string|×|query|なし|宛先の電話番号|
|From|string|×|query|なし|送信元の電話番号|
|DateSent|string|×|query|なし|この YYYY-MM-DD 日付 (GMT 形式) で送信されたメッセージのみを表示します。例: DateSent=2009-07-06。不等号も指定できます。たとえば、ある日付の真夜中以前に送信されたメッセージの場合は「DateSent<=YYYY-MM-DD」と、ある日付の真夜中以後に送信されたメッセージの場合は「DateSent>=YYYY-MM-DD」と指定できます。|
|PageSize|integer|×|query|50|各リスト ページで返されるリソースの数。既定は 50 です。|
|ページ|integer|×|query|0|ページ番号。既定値は 0 です。|

### 応答
|Name|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|



### メッセージを送信する
携帯電話番号に新しいメッセージを送信します。```POST: /Messages.json```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|sendMessageRequest| |○|body|なし|送信するメッセージ|

### 応答
|Name|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## オブジェクト定義

#### SendMessageRequest: メッセージ送信操作の要求モデル

|プロパティ名 | データ型 | 必須|
|---|---|---|
|from|string|○|
|to|string|○|
|body|string|○|
|media\_url|array|×|
|status\_callback|string|×|
|messaging\_service\_sid|string|×|
|application\_sid|string|×|
|max\_price|string|×|


#### Message: メッセージのモデル

|プロパティ名 | データ型 |必須|
|---|---|---|
|body|string|×|
|from|string|×|
|to|string|×|
|status|string|×|
|sid|string|×|
|account\_sid|string|×|
|api\_version|string|×|
|num\_segments|string|×|
|num\_media|string|×|
|date\_created|string|×|
|date\_sent|string|×|
|date\_updated|string|×|
|direction|string|×|
|error\_code|string|×|
|error\_message|string|×|
|price|string|×|
|price\_unit|string|×|
|uri|string|×|
|subresource\_uris|array|×|
|messaging\_service\_sid|string|×|

#### MessageList: メッセージの一覧表示操作の応答モデル

|プロパティ名 | データ型 |必須|
|---|---|---|
|messages|array|×|
|page|integer|×|
|page\_size|integer|×|
|num\_pages|integer|×|
|uri|string|×|
|first\_page\_uri|string|×|
|next\_page\_uri|string|×|
|total|integer|×|
|previous\_page\_uri|string|×|

#### IncomingPhoneNumberList: メッセージの一覧表示操作の応答モデル

|プロパティ名 | データ型 |必須|
|---|---|---|
|incoming\_phone\_numbers|array|×|
|page|integer|×|
|page\_size|integer|×|
|num\_pages|integer|×|
|uri|string|×|
|first\_page\_uri|string|×|
|next\_page\_uri|string|×|


#### AddIncomingPhoneNumberRequest: 着信番号追加操作の要求モデル

|プロパティ名 | データ型 |必須|
|---|---|---|
|PhoneNumber|string|○|
|AreaCode|string|×|
|FriendlyName|string|×|


#### IncomingPhoneNumber: 着信電話番号

|プロパティ名 | データ型 |必須|
|---|---|---|
|phone\_number|string|×|
|friendly\_name|string|×|
|sid|string|×|
|account\_sid|string|×|
|date\_created|string|×|
|date\_updated|string|×|
|capabilities|未定義|×|
|status\_callback|string|×|
|status\_callback\_method|string|×|
|api\_version|string|×|


#### Capabilities: 電話番号機能

|プロパティ名 | データ型 |必須|
|---|---|---|
|mms|boolean|×|
|sms|boolean|×|
|voice|boolean|×|

#### AvailablePhoneNumbers: 利用可能な電話番号

|プロパティ名 | データ型 |必須|
|---|---|---|
|phone\_number|string|×|
|friendly\_name|string|×|
|lata|string|×|
|latitude|string|×|
|longitude|string|×|
|postal\_code|string|×|
|rate\_center|string|×|
|region|string|×|
|MMS|boolean|×|
|SMS|boolean|×|
|voice|boolean|×|


#### UsageRecords: 使用状況レコード クラス

|プロパティ名 | データ型 |必須|
|---|---|---|
|カテゴリ|string|×|
|usage|string|×|
|usage\_unit|string|×|
|description|string|×|
|price|number|×|
|price\_unit|string|×|
|count|string|×|
|count\_unit|string|×|
|start\_date|string|×|
|end\_date|string|×|


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0921_2016-->