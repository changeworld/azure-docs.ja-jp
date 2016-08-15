<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。Wunderlist では、ユーザーの作業を支援する todo リストとタスク マネージャーを提供します。買い物メモを家族と共有したり、プロジェクトで作業したり、休暇の計画を立てたりするときも、Wunderlist を使えばタスクを簡単に確認、共有、完了できます。Wunderlist は携帯電話、タブレット、コンピューター間で即時に同期するため、すべてのタスクにどこからでもアクセスできます。"
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# Wunderlist コネクタの使用



Wunderlist コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [フロー](http://flows.microsoft.com)

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

Wunderlist コネクタは、アクションとして使用できます。Wunderlist コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 Wunderlist コネクタでは、次のアクションやトリガーを使用できます。

### Wunderlist のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|自分のアカウントに関連付けられたリストを取得します。|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|リストを作成します。|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|特定のリストからタスクを取得します。|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|タスクを作成します。|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|特定のリストまたは特定のタスクからサブタスクを取得します。|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|特定のタスク内にサブタスクを作成します。|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|特定のリストまたは特定のタスクのメモを取得します。|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|特定のタスクにメモを追加します。|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|特定のリストまたは特定のタスクのタスク コメントを取得します。|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|特定のタスクにコメントを追加します。|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|特定のリストまたは特定のタスクのアラームを取得します。|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|アラームを設定します。|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|特定のリストまたは特定のタスクのファイルを取得します。|
|[GetList](connectors-create-api-wunderlist.md#getlist)|特定のリストを取得します。|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|リストを削除します。|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|特定のリストを更新します。|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|特定のタスクを取得します。|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|特定のタスクを更新します。|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|特定のタスクを削除します。|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|特定のサブタスクを取得します。|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|特定のサブタスクを更新します。|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|特定のサブタスクを削除します。|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|特定のメモを取得します。|
|[UpdateNote](connectors-create-api-wunderlist.md#updatenote)|特定のメモを更新します。|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|特定のメモを削除します。|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|特定のタスク コメントを取得します。|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|特定のアラームを更新します。|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|特定のアラームを削除します。|
### Wunderlist のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|
|タスクの期限日|リスト内のタスクの期限が来たときに新しいフローをトリガーします|
|新しいタスクの作成時|新しいタスクがリストで作成されたときに新しいフローをトリガーします|
|アラームの発生時|アラームが発生したときに新しいフローをトリガーします|


## Wunderlist への接続を作成する
Wunderlist を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|はい|Wunderlist の資格情報を提供します|
接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。


>[AZURE.INCLUDE [Wunderlist への接続を作成する手順](../../includes/connectors-create-api-wunderlist.md)]


>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Wunderlist のリファレンス
適用されるバージョン: 1.0

## TriggerTaskDue
タスクの期限日: リスト内のタスクの期限が来たときに新しいフローをトリガーします

```GET: /trigger/tasksdue```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|


## TriggerTaskNew
新しいタスクの作成時: 新しいタスクがリストで作成されたときに新しいフローをトリガーします

```GET: /trigger/tasksnew```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|


## TriggerReminder
アラームの発生時: アラームが発生したときに新しいフローをトリガーします

```GET: /trigger/reminders```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|


## RetrieveLists
リストの取得: 自分のアカウントに関連付けられたリストを取得します。

```GET: /lists```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateList
リストの作成: リストを作成します。

```POST: /lists```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいリスト|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|default|操作に失敗しました。|


## ListTasks
タスクの取得: 特定のリストからタスクを取得します。

```GET: /tasks```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|完了|boolean|×|query|なし|完了|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateTask
タスクの作成: タスクを作成します

```POST: /tasks```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいタスク|

#### Response

|名前|説明|
|---|---|
|201|作成日時|


## ListSubTasks
サブタスクの取得: 特定のリストまたは特定のタスクからサブタスクを取得します。

```GET: /subtasks```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|
|完了|boolean|×|query|なし|完了|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateSubTask
サブタスクの作成: 特定のタスク内にサブタスクを作成します

```POST: /subtasks```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいサブタスク|

#### Response

|名前|説明|
|---|---|
|201|作成日時|


## ListNotes
メモの取得: 特定のリストまたは特定のタスクのメモを取得します。

```GET: /notes```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateNote
メモの作成: 特定のタスクにメモを追加します

```POST: /notes```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいメモ|

#### Response

|名前|説明|
|---|---|
|201|作成日時|


## ListComments
タスク コメントの取得: 特定のリストまたは特定のタスクのタスク コメントを取得します。

```GET: /task_comments```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateComment
タスクのコメントの追加: 特定のタスクにコメントを追加します

```POST: /task_comments```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいタスク コメント|

#### Response

|名前|説明|
|---|---|
|201|作成日時|


## RetrieveReminders
アラームの取得: 特定のリストまたは特定のタスクのアラームを取得します。

```GET: /reminders```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateReminder
アラームの設定: アラームを設定します。

```POST: /reminders```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいアラーム|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|default|操作に失敗しました。|


## RetrieveFiles
ファイルの取得: 特定のリストまたは特定のタスクのファイルを取得します。

```GET: /files```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## GetList
リストの取得: 特定のリストを取得します

```GET: /lists/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|リスト ID|

#### Response

|名前|説明|
|---|---|
|200|OK|


## DeleteList
リストの削除: リストを削除します

```DELETE: /lists/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|リスト ID|
|revision|integer|○|query|なし|リビジョン|

#### Response

|名前|説明|
|---|---|
|204|コンテンツなし|


## UpdateList
リストの更新: 特定のリストを更新します

```PATCH: /lists/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|リスト ID|
|post| |○|body|なし|リストの詳細|

#### Response

|名前|説明|
|---|---|
|200|OK|


## GetTask
タスクの取得: 特定のタスクを取得します

```GET: /tasks/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|id|integer|○|path|なし|タスク ID|

#### Response

|名前|説明|
|---|---|
|200|OK|


## UpdateTask
タスクの更新: 特定のタスクを更新します

```PATCH: /tasks/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|id|integer|○|path|なし|タスク ID|
|post| |○|body|なし|タスクの詳細|

#### Response

|名前|説明|
|---|---|
|200|OK|


## DeleteTask
タスクの削除: 特定のタスクを削除します

```DELETE: /tasks/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|id|integer|○|path|なし|タスク ID|
|revision|integer|○|query|なし|リビジョン|

#### Response

|名前|説明|
|---|---|
|204|コンテンツなし|


## GetSubTask
サブタスクの取得: 特定のサブタスクを取得します

```GET: /subtasks/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|サブタスク ID|

#### Response

|名前|説明|
|---|---|
|200|OK|


## UpdateSubTask
サブタスクの更新: 特定のサブタスクを更新します

```PATCH: /subtasks/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|サブタスク ID|
|post| |○|body|なし|サブタスクの詳細|

#### Response

|名前|説明|
|---|---|
|200|OK|


## DeleteSubTask
サブタスクの削除: 特定のサブタスクを削除します

```DELETE: /subtasks/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|サブタスク ID|
|revision|integer|○|query|なし|リビジョン|

#### Response

|名前|説明|
|---|---|
|204|コンテンツなし|


## GetNote
メモの取得: 特定のメモを取得します

```GET: /notes/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|メモ ID|

#### Response

|名前|説明|
|---|---|
|200|OK|


## UpdateNote
メモの更新: 特定のメモを更新します

```PATCH: /notes/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|メモ ID|
|post| |○|body|なし|メモの詳細|

#### Response

|名前|説明|
|---|---|
|200|OK|


## DeleteNote
メモの削除: 特定のメモを削除します

```DELETE: /notes/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|メモ ID|
|revision|integer|○|query|なし|リビジョン|

#### Response

|名前|説明|
|---|---|
|204|コンテンツなし|


## GetComment
タスク コメントの取得: 特定のタスク コメントを取得します

```GET: /task_comments/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|コメント ID|

#### Response

|名前|説明|
|---|---|
|200|OK|


## UpdateReminder
アラームの更新: 特定のアラームを更新します

```PATCH: /reminders/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|アラーム ID|
|post| |○|body|なし|アラームの詳細|

#### Response

|名前|説明|
|---|---|
|200|OK|


## DeleteReminder
アラームの削除: 特定のアラームを削除します

```DELETE: /reminders/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|アラームの ID です。|
|revision|integer|○|query|なし|リビジョン|

#### Response

|名前|説明|
|---|---|
|204|コンテンツなし|


## オブジェクト定義 

### 一覧表示


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|created\_at|string|いいえ |
|title|string|いいえ |
|list\_type|string|いいえ |
|type|string|いいえ |
|revision|integer|いいえ |



### CreatedList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|created\_at|string|いいえ |
|title|string|いいえ |
|revision|integer|いいえ |
|type|string|いいえ |



### タスク


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|assignee\_id|integer|いいえ |
|assigner\_id|integer|いいえ |
|created\_at|string|いいえ |
|created\_by\_id|integer|いいえ |
|due\_date|string|いいえ |
|list\_id|integer|いいえ |
|revision|integer|いいえ |
|starred|boolean|いいえ |
|title|string|いいえ |



### サブタスク


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|task\_id|integer|いいえ |
|created\_at|string|いいえ |
|created\_by\_id|integer|いいえ |
|revision|string|いいえ |
|title|string|いいえ |



### 注


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|task\_id|integer|いいえ |
|content|string|いいえ |
|created\_at|string|いいえ |
|updated\_at|string|いいえ |
|revision|integer|いいえ |



### コメント


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|task\_id|integer|いいえ |
|revision|integer|いいえ |
|text|string|いいえ |
|type|string|いいえ |
|created\_at|string|いいえ |



### アラーム


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|date|string|いいえ |
|task\_id|integer|いいえ |
|revision|integer|いいえ |
|type|string|いいえ |
|created\_at|string|いいえ |
|updated\_at|string|いいえ |



### CreatedReminder


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|date|string|いいえ |
|task\_id|integer|いいえ |
|revision|integer|いいえ |
|created\_at|string|いいえ |
|updated\_at|string|いいえ |



### ファイル


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|いいえ |
|url|string|いいえ |
|task\_id|integer|いいえ |
|list\_id|integer|いいえ |
|user\_id|integer|いいえ |
|file\_name|string|いいえ |
|content\_type|string|いいえ |
|file\_size|integer|いいえ |
|local\_created\_at|string|いいえ |
|created\_at|string|いいえ |
|updated\_at|string|いいえ |
|type|string|いいえ |
|revision|integer|いいえ |



### NewTask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|はい |
|title|string|はい |
|assignee\_id|integer|いいえ |
|完了|boolean|いいえ |
|recurrence\_type|string|いいえ |
|recurrence\_count|integer|いいえ |
|due\_date|string|いいえ |
|starred|boolean|いいえ |



### NewList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|title|string|あり |



### NewSubtask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|あり |
|task\_id|integer|はい |
|title|string|あり |
|完了|boolean|いいえ |



### NewNote


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|あり |
|task\_id|integer|はい |
|content|string|あり |



### NewComment


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|あり |
|task\_id|integer|あり |
|text|string|あり |



### NewReminder


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|あり |
|task\_id|integer|あり |
|date|string|はい |



### UpdateTask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|revision|integer|いいえ |
|title|string|いいえ |
|assignee\_id|integer|いいえ |
|完了|boolean|いいえ |
|recurrence\_type|string|いいえ |
|recurrence\_count|integer|いいえ |
|due\_date|string|いいえ |
|starred|boolean|いいえ |



### UpdateList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|revision|integer|いいえ |
|title|string|いいえ |



### UpdateSubtask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|revision|integer|いいえ |
|title|string|いいえ |
|完了|boolean|いいえ |



### UpdateNote


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|revision|integer|いいえ |
|content|string|いいえ |



### UpdateReminder


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|date|string|いいえ |
|revision|integer|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->