---
title: Outlook.com | Microsoft Docs
description: Azure App Service を使用してロジック アプリを作成します。Outlook.com コネクタでは、メール、予定表、連絡先を管理できます。メールを送信する、会議のスケジュールを設定する、連絡先を追加するなど、さまざまなアクションを実行できます。
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# Outlook.com コネクタの使用
Outlook.com コネクタでは、メール、予定表、連絡先を管理できます。メールを送信する、会議のスケジュールを設定する、連絡先を追加するなど、さまざまなアクションを実行できます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション
Outlook.com コネクタは、アクションとして使用できます。Outlook.com コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 Outlook.com コネクタでは、次のアクションやトリガーを使用できます。

### Outlook.com のアクション
実行できるアクションは以下のとおりです。

| アクション | Description |
| --- | --- |
| [GetEmails](connectors-create-api-outlook.md#GetEmails) |フォルダーから電子メールを取得します |
| [SendEmail](connectors-create-api-outlook.md#SendEmail) |電子メールを送信します |
| [DeleteEmail](connectors-create-api-outlook.md#DeleteEmail) |ID を指定して電子メールを削除します |
| [MarkAsRead](connectors-create-api-outlook.md#MarkAsRead) |電子メールを既読としてマークします |
| [ReplyTo](connectors-create-api-outlook.md#ReplyTo) |電子メールに返信します |
| [GetAttachment](connectors-create-api-outlook.md#GetAttachment) |ID を指定して電子メールの添付ファイルを取得します |
| [SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions) |複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます |
| [SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail) |承認の電子メールを送信し、受信者からの返信を待ちます |
| [CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables) |予定表を取得します |
| [CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems) |予定表から項目を取得します |
| [CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem) |新しいイベントを作成します |
| [CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem) |予定表から特定の項目を取得します |
| [CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem) |予定表項目を削除します |
| [CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem) |予定表項目の一部を更新します |
| [ContactGetTables](connectors-create-api-outlook.md#ContactGetTables) |連絡先フォルダーを取得します |
| [ContactGetItems](connectors-create-api-outlook.md#ContactGetItems) |連絡先フォルダーから連絡先を取得します |
| [ContactPostItem](connectors-create-api-outlook.md#ContactPostItem) |新しい連絡先を作成します |
| [ContactGetItem](connectors-create-api-outlook.md#ContactGetItem) |連絡先フォルダーから特定の連絡先を取得します |
| [ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem) |連絡先を削除します |
| [ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem) |連絡先の一部を更新します |

### Outlook.com のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| イベントが間もなく開始されるとき |予定表イベントが間もなく開始されるときにフローをトリガーします |
| 新しい電子メールの着信時 |新しい電子メールが着信したときにフローをトリガーします |
| 新しい項目の作成時 |新しい予定表項目が作成されたときにトリガーされます |
| 項目の更新時 |予定表項目が変更されたときにトリガーされます |

## Outlook.com への接続を作成する
Outlook.com を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | Description |
| --- | --- | --- |
| トークン |はい |Outlook.com の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Outlook.com への接続を作成する手順](../../includes/connectors-create-api-outlook.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## Outlook.com のリファレンス
適用されるバージョン: 1.0

## OnUpcomingEvents
イベントが間もなく開始されるとき: 予定表イベントが間もなく開始されるときにフローをトリガーします

```GET: /Events/OnUpcomingEvents```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |query |なし |予定表の一意識別子 |
| lookAheadTimeInMinutes |integer |× |query |15 |間もなく開始されるイベントまでの時間 (分) |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## GetEmails
電子メールを取得する: フォルダーから電子メールを取得します

```GET: /Mail```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |× |query |Inbox |電子メールを取得するフォルダーのパス (既定値: 'Inbox') |
| top |integer |× |query |10 |取得する電子メールの件数 (既定値: 10) |
| fetchOnlyUnread |boolean |× |query |true |未読の電子メールのみを取得しますか？ |
| includeAttachments |boolean |× |query |false |True に設定すると、電子メールと共に添付ファイルも取得されます |
| searchQuery |string |× |query |なし |電子メールをフィルターする検索クエリ |
| skip |integer |× |query |0 |スキップする電子メールの件数 (既定値: 0) |
| skipToken |string |× |query |なし |新しいページを取得するスキップ トークン |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## SendEmail
電子メールを送信する: 電子メールを送信します

```POST: /Mail```

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| emailMessage | |○ |body |なし |電子メール |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## DeleteEmail
電子メールを削除する: ID を指定して電子メールを削除します

```DELETE: /Mail/{messageId}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| messageId |string |○ |path |なし |削除する電子メールの ID |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## MarkAsRead
既読としてマークする: 電子メールを既読としてマークします

```POST: /Mail/MarkAsRead/{messageId}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| messageId |string |○ |path |なし |既読とマークする電子メールの ID |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## ReplyTo
電子メールに返信する: 電子メールに返信します

```POST: /Mail/ReplyTo/{messageId}```

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| messageId |string |○ |path |なし |返信する電子メールの ID |
| comment |string |○ |query |なし |応答のコメント |
| replyAll |boolean |× |query |false |すべての受信者に返信する |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## GetAttachment
添付ファイルを取得する: ID を指定して電子メールの添付ファイルを取得します

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| messageId |string |○ |path |なし |電子メールの ID |
| attachmentId |string |○ |path |なし |ダウンロードする添付ファイルの ID |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## OnNewEmail
新しい電子メールの着信時: 新しい電子メールが着信したときにフローをトリガーします

```GET: /Mail/OnNewEmail```

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |× |query |Inbox |取得する電子メール フォルダー (既定値: 受信トレイ)。 |
| to |string |× |query |なし |受信者の電子メール アドレス |
| from |string |× |query |なし |差出人アドレス |
| importance |string |× |query |Normal |電子メールの重要度 (High、Normal、Low) (既定値: Normal) |
| fetchOnlyWithAttachment |boolean |× |query |false |ファイルが添付された電子メールのみを取得します |
| includeAttachments |boolean |× |query |false |添付ファイルを含めます |
| subjectFilter |string |× |query |なし |サブジェクト内で検索する文字列 |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |承認済み |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## SendMailWithOptions
オプションを指定して電子メールを送信する: 複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます

```POST: /mailwithoptions/$subscriptions```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| optionsEmailSubscription | |○ |body |なし |オプションの電子メールのサブスクリプション要求 |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| 201 |サブスクリプションが作成されました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## SendApprovalMail
承認の電子メールを送信する: 承認の電子メールを送信し、受信者からの返信を待ちます

```POST: /approvalmail/$subscriptions```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| approvalEmailSubscription | |○ |body |なし |承認の電子メールのサブスクリプション要求 |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| 201 |サブスクリプションが作成されました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## CalendarGetTables
予定表を取得する: 予定表を取得します

```GET: /datasets/calendars/tables```

この呼び出しには、パラメーターはありません

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## CalendarGetItems
イベントを取得する: 予定表から項目を取得します

```GET: /datasets/calendars/tables/{table}/items```

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |取得する予定表の一意識別子 |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## CalendarPostItem
イベントを作成する: 新しいイベントを作成します

```POST: /datasets/calendars/tables/{table}/items```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |予定表の一意識別子 |
| item | |○ |body |なし |作成する予定表項目 |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## CalendarGetItem
イベントを取得する: 予定表から特定の項目を取得します

```GET: /datasets/calendars/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |予定表の一意識別子 |
| id |string |○ |path |なし |取得する予定表項目の一意識別子 |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## CalendarDeleteItem
イベントを削除する: 予定表から項目を削除します

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |予定表の一意識別子 |
| id |string |○ |path |なし |削除する予定表項目の一意識別子 |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## CalendarPatchItem
イベントを更新する: 予定表項目の一部を更新します

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |予定表の一意識別子 |
| id |string |○ |path |なし |更新する予定表項目の一意識別子 |
| item | |○ |body |なし |更新する予定表項目 |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## CalendarGetOnNewItems
新しい項目に対して: 新しい予定表項目が作成されたときにトリガーされます

```GET: /datasets/calendars/tables/{table}/onnewitems```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |予定表の一意識別子 |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## CalendarGetOnUpdatedItems
更新された項目に対して: 予定表項目が変更されたときにトリガーされます

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |予定表の一意識別子 |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## ContactGetTables
連絡先フォルダーを取得する: 連絡先フォルダーを取得します

```GET: /datasets/contacts/tables```

この呼び出しには、パラメーターはありません

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## ContactGetItems
連絡先を取得する: 連絡先フォルダーから連絡先を取得します

```GET: /datasets/contacts/tables/{table}/items```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |取得する連絡先フォルダーの一意識別子 |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## ContactPostItem
連絡先を作成する: 新しい連絡先を作成します

```POST: /datasets/contacts/tables/{table}/items```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |連絡先フォルダーの一意識別子 |
| item | |○ |body |なし |作成する連絡先 |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## ContactGetItem
連絡先を取得する: 連絡先フォルダーから特定の連絡先を取得します

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |連絡先フォルダーの一意識別子 |
| id |string |○ |path |なし |取得する連絡先の一意識別子 |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## ContactDeleteItem
連絡先を削除する: 連絡先を削除します

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |連絡先フォルダーの一意識別子 |
| id |string |○ |path |なし |削除する連絡先の一意識別子 |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## ContactPatchItem
連絡先を更新する: 連絡先の一部を更新します

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |path |なし |連絡先フォルダーの一意識別子 |
| id |string |○ |path |なし |更新する連絡先の一意識別子 |
| item | |○ |body |なし |更新する連絡先項目 |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## オブジェクト定義
### TriggerBatchResponse[IDictionary[String,Object]]
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### オブジェクト
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
|  | | |

### SendMessage
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| [添付ファイル] |array |なし |
| From |string |なし |
| Cc |string |なし |
| [Bcc] |string |なし |
| [件名] |string |はい |
| 本文 |string |はい |
| [重要度] |string |なし |
| IsHtml |boolean |なし |
| To |string |はい |

### SendAttachment
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| @odata.type |string |なし |
| 名前 |string |はい |
| ContentBytes |string |はい |

### ReceiveMessage
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ID |string |なし |
| IsRead |boolean |なし |
| HasAttachment |boolean |なし |
| DateTimeReceived |string |なし |
| [添付ファイル] |array |なし |
| From |string |なし |
| Cc |string |なし |
| [Bcc] |string |なし |
| [件名] |string |はい |
| 本文 |string |はい |
| [重要度] |string |なし |
| IsHtml |boolean |なし |
| To |string |はい |

### ReceiveAttachment
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ID |string |はい |
| ContentType |string |はい |
| @odata.type |string |なし |
| 名前 |string |はい |
| ContentBytes |string |はい |

### DigestMessage
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| [件名] |string |はい |
| 本文 |string |なし |
| [重要度] |string |なし |
| Digest |array |はい |
| [添付ファイル] |array |なし |
| To |string |はい |

### TriggerBatchResponse[ReceiveMessage]
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### DataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| tabular |未定義 |なし |
| BLOB |未定義 |なし |

### TabularDataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| source |string |なし |
| displayName |string |なし |
| urlEncoding |string |なし |
| tableDisplayName |string |なし |
| tablePluralName |string |なし |

### BlobDataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| source |string |なし |
| displayName |string |なし |
| urlEncoding |string |なし |

### TableMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| name |string |なし |
| title |string |なし |
| x-ms-permission |string |なし |
| x-ms-capabilities |未定義 |なし |
| schema |未定義 |なし |

### TableCapabilitiesMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| sortRestrictions |未定義 |なし |
| filterRestrictions |未定義 |なし |
| filterFunctions |array |なし |

### TableSortRestrictionsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| sortable |boolean |なし |
| unsortableProperties |array |なし |
| ascendingOnlyProperties |array |なし |

### TableFilterRestrictionsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| filterable |boolean |なし |
| nonFilterableProperties |array |なし |
| requiredProperties |array |なし |

### OptionsEmailSubscription
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| NotificationUrl |string |なし |
| メッセージ |未定義 |なし |

### MessageWithOptions
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| [件名] |string |はい |
| オプション |string |はい |
| 本文 |string |なし |
| [重要度] |string |なし |
| [添付ファイル] |array |なし |
| To |string |はい |

### SubscriptionResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |なし |
| resource |string |なし |
| notificationType |string |なし |
| notificationUrl |string |なし |

### ApprovalEmailSubscription
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| NotificationUrl |string |なし |
| メッセージ |未定義 |なし |

### ApprovalMessage
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| [件名] |string |はい |
| オプション |string |はい |
| 本文 |string |なし |
| [重要度] |string |なし |
| [添付ファイル] |array |なし |
| To |string |はい |

### ApprovalEmailResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| SelectedOption |string |なし |

### TablesList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### テーブル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |なし |
| DisplayName |string |なし |

### 項目
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ItemInternalId |string |なし |

### CalendarItemsList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### CalendarItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ItemInternalId |string |なし |

### ContactItemsList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### ContactItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ItemInternalId |string |なし |

### DataSetsList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### DataSet
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |なし |
| DisplayName |string |なし |

## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->