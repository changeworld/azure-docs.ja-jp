<properties
pageTitle="SendGrid | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。SendGrid 接続プロバイダーを使用して、電子メールを送信し、受信者リストを管理できます。"
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# SendGrid コネクタの使用



SendGrid コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [フロー](http://flows.microsoft.com)  

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

SendGrid コネクタは、アクションとして使用できます。SendGrid コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 SendGrid コネクタでは、次のアクションやトリガーを使用できます。

### SendGrid のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|SendGrid API を使用して電子メールを送信します (受信者は 10,000 に制限)。|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|個々の受信者を受信者リストに追加します。|
### SendGrid のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|


## SendGrid への接続の作成
SendGrid を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|ApiKey|あり|SendGrid API キーを指定します。|
 

>[AZURE.INCLUDE [SendGrid への接続を作成する手順](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

## SendGrid のリファレンス
適用されるバージョン: 1.0

## SendEmail
電子メールの送信: SendGrid API を使用して電子メールを送信します (受信者は 10,000 に制限)。

```POST: /api/mail.send.json```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|request| |○|body|なし|送信する電子メール メッセージ|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|429|要求が多すぎます|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## AddRecipientToList
リストへの受信者の追加: 個々の受信者を受信者リストに追加します。

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|listId|string|○|path|なし|受信者リストの一意の ID|
|recipientId|string|○|path|なし|受信者の一意の ID|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## オブジェクト定義 

### EmailRequest


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|from|string|はい |
|fromname|string|いいえ |
|] を [|string|あり |
|toname|string|いいえ |
|subject|string|あり |
|body|string|あり |
|ishtml|boolean|いいえ |
|cc|string|いいえ |
|ccname|string|いいえ |
|bcc|string|いいえ |
|bccname|string|いいえ |
|replyto|string|いいえ |
|date|string|いいえ |
|headers|string|いいえ |
|ファイルのアップロード|array|いいえ |
|filenames|array|いいえ |



### EmailResponse


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|message|string|いいえ |



### RecipientLists


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|lists|array|いいえ |



### RecipientList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|name|string|いいえ |
|recipient\_count|integer|いいえ |



### Recipients


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|recipients|array|いいえ |



### Recipient


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|email|string|いいえ |
|last\_name|string|いいえ |
|first\_name|string|いいえ |
|id|string|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->