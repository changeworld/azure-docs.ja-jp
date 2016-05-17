<properties
pageTitle="Outlook.com |Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。Outlook.com コネクタでは、メール、予定表、連絡先を管理できます。メールを送信する、会議のスケジュールを設定する、連絡先を追加するなど、さまざまなアクションを実行できます。"
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
ms.date="04/29/2016"
ms.author="deonhe"/>

# Outlook.com コネクタの使用

Outlook.com コネクタでは、メール、予定表、連絡先を管理できます。メールを送信する、会議のスケジュールを設定する、連絡先を追加するなど、さまざまなアクションを実行できます。

Outlook.com コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flow](http://flow.microsoft.com)  

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。「[ロジック アプリを作成する](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション

Outlook.com コネクタは、アクションとして使用できます。Outlook.com コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 Outlook.com コネクタでは、次のアクションやトリガーを使用できます。

### Outlook.com のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|フォルダーから電子メールを取得します|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|電子メールを送信します|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|ID を指定して電子メールを削除します|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|電子メールを既読としてマークします|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|電子メールに返信します|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|ID を指定して電子メールの添付ファイルを取得します|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|承認の電子メールを送信し、受信者からの返信を待ちます|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|予定表を取得します|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|予定表から項目を取得します|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|新しいイベントを作成します|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|予定表から特定の項目を取得します|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|予定表項目を削除します|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|予定表項目の一部を更新します|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|連絡先フォルダーを取得します|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|連絡先フォルダーから連絡先を取得します|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|新しい連絡先を作成します|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|連絡先フォルダーから特定の連絡先を取得します|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|連絡先を削除します|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|連絡先の一部を更新します|
### Outlook.com のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|
|イベントが間もなく開始されるとき|予定表イベントが間もなく開始されるときにフローをトリガーします|
|新しい電子メールの着信時|新しい電子メールが着信したときにフローをトリガーします|
|新しい項目の作成時|新しい予定表項目が作成されたときにトリガーされます|
|項目の更新時|予定表項目が変更されたときにトリガーされます|


## Outlook.com への接続を作成する
Outlook.com を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|あり|Outlook.com の資格情報を提供します|
接続を作成したら、接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Outlook.com のリファレンス
適用されるバージョン: 1.0

## OnUpcomingEvents
イベントが間もなく開始されるとき: 予定表イベントが間もなく開始されるときにフローをトリガーします

```GET: /Events/OnUpcomingEvents```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|query|なし|予定表の一意識別子|
|lookAheadTimeInMinutes|integer|×|query|15|間もなく開始されるイベントまでの時間 (分)|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## GetEmails
電子メールを取得する: フォルダーから電子メールを取得します

```GET: /Mail```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|×|query|Inbox|電子メールを取得するフォルダーのパス (既定値: 'Inbox')|
|top|integer|×|query|10|取得する電子メールの件数 (既定値: 10)|
|fetchOnlyUnread|ブール値|×|query|true|未読の電子メールのみを取得しますか？|
|includeAttachments|ブール値|×|query|false|True に設定すると、電子メールと共に添付ファイルも取得されます|
|searchQuery|string|×|query|なし|電子メールをフィルターする検索クエリ|
|skip|integer|×|query|0|スキップする電子メールの件数 (既定値: 0)|
|skipToken|string|×|query|なし|新しいページを取得するスキップ トークン|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## SendEmail
電子メールを送信する: 電子メールを送信します

```POST: /Mail```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|emailMessage| |○|body|なし|電子メール|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## DeleteEmail
電子メールを削除する: ID を指定して電子メールを削除します

```DELETE: /Mail/{messageId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|messageId|string|○|path|なし|削除する電子メールの ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## MarkAsRead
既読としてマークする: 電子メールを既読としてマークします

```POST: /Mail/MarkAsRead/{messageId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|messageId|string|○|path|なし|既読とマークする電子メールの ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## ReplyTo
電子メールに返信する: 電子メールに返信します

```POST: /Mail/ReplyTo/{messageId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|messageId|string|○|path|なし|返信する電子メールの ID|
|comment|string|○|query|なし|応答のコメント|
|replyAll|ブール値|×|query|false|すべての受信者に返信する|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## GetAttachment
添付ファイルを取得する: ID を指定して電子メールの添付ファイルを取得します

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|messageId|string|○|path|なし|電子メールの ID|
|attachmentId|string|○|path|なし|ダウンロードする添付ファイルの ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## OnNewEmail
新しい電子メールの着信時: 新しい電子メールが着信したときにフローをトリガーします

```GET: /Mail/OnNewEmail```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|×|query|Inbox|取得する電子メール フォルダー (既定値: 受信トレイ)。|
|to|string|×|query|なし|受信者の電子メール アドレス|
|from|string|×|query|なし|差出人アドレス|
|importance|string|×|query|Normal|電子メールの重要度 (High、Normal、Low) (既定値: Normal)|
|fetchOnlyWithAttachment|ブール値|×|query|false|ファイルが添付された電子メールのみを取得します|
|includeAttachments|ブール値|×|query|false|添付ファイルを含めます|
|subjectFilter|string|×|query|なし|サブジェクト内で検索する文字列|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|承認済み|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## SendMailWithOptions
オプションを指定して電子メールを送信する: 複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます

```POST: /mailwithoptions/$subscriptions```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |○|body|なし|オプションの電子メールのサブスクリプション要求|

#### Response

|名前|説明|
|---|---|
|200|OK|
|201|サブスクリプションの作成|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## SendApprovalMail
承認の電子メールを送信する: 承認の電子メールを送信し、受信者からの返信を待ちます

```POST: /approvalmail/$subscriptions```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |○|body|なし|承認の電子メールのサブスクリプション要求|

#### Response

|名前|説明|
|---|---|
|200|OK|
|201|サブスクリプションの作成|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## CalendarGetTables
予定表を取得する: 予定表を取得します

```GET: /datasets/calendars/tables```

この呼び出しには、パラメーターはありません
#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CalendarGetItems
イベントを取得する: 予定表から項目を取得します

```GET: /datasets/calendars/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|取得する予定表の一意識別子|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CalendarPostItem
イベントを作成する: 新しいイベントを作成します

```POST: /datasets/calendars/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|item| |○|body|なし|作成する予定表項目|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CalendarGetItem
イベントを取得する: 予定表から特定の項目を取得します

```GET: /datasets/calendars/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|id|string|○|path|なし|取得する予定表項目の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CalendarDeleteItem
イベントを削除する: 予定表から項目を削除します

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|id|string|○|path|なし|削除する予定表項目の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CalendarPatchItem
イベントを更新する: 予定表項目の一部を更新します

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|id|string|○|path|なし|更新する予定表項目の一意識別子|
|item| |○|body|なし|更新する予定表項目|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CalendarGetOnNewItems
新しい項目に対して: 新しい予定表項目が作成されたときにトリガーされます

```GET: /datasets/calendars/tables/{table}/onnewitems```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CalendarGetOnUpdatedItems
更新された項目に対して: 予定表項目が変更されたときにトリガーされます

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ContactGetTables
連絡先フォルダーを取得する: 連絡先フォルダーを取得します

```GET: /datasets/contacts/tables```

この呼び出しには、パラメーターはありません
#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ContactGetItems
連絡先を取得する: 連絡先フォルダーから連絡先を取得します

```GET: /datasets/contacts/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|取得する連絡先フォルダーの一意識別子|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ContactPostItem
連絡先を作成する: 新しい連絡先を作成します

```POST: /datasets/contacts/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|連絡先フォルダーの一意識別子|
|item| |○|body|なし|作成する連絡先|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ContactGetItem
連絡先を取得する: 連絡先フォルダーから特定の連絡先を取得します

```GET: /datasets/contacts/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|連絡先フォルダーの一意識別子|
|id|string|○|path|なし|取得する連絡先の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ContactDeleteItem
連絡先を削除する: 連絡先を削除します

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|連絡先フォルダーの一意識別子|
|id|string|○|path|なし|削除する連絡先の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ContactPatchItem
連絡先を更新する: 連絡先の一部を更新します

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|連絡先フォルダーの一意識別子|
|id|string|○|path|なし|更新する連絡先の一意識別子|
|item| |○|body|なし|更新する連絡先項目|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## オブジェクト定義 

### TriggerBatchResponse[IDictionary[String,Object]]


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### オブジェクト


| プロパティ名 | データ型 | 必須 |
|---|---|---|



### SendMessage


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|[添付ファイル]|array|いいえ |
|ファイル|string|いいえ |
|Cc|string|いいえ |
|[Bcc]|string|いいえ |
|[件名]|string|あり |
|本文|string|はい |
|[重要度]|string|いいえ |
|IsHtml|boolean|いいえ |
|To|string|あり |



### SendAttachment


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|@odata.type|string|いいえ |
|名前|string|あり |
|ContentBytes|string|あり |



### ReceiveMessage


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ID|string|いいえ |
|IsRead|boolean|いいえ |
|HasAttachment|boolean|いいえ |
|DateTimeReceived|string|いいえ |
|[添付ファイル]|array|いいえ |
|ファイル|string|いいえ |
|Cc|string|いいえ |
|[Bcc]|string|いいえ |
|[件名]|string|あり |
|本文|string|あり |
|[重要度]|string|いいえ |
|IsHtml|boolean|いいえ |
|To|string|はい |



### ReceiveAttachment


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ID|string|はい |
|ContentType|string|あり |
|@odata.type|string|いいえ |
|名前|string|はい |
|ContentBytes|string|あり |



### DigestMessage


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|[件名]|string|あり |
|本文|string|いいえ |
|[重要度]|string|いいえ |
|Digest|array|はい |
|[添付ファイル]|array|いいえ |
|To|string|はい |



### TriggerBatchResponse[ReceiveMessage]


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### DataSetsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|tabular|未定義|いいえ |
|BLOB|未定義|いいえ |



### TabularDataSetsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|source セクション|string|いいえ |
|displayName|string|いいえ |
|urlEncoding|string|いいえ |
|tableDisplayName|string|いいえ |
|tablePluralName|string|いいえ |



### BlobDataSetsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|source セクション|string|いいえ |
|displayName|string|いいえ |
|urlEncoding|string|いいえ |



### TableMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|name|string|いいえ |
|title|string|いいえ |
|x-ms-permission|string|いいえ |
|x-ms-capabilities|未定義|いいえ |
|schema|未定義|いいえ |



### TableCapabilitiesMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|sortRestrictions|未定義|いいえ |
|filterRestrictions|未定義|いいえ |
|filterFunctions|array|いいえ |



### TableSortRestrictionsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|sortable|boolean|いいえ |
|unsortableProperties|array|いいえ |
|ascendingOnlyProperties|array|いいえ |



### TableFilterRestrictionsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|filterable|boolean|いいえ |
|nonFilterableProperties|array|いいえ |
|requiredProperties|array|いいえ |



### OptionsEmailSubscription


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|NotificationUrl|string|いいえ |
|メッセージ|未定義|いいえ |



### MessageWithOptions


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|[件名]|string|はい |
|オプション|string|あり |
|本文|string|いいえ |
|[重要度]|string|いいえ |
|[添付ファイル]|array|いいえ |
|To|string|あり |



### SubscriptionResponse


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|いいえ |
|resource|string|いいえ |
|notificationType|string|いいえ |
|notificationUrl|string|いいえ |



### ApprovalEmailSubscription


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|NotificationUrl|string|いいえ |
|メッセージ|未定義|いいえ |



### ApprovalMessage


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|[件名]|string|あり |
|オプション|string|あり |
|本文|string|いいえ |
|[重要度]|string|いいえ |
|[添付ファイル]|array|いいえ |
|To|string|あり |



### ApprovalEmailResponse


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|SelectedOption|string|いいえ |



### TablesList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### テーブル


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|いいえ |
|DisplayName|string|いいえ |



### 項目


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ItemInternalId|string|いいえ |



### CalendarItemsList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### CalendarItem


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ItemInternalId|string|いいえ |



### ContactItemsList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### ContactItem


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ItemInternalId|string|いいえ |



### DataSetsList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### DataSet


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|いいえ |
|DisplayName|string|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->