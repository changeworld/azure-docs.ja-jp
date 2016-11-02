<properties
pageTitle="SendGrid | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。SendGrid 接続プロバイダーを使用して、電子メールを送信し、受信者リストを管理できます。"
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# SendGrid コネクタの使用

SendGrid 接続プロバイダーを使用して、電子メールを送信し、受信者リストを管理できます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

SendGrid コネクタは、アクションとして使用できます。SendGrid コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 SendGrid コネクタで使用可能なアクションは次のとおりです。トリガーはありません。

### SendGrid のアクション
実行できるアクションは以下のとおりです。

|アクション|Description|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|SendGrid API を使用して電子メールを送信します (受信者は 10,000 に制限)。|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|個々の受信者を受信者リストに追加します。|


## SendGrid への接続の作成
SendGrid を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|Description|
| ---|---|---|
|ApiKey|はい|SendGrid API キーを指定します。|
 

>[AZURE.INCLUDE [SendGrid への接続を作成する手順](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

## SendGrid のリファレンス
適用されるバージョン: 1.0

## SendEmail
電子メールの送信: SendGrid API を使用して電子メールを送信します (受信者は 10,000 に制限)。

```POST: /api/mail.send.json```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|request| |○|body|なし|送信する電子メール メッセージ|

#### 応答

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

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|listId|string|○|path|なし|受信者リストの一意の ID|
|recipientId|string|○|path|なし|受信者の一意の ID|

#### 応答

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
|fromname|string|なし |
|to|string|はい |
|toname|string|なし |
|subject|string|はい |
|body|string|はい |
|ishtml|boolean|なし |
|cc|string|なし |
|ccname|string|なし |
|bcc|string|なし |
|bccname|string|なし |
|replyto|string|なし |
|date|string|なし |
|headers|string|なし |
|ファイルのアップロード|array|なし |
|filenames|array|なし |



### EmailResponse


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|message|string|なし |



### RecipientLists


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|lists|array|なし |



### RecipientList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|name|string|なし |
|recipient\_count|integer|なし |



### Recipients


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|recipients|array|なし |



### Recipient


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|電子メール|string|なし |
|last\_name|string|なし |
|first\_name|string|なし |
|id|string|なし |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->