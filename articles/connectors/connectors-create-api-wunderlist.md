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
ms.date="08/18/2016"
ms.author="deonhe"/>

# Wunderlist コネクタの使用

Wunderlist では、ユーザーの作業を支援する todo リストとタスク マネージャーを提供します。買い物メモを家族と共有したり、プロジェクトで作業したり、休暇の計画を立てたりするときも、Wunderlist を使えばタスクを簡単に確認、共有、完了できます。Wunderlist は携帯電話、タブレット、コンピューター間で即時に同期するため、すべてのタスクにどこからでもアクセスできます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

Wunderlist コネクタは、アクションとして使用できます。Wunderlist コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 Wunderlist コネクタでは、次のアクションやトリガーを使用できます。

### Wunderlist のアクション
実行できるアクションは以下のとおりです。

|アクション|Description|
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

|トリガー | Description|
|--- | ---|
|タスクの期限日|リスト内のタスクの期限が来たときに新しいフローをトリガーします|
|新しいタスクの作成時|新しいタスクがリストで作成されたときに新しいフローをトリガーします|
|アラームの発生時|アラームが発生したときに新しいフローをトリガーします|


## Wunderlist への接続を作成する
Wunderlist を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|Description|
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

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|

#### 応答

|名前|説明|
|---|---|
|200|操作に成功しました|


## TriggerTaskNew
新しいタスクの作成時: 新しいタスクがリストで作成されたときに新しいフローをトリガーします

```GET: /trigger/tasksnew```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|

#### 応答

|名前|説明|
|---|---|
|200|操作に成功しました|


## TriggerReminder
アラームの発生時: アラームが発生したときに新しいフローをトリガーします

```GET: /trigger/reminders```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### 応答

|名前|説明|
|---|---|
|200|操作に成功しました|


## RetrieveLists
リストの取得: 自分のアカウントに関連付けられたリストを取得します。

```GET: /lists```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|

#### 応答

|Name|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateList
リストの作成: リストを作成します。

```POST: /lists```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいリスト|

#### 応答

|名前|説明|
|---|---|
|200|操作に成功しました|
|default|操作に失敗しました。|


## ListTasks
タスクの取得: 特定のリストからタスクを取得します。

```GET: /tasks```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|完了|boolean|×|query|なし|完了|

#### 応答

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateTask
タスクの作成: タスクを作成します

```POST: /tasks```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいタスク|

#### 応答

|名前|Description|
|---|---|
|201|作成日時|


## ListSubTasks
サブタスクの取得: 特定のリストまたは特定のタスクからサブタスクを取得します。

```GET: /subtasks```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|
|完了|boolean|×|query|なし|完了|

#### 応答

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateSubTask
サブタスクの作成: 特定のタスク内にサブタスクを作成します

```POST: /subtasks```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいサブタスク|

#### 応答

|名前|Description|
|---|---|
|201|作成日時|


## ListNotes
メモの取得: 特定のリストまたは特定のタスクのメモを取得します。

```GET: /notes```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### 応答

|Name|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateNote
メモの作成: 特定のタスクにメモを追加します

```POST: /notes```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいメモ|

#### 応答

|名前|Description|
|---|---|
|201|作成日時|


## ListComments
タスク コメントの取得: 特定のリストまたは特定のタスクのタスク コメントを取得します。

```GET: /task_comments```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### 応答

|Name|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateComment
タスクのコメントの追加: 特定のタスクにコメントを追加します

```POST: /task_comments```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいタスク コメント|

#### 応答

|名前|Description|
|---|---|
|201|作成日時|


## RetrieveReminders
アラームの取得: 特定のリストまたは特定のタスクのアラームを取得します。

```GET: /reminders```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### 応答

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateReminder
アラームの設定: アラームを設定します。

```POST: /reminders```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|post| |○|body|なし|作成する新しいアラーム|

#### 応答

|Name|説明|
|---|---|
|200|操作に成功しました|
|default|操作に失敗しました。|


## RetrieveFiles
ファイルの取得: 特定のリストまたは特定のタスクのファイルを取得します。

```GET: /files```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|task\_id|integer|×|query|なし|タスク ID|

#### 応答

|Name|説明|
|---|---|
|200|操作に成功しました|
|400|正しくない要求|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## GetList
リストの取得: 特定のリストを取得します

```GET: /lists/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|string|○|path|なし|リスト ID|

#### 応答

|名前|説明|
|---|---|
|200|OK|


## DeleteList
リストの削除: リストを削除します

```DELETE: /lists/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|リスト ID|
|revision|integer|○|query|なし|リビジョン|

#### 応答

|Name|Description|
|---|---|
|204|コンテンツなし|


## UpdateList
リストの更新: 特定のリストを更新します

```PATCH: /lists/{id}```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|リスト ID|
|post| |○|body|なし|リストの詳細|

#### 応答

|名前|説明|
|---|---|
|200|OK|


## GetTask
タスクの取得: 特定のタスクを取得します

```GET: /tasks/{id}```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|id|integer|○|path|なし|タスク ID|

#### 応答

|名前|説明|
|---|---|
|200|OK|


## UpdateTask
タスクの更新: 特定のタスクを更新します

```PATCH: /tasks/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|id|integer|○|path|なし|タスク ID|
|post| |○|body|なし|タスクの詳細|

#### 応答

|名前|説明|
|---|---|
|200|OK|


## DeleteTask
タスクの削除: 特定のタスクを削除します

```DELETE: /tasks/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|integer|○|query|なし|リスト ID|
|id|integer|○|path|なし|タスク ID|
|revision|integer|○|query|なし|リビジョン|

