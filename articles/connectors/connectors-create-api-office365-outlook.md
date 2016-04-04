<properties
	pageTitle="PowerApps Enterprise または Logic Apps に Office 365 Outlook API を追加する | Microsoft Azure"
	description="Office 365 Outlook API と REST API パラメーターの概要"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/16/2016"
ms.author="mandia"/>

# Office 365 Outlook API の概要 

Office 365 Outlook に接続して、メールの取得、メールの返信、予定表と連絡先の更新などを行います。Office 365 Outlook API は、以下のツールから使用できます。

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

&nbsp;

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、こちらの [Office 365 API](../app-service-logic/app-service-logic-connector-office365.md) をクリックしてください。

Office 365 Outlook では、次のことができます。

- Office 365 Outlook から取得したデータに基づいてビジネス フローを構築できます。 
- 新しいメールの着信時や新しい連絡先の作成時などに、トリガーを使用できます。
- メールへの返信、新しい予定表イベントの作成などの各種操作を使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、Salesforce に新しいオブジェクトがある場合は、そのオブジェクトを取得し、Office 365 Outlook の連絡先を更新できます。 
- PowerApps Enterprise に Office 365 Outlook API を追加します。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」をご覧ください。

ロジック アプリに操作を追加する方法については、「[SaaS サービスを接続する新しいロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション

Office 365 Outlook API では、次のトリガーとアクションを使用できます。

| トリガー | アクション|
| --- | --- |
|<ul><li>イベントが間もなく開始されるとき</li><li>新しい電子メールの着信時</li><li>新しい項目の作成時</li><li>項目の更新時</li></ul>| <ul><li>連絡先を作成する</li><li>イベントを作成する</li><li>承認の電子メールを送信する</li><li>電子メールを送信する</li><li>連絡先を削除する</li><li>電子メールを削除する</li><li>イベントを削除する</li><li>添付ファイルを取得する</li><li>予定表を取得する</li><li>連絡先を取得する</li><li>連絡先フォルダーを取得する</li><li>連絡先を取得する</li><li>電子メールを取得する</li><li>イベントを取得する</li><li>イベントを取得する</li><li>既読としてマークする</li><li>イベントが間もなく開始されるとき</li><li>新しい電子メールの着信時</li><li>新しい項目の作成時</li><li>項目の更新時</li><li>メッセージに返信する</li><li>オプションを使用して電子メールを送信する</li><li>連絡先を更新する</li><li>イベントを更新する</li></ul> |

すべての API は、JSON および XML 形式のデータに対応します。


## Office 365 への接続を作成する

この API をロジック アプリに追加する場合は、Office 365 Outlook アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

1. Office 365 Outlook アカウントにサインインします。
2. ロジック アプリが Office 365 アカウントに接続して使用することを許可します。 

接続を作成したら、受信トレイ フォルダー パスやメール メッセージなど、Office 365 Outlook のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、前述の Office 365 Outlook 接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。


### イベントが間もなく開始されるとき 
予定表イベントが間もなく開始されるときにフローをトリガーします。```GET: /Events/OnUpcomingEvents```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|query|なし|予定表の一意識別子|
|lookAheadTimeInMinutes|integer|×|query|15|間もなく開始されるイベントまでの時間 (分)。|

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


### 電子メールを取得する 
フォルダーから電子メールを取得します。```GET: /Mail```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|×|query|Inbox|メッセージを取得するフォルダーのパス (既定値: 'Inbox')|
|top|integer|×|query|10|取得する電子メールの件数 (既定値: 10)|
|fetchOnlyUnread|ブール値|×|query|true|未読メッセージのみを取得するかどうか|
|includeAttachments|ブール値|×|query|false|true に設定すると、電子メール メッセージと共に添付ファイルも取得されます。|
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


### 電子メールを送信する 
電子メール メッセージを送信します。```POST: /Mail```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|emailMessage| |○|body|なし|電子メール メッセージのインスタンス|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### 電子メールを削除する 
ID を指定して電子メール メッセージを削除します。```DELETE: /Mail/{messageId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|messageId|string|○|path|なし|削除するメッセージの ID。|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### 既読としてマークする 
電子メール メッセージを既読とマークします。```POST: /Mail/MarkAsRead/{messageId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|messageId|string|○|path|なし|既読とマークするメッセージの ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### メッセージに返信する 
電子メール メッセージに返信します。```POST: /Mail/ReplyTo/{messageId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|messageId|string|○|path|なし|返信するメッセージの ID|
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


### 添付ファイルを取得する 
ID を指定してメッセージの添付ファイルを取得します。```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|messageId|string|○|path|なし|メッセージの ID|
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


### 新しい電子メールの着信時 
新しい電子メールを着信したときにフローをトリガーします。```GET: /Mail/OnNewEmail```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|×|query|Inbox|取得する電子メール フォルダー (既定値: 受信トレイ)。|
|to|string|×|query|なし|受信者の電子メール アドレス|
|from|string|×|query|なし|差出人アドレス|
|importance|string|×|query|Normal|電子メールの重要度 (High、Normal、Low) (既定値: Normal)|
|fetchOnlyWithAttachment|ブール値|×|query|false|ファイルが添付された電子メールのみを取得します|
|includeAttachments|ブール値|×|query|false|添付ファイルを含めます|
|subjectFilter|string|×|query|なし|サブジェクト内で検索する文字列。|

#### Response

|名前|説明|
|---|---|
|200|操作が正常に完了しました。|
|202|承認済み|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### オプションを指定して電子メールを送信する 
複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます。```POST: /mailwithoptions/$subscriptions```

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


### 承認の電子メールを送信します 
承認の電子メールを送信し、To に指定した受信者からの返信を待ちます。```POST: /approvalmail/$subscriptions```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |○|body|なし|承認の電子メールのサブスクリプション要求。|


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




### 予定表を取得する 
予定表を取得します。```GET: /datasets/calendars/tables```

この呼び出しには、パラメーターはありません。

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|




### Get events 
予定表から項目を取得します。```GET: /datasets/calendars/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|取得する予定表の一意識別子|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### イベントを作成する 
新しいイベントを作成します。```POST: /datasets/calendars/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|item| |○|body|なし|作成する予定表項目|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### イベントを取得する 
予定表から特定の項目を取得します。```GET: /datasets/calendars/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|id|string|○|path|なし|取得する予定表項目の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### インベントリを削除する 
予定表項目を削除します。```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子。|
|id|string|○|path|なし|削除する予定表項目の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### イベントを更新する 
予定表項目の一部を更新します。```PATCH: /datasets/calendars/tables/{table}/items/{id}```

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


### 新しい項目の作成時 
新しい予定表項目が作成されたときにトリガーされます。```GET: /datasets/calendars/tables/{table}/onnewitems```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 項目の更新時 
予定表項目が変更されたときにトリガーされます。```GET: /datasets/calendars/tables/{table}/onupdateditems```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|予定表の一意識別子|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 連絡先フォルダーを取得する 
連絡先フォルダーを取得します。```GET: /datasets/contacts/tables```

この呼び出しには、パラメーターはありません。

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 連絡先を取得する 
連絡先フォルダーから連絡先を取得します。```GET: /datasets/contacts/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|取得する連絡先フォルダーの一意識別子|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 連絡先を作成する 
新しい連絡先を作成します。```POST: /datasets/contacts/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|連絡先フォルダーの一意識別子|
|item| |○|body|なし|作成する連絡先|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 連絡先を取得する 
連絡先フォルダーから特定の連絡先を取得します。```GET: /datasets/contacts/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|連絡先フォルダーの一意識別子|
|id|string|○|path|なし|取得する連絡先の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 連絡先を削除する 
連絡先を削除します。```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|連絡先フォルダーの一意識別子。|
|id|string|○|path|なし|削除する連絡先の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 連絡先を更新する 
連絡先の一部を更新します。```PATCH: /datasets/contacts/tables/{table}/items/{id}```

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

#### TriggerBatchResponse[IDictionary[String,Object]]

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|


#### SendMessage: 電子メール メッセージを送信します

| 名前 | データ型 |必須|
|---|---|---|
|[添付ファイル]|array|×|
|ファイル|string|×|
|Cc|string|×|
|[Bcc]|string|×|
|[件名]|string|○|
|本文|string|○|
|[重要度]|string|×|
|IsHtml|ブール値|×|
|To|string|○|

#### SendAttachment: 添付ファイル

| 名前 | データ型 |必須|
|---|---|---|
|@odata.type|string|×|
|名前|string|○|
|ContentBytes|string|○|


#### ReceiveMessage: 電子メール メッセージを受信します

| 名前 | データ型 |必須|
|---|---|---|
|ID|string|×|
|IsRead|ブール値|×|
|HasAttachment|ブール値|×|
|DateTimeReceived|string|×|
|[添付ファイル]|array|×|
|ファイル|string|×|
|Cc|string|×|
|[Bcc]|string|×|
|[件名]|string|○|
|本文|string|○|
|[重要度]|string|×|
|IsHtml|ブール値|×|
|To|string|○|


#### ReceiveAttachment: 添付ファイルを受信します

| 名前 | データ型 |必須|
|---|---|---|
|ID|string|○|
|ContentType|string|○|
|@odata.type|string|×|
|名前|string|×|
|ContentBytes|string|○|


#### DigestMessage: 電子メール メッセージを送信します

| 名前 | データ型 |必須|
|---|---|---|
|[件名]|string|○|
|本文|string|×|
|[重要度]|string|×|
|Digest|array|○|
|[添付ファイル]|array|×|
|To|string|○|

#### TriggerBatchResponse[ReceiveMessage]

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|


#### DataSetsMetadata

| 名前 | データ型 |必須|
|---|---|---|
|tabular|未定義|×|
|BLOB|未定義|×|


#### TabularDataSetsMetadata

| 名前 | データ型 |必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|


#### BlobDataSetsMetadata

| 名前 | データ型 |必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|


#### TableMetadata

| 名前 | データ型 |必須|
|---|---|---|
|name|string|×|
|title|string|×|
|x-ms-permission|string|×|
|schema|未定義|×|


#### OptionsEmailSubscription: オプションの電子メール サブスクリプションのモデル

| 名前 | データ型 |必須|
|---|---|---|
|NotificationUrl|string|×|
|メッセージ|未定義|×|

#### MessageWithOptions: ユーザーのオプションの電子メール メッセージ。ユーザー入力の一部として予測されるメッセージ。

| 名前 | データ型 |必須|
|---|---|---|
|[件名]|string|○|
|オプション|string|○|
|本文|string|×|
|[重要度]|string|×|
|[添付ファイル]|array|×|
|To|string|○|

#### SubscriptionResponse: 承認の電子メール サブスクリプションのモデル

| 名前 | データ型 |必須|
|---|---|---|
|id|string|×|
|resource|string|×|
|notificationType|string|×|
|notificationUrl|string|×|


#### ApprovalEmailSubscription: 承認の電子メール サブスクリプションのモデル

| 名前 | データ型 |必須|
|---|---|---|
|NotificationUrl|string|×|
|メッセージ|未定義|×|


#### ApprovalMessage: 電子メール メッセージを承認します。ユーザー入力の一部として予測されるメッセージ。

| 名前 | データ型 |必須|
|---|---|---|
|[件名]|string|○|
|オプション|string|○|
|本文|string|×|
|[重要度]|string|×|
|[添付ファイル]|array|×|
|To|string|○|

#### ApprovalEmailResponse: 承認の電子メールの返信

| 名前 | データ型 |必須|
|---|---|---|
|SelectedOption|string|×|

#### TablesList

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|


#### テーブル

| 名前 | データ型 |必須|
|---|---|---|
|名前|string|×|
|DisplayName|string|×|


#### 項目

| 名前 | データ型 |必須|
|---|---|---|
|ItemInternalId|string|×|


#### CalendarItemsList: 予定表項目の一覧

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|


#### CalendarItem: 予定表テーブル項目を表します

| 名前 | データ型 |必須|
|---|---|---|
|ItemInternalId|string|×|


#### ContactItemsList: 連絡先項目の一覧

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|


#### ContactItem: 連絡先テーブル項目を表します

| 名前 | データ型 |必須|
|---|---|---|
|ItemInternalId|string|×|


#### DataSetsList

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|


#### DataSet

| 名前 | データ型 | 必須|
|---|---|---|
|名前|string|×|
|DisplayName|string|×|


## 次のステップ

[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

[API リスト](apis-list.md)に戻ります。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/connectors-create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0323_2016-->