<properties
pageTitle="ProjectOnline |Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。Project Online は、プロジェクト ポートフォリオ管理 (PPM) や日常業務を行うための Microsoft の柔軟なオンライン ソリューションです。Office 365 経由で配信される Project Online により、強力なプロジェクト管理機能が直ちに実現します。ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、管理を実行できます。"
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

# ProjectOnline コネクタの使用

Project Online は、プロジェクト ポートフォリオ管理 (PPM) や日常業務を行うための Microsoft の柔軟なオンライン ソリューションです。Office 365 経由で配信される Project Online により、強力なプロジェクト管理機能が直ちに実現します。ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、管理を実行できます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

ProjectOnline コネクタは、アクションとして使用できます。ProjectOnline コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 ProjectOnline コネクタでは、次のアクションやトリガーを使用できます。

### ProjectOnline のアクション
実行できるアクションは以下のとおりです。

|アクション|Description|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|プロジェクト オンライン サイトでプロジェクトを一覧表示します|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|プロジェクト オンライン サイトで新しいプロジェクトを作成します|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|プロジェクトで新しいタスクを作成します|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|プロジェクト オンライン サイトでエンタープライズ リソースを作成します|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|プロジェクトで発行されたタスクを一覧表示します|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|サイト内のプロジェクトをチェック アウトします|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|サイト内の既存のプロジェクトをチェックインおよび発行します|
### ProjectOnline のトリガー
次のイベントをリッスンできます。

|トリガー | Description|
|--- | ---|
|新しいプロジェクトの作成時|新しいプロジェクトが作成されるたびにフローをトリガーします|
|新しいリソースの作成時|新しいリソースが作成されたときにフローをトリガーします|
|新しいタスクの作成時|新しいタスクが作成されたときにフローをトリガーします|


## ProjectOnline への接続を作成する
ProjectOnline を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|Description|
| ---|---|---|
|トークン|はい|ProjectOnline の資格情報を提供します|