#### 応答

|名前|Description|
|---|---|
|204|コンテンツなし|


## GetSubTask
サブタスクの取得: 特定のサブタスクを取得します

```GET: /subtasks/{id}```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|string|○|path|なし|サブタスク ID|

#### 応答

|名前|説明|
|---|---|
|200|OK|


## UpdateSubTask
サブタスクの更新: 特定のサブタスクを更新します

```PATCH: /subtasks/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|サブタスク ID|
|post| |○|body|なし|サブタスクの詳細|

#### 応答

|名前|説明|
|---|---|
|200|OK|


## DeleteSubTask
サブタスクの削除: 特定のサブタスクを削除します

```DELETE: /subtasks/{id}```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|サブタスク ID|
|revision|integer|○|query|なし|リビジョン|

#### 応答

|名前|Description|
|---|---|
|204|コンテンツなし|


## GetNote
メモの取得: 特定のメモを取得します

```GET: /notes/{id}```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|string|○|path|なし|メモ ID|

#### 応答

|名前|説明|
|---|---|
|200|OK|


## UpdateNote
メモの更新: 特定のメモを更新します

```PATCH: /notes/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|メモ ID|
|post| |○|body|なし|メモの詳細|

#### 応答

|Name|説明|
|---|---|
|200|OK|


## DeleteNote
メモの削除: 特定のメモを削除します

```DELETE: /notes/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|メモ ID|
|revision|integer|○|query|なし|リビジョン|

#### 応答

|Name|Description|
|---|---|
|204|コンテンツなし|


## GetComment
タスク コメントの取得: 特定のタスク コメントを取得します

```GET: /task_comments/{id}```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|string|○|path|なし|コメント ID|

#### 応答

|名前|説明|
|---|---|
|200|OK|


## UpdateReminder
アラームの更新: 特定のアラームを更新します

```PATCH: /reminders/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|アラーム ID|
|post| |○|body|なし|アラームの詳細|

#### 応答

|Name|説明|
|---|---|
|200|OK|


## DeleteReminder
アラームの削除: 特定のアラームを削除します

```DELETE: /reminders/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|integer|○|path|なし|アラームの ID です。|
|revision|integer|○|query|なし|リビジョン|

#### 応答

|名前|Description|
|---|---|
|204|コンテンツなし|


## オブジェクト定義 

### 一覧表示


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|created\_at|string|なし |
|title|string|なし |
|list\_type|string|なし |
|type|string|なし |
|revision|integer|なし |



### CreatedList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|created\_at|string|なし |
|title|string|なし |
|revision|integer|なし |
|type|string|なし |



### タスク


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|assignee\_id|integer|なし |
|assigner\_id|integer|なし |
|created\_at|string|なし |
|created\_by\_id|integer|なし |
|due\_date|string|なし |
|list\_id|integer|なし |
|revision|integer|なし |
|starred|boolean|なし |
|title|string|なし |



### サブタスク


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|task\_id|integer|なし |
|created\_at|string|なし |
|created\_by\_id|integer|なし |
|revision|string|なし |
|title|string|なし |



### 注


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|task\_id|integer|なし |
|content|string|なし |
|created\_at|string|なし |
|updated\_at|string|なし |
|revision|integer|なし |



### コメント


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|task\_id|integer|なし |
|revision|integer|なし |
|text|string|なし |
|type|string|なし |
|created\_at|string|なし |



### アラーム


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|date|string|なし |
|task\_id|integer|なし |
|revision|integer|なし |
|type|string|なし |
|created\_at|string|なし |
|updated\_at|string|なし |



### CreatedReminder


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|date|string|なし |
|task\_id|integer|なし |
|revision|integer|なし |
|created\_at|string|なし |
|updated\_at|string|なし |



### ファイル


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|integer|なし |
|url|string|なし |
|task\_id|integer|なし |
|list\_id|integer|なし |
|user\_id|integer|なし |
|file\_name|string|なし |
|content\_type|string|なし |
|file\_size|integer|なし |
|local\_created\_at|string|なし |
|created\_at|string|なし |
|updated\_at|string|なし |
|type|string|なし |
|revision|integer|なし |



### NewTask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|はい |
|title|string|はい |
|assignee\_id|integer|なし |
|完了|boolean|なし |
|recurrence\_type|string|なし |
|recurrence\_count|integer|なし |
|due\_date|string|なし |
|starred|boolean|なし |



### NewList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|title|string|はい |



### NewSubtask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|はい |
|task\_id|integer|はい |
|title|string|はい |
|完了|boolean|なし |



### NewNote


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|はい |
|task\_id|integer|はい |
|content|string|はい |



### NewComment


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|はい |
|task\_id|integer|はい |
|text|string|はい |



### NewReminder


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|integer|はい |
|task\_id|integer|はい |
|date|string|はい |



### UpdateTask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|revision|integer|なし |
|title|string|なし |
|assignee\_id|integer|なし |
|完了|boolean|なし |
|recurrence\_type|string|なし |
|recurrence\_count|integer|なし |
|due\_date|string|なし |
|starred|boolean|なし |



### UpdateList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|revision|integer|なし |
|title|string|なし |



### UpdateSubtask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|revision|integer|なし |
|title|string|なし |
|完了|boolean|なし |



### UpdateNote


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|revision|integer|なし |
|content|string|なし |



### UpdateReminder


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|date|string|なし |
|revision|integer|なし |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->