>[AZURE.INCLUDE [ProjectOnline への接続を作成する手順](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## ProjectOnline のリファレンス
適用されるバージョン: 1.0

## OnNewProject
新しいプロジェクトの作成時: 新しいプロジェクトが作成されるたびにフローをトリガーします

```GET: /trigger/_api/ProjectData/Projects```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## OnNewResource
新しいリソースの作成時: 新しいリソースが作成されたときに新しいフローをトリガーします

```GET: /trigger/_api/ProjectData/Resources```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## OnNewTask
新しいタスクの作成時: 新しいタスクが作成されたときにフローをトリガーします

```GET: /trigger/_api/ProjectData/Tasks```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## ListProjects
プロジェクトの一覧表示: プロジェクト オンライン サイトでプロジェクトを一覧表示します

```GET: /_api/ProjectServer/Projects```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## CreateProject
プロジェクトの新規作成: プロジェクト オンライン サイトで新しいプロジェクトを作成します

```POST: /_api/ProjectServer/Projects```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|proj| |○|body|なし|作成する新しいプロジェクト|

#### 応答

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|ForbIDden|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## CreateTask
新しいタスクの作成: プロジェクトで新しいタスクを作成します

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|○|path|なし|タスクを追加するプロジェクトの一意の ID|
|タスク| |○|body|なし|プロジェクトに追加する新しいタスク|

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


## CreateResource
新しいリソースの作成: プロジェクト オンライン サイトでエンタープライズ リソースを作成します

```POST: /_api/ProjectServer/EnterpriseResources```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|resource| |○|body|なし|プロジェクトに追加する新しいエンタープライズ リソース|

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


## ListTasks
タスクの一覧表示: プロジェクトで発行されたタスクを一覧表示します

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|○|path|なし|タスクを取得するプロジェクトの一意の ID|

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


## CheckoutProject
プロジェクトのチェック アウト: サイト内のプロジェクトをチェック アウトします

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|○|path|なし|タスクを追加するプロジェクトの一意の ID|

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


## PublishProject
プロジェクトのチェックインと発行: サイト内の既存のプロジェクトをチェックインおよび発行します

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|○|path|なし|チェックインするプロジェクトの一意の ID|

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

### TriggerProjectsWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|なし |



### TriggerProject


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ProjectStartDate|string|なし |
|ProjectFinishDate|string|なし |
|ProjectCreatedDate|string|なし |
|ProjectId|string|なし |
|ProjectModifiedDate|string|なし |
|ProjectType|integer|なし |
|ProjectName|string|なし |



### TriggerResourcesWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|なし |



### TriggerResource


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ResourceId|string|なし |
|ResourceBaseCalendar|string|なし |
|ResourceBookingType|integer|なし |
|ResourceCanLevel|boolean|なし |
|ResourceCostPerUse|number|なし |
|ResourceCreatedDate|string|なし |
|ResourceEarliestAvailableFrom|string|なし |
|ResourceEmail|string|なし |
|ResourceInitials|string|なし |
|ResourceIsActive|boolean|なし |
|ResourceIsGeneric|boolean|なし |
|ResourceLatestAvailableTo|string|なし |
|ResourceModifiedDate|string|なし |
|ResourceName|string|なし |
|ResourceStatsuName|string|なし |
|ResourceType|integer|なし |
|TypeDescription|string|なし |
|TypeName|string|なし |



### TriggerTasksWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|なし |



### TriggerTask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ProjectId|string|なし |
|TaskId|string|なし |
|ProjectName|string|なし |
|TaskName|string|なし |
|TaskCreatedDate|string|なし |
|TaskModifieddate|string|なし |
|TaskStartDate|string|なし |
|TaskFinishDate|string|なし |
|TaskPriority|integer|なし |
|TaskIsActive|boolean|なし |



### NewProject


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|はい |
|Description|string|なし |
|開始|string|なし |



### NewReource


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|はい |
|IsBudget|boolean|なし |
|IsGeneric|boolean|なし |
|IsInactive|boolean|なし |



### Project


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ApprovedStart|string|なし |
|ApprovedEnd|string|なし |
|CheckedOutDate|string|なし |
|CheckOutDescription|string|なし |
|CheckOutId|string|なし |
|CreatedDate|string|なし |
|ID|string|なし |
|IsCheckedOut|boolean|なし |
|LastPublishedDate|string|なし |
|LastSavedDate|string|なし |
|OptimizerDecision|integer|なし |
|PlannerDecision|integer|なし |
|ProjectType|integer|なし |
|名前|string|なし |
|WinprojVersion|string|なし |



### ProjectsWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|なし |



### NewTask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|parameters|未定義|はい |



### TaskParameters


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|はい |
|メモ|string|なし |
|開始|string|なし |
|時間|string|なし |



### EnterpriseResource


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|CanLevel|boolean|なし |
|コード|string|なし |
|CostAccrual|integer|なし |
|CostCenter|string|なし |
|作成日時|string|なし |
|DefaultBookingType|integer|なし |
|電子メール|string|なし |
|ExternalId|string|なし |
|グループ|string|なし |
|HireDate|string|なし |
|ID|string|なし |
|Initials|string|なし |
|IsActive|boolean|なし |
|IsBudget|boolean|なし |
|IsCheckedOut|boolean|なし |
|IsGeneric|boolean|なし |
|IsTeam|boolean|なし |
|MaterialLabel|string|なし |
|修正済み|string|なし |
|名前|string|なし |
|Phonetics|string|なし |
|ResourceType|integer|なし |
|TerminationDate|string|なし |



### TasksWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|なし |



### タスク


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|作成日時|string|なし |
|修正済み|string|なし |
|開始|string|なし |
|完了|string|なし |
|Name|string|なし |
|ID|string|なし |
|優先順位|integer|なし |
|PercentComplete|integer|なし |
|メモ|string|なし |
|連絡先|string|なし |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->