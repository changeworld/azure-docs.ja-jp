<properties
pageTitle="ProjectOnline |Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。Project Online は、プロジェクト ポートフォリオ管理 (PPM) や日常業務を行うための Microsoft の柔軟なオンライン ソリューションです。Office 365 経由で配信される Project Online により、強力なプロジェクト管理機能が直ちに実現します。ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、管理を実行できます。"
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

# ProjectOnline コネクタの使用

Project Online は、プロジェクト ポートフォリオ管理 (PPM) や日常業務を行うための Microsoft の柔軟なオンライン ソリューションです。Office 365 経由で配信される Project Online により、強力なプロジェクト管理機能が直ちに実現します。ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、管理を実行できます。

ProjectOnline コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [フロー](http://flow.microsoft.com)  

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成に関する記事](../app-service-logic/app-service-logic-create-a-logic-app.md)を参照してください。

## トリガーとアクション

ProjectOnline コネクタは、アクションとして使用できます。ProjectOnline コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 ProjectOnline コネクタでは、次のアクションやトリガーを使用できます。

### ProjectOnline のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
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

|トリガー | 説明|
|--- | ---|
|新しいプロジェクトの作成時|新しいプロジェクトが作成されるたびにフローをトリガーします|
|新しいリソースの作成時|新しいリソースが作成されたときにフローをトリガーします|
|新しいタスクの作成時|新しいタスクが作成されたときにフローをトリガーします|


## ProjectOnline への接続を作成する
ProjectOnline を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|はい|ProjectOnline の資格情報を提供します|
接続を作成したら、接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## ProjectOnline のリファレンス
適用されるバージョン: 1.0

## OnNewProject
新しいプロジェクトの作成時: 新しいプロジェクトが作成されるたびにフローをトリガーします

```GET: /trigger/_api/ProjectData/Projects```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## OnNewResource
新しいリソースの作成時: 新しいリソースが作成されたときに新しいフローをトリガーします

```GET: /trigger/_api/ProjectData/Resources```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## OnNewTask
新しいタスクの作成時: 新しいタスクが作成されたときにフローをトリガーします

```GET: /trigger/_api/ProjectData/Tasks```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## ListProjects
プロジェクトの一覧表示: プロジェクト オンライン サイトでプロジェクトを一覧表示します

```GET: /_api/ProjectServer/Projects```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## CreateProject
プロジェクトの新規作成: プロジェクト オンライン サイトで新しいプロジェクトを作成します

```POST: /_api/ProjectServer/Projects```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|proj| |○|body|なし|作成する新しいプロジェクト|

#### Response

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

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|○|path|なし|タスクを追加するプロジェクトの一意の ID|
|タスク| |○|body|なし|プロジェクトに追加する新しいタスク|

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


## CreateResource
新しいリソースの作成: プロジェクト オンライン サイトでエンタープライズ リソースを作成します

```POST: /_api/ProjectServer/EnterpriseResources```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|resource| |○|body|なし|プロジェクトに追加する新しいエンタープライズ リソース|

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


## ListTasks
タスクの一覧表示: プロジェクトで発行されたタスクを一覧表示します

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|○|path|なし|タスクを取得するプロジェクトの一意の ID|

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


## CheckoutProject
プロジェクトのチェック アウト: サイト内のプロジェクトをチェック アウトします

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|○|path|なし|タスクを追加するプロジェクトの一意の ID|

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


## PublishProject
プロジェクトのチェックインと発行: サイト内の既存のプロジェクトをチェックインおよび発行します

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|siteUrl|string|○|query|なし|プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|○|path|なし|チェックインするプロジェクトの一意の ID|

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

### TriggerProjectsWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### TriggerProject


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ProjectStartDate|string|いいえ |
|ProjectFinishDate|string|いいえ |
|ProjectCreatedDate|string|いいえ |
|ProjectId|string|いいえ |
|ProjectModifiedDate|string|いいえ |
|ProjectType|integer|いいえ |
|ProjectName|string|いいえ |



### TriggerResourcesWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### TriggerResource


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ResourceId|string|いいえ |
|ResourceBaseCalendar|string|いいえ |
|ResourceBookingType|integer|いいえ |
|ResourceCanLevel|boolean|いいえ |
|ResourceCostPerUse|number|いいえ |
|ResourceCreatedDate|string|いいえ |
|ResourceEarliestAvailableFrom|string|いいえ |
|ResourceEmail|string|いいえ |
|ResourceInitials|string|いいえ |
|ResourceIsActive|boolean|いいえ |
|ResourceIsGeneric|boolean|いいえ |
|ResourceLatestAvailableTo|string|いいえ |
|ResourceModifiedDate|string|いいえ |
|ResourceName|string|いいえ |
|ResourceStatsuName|string|いいえ |
|ResourceType|integer|いいえ |
|TypeDescription|string|いいえ |
|TypeName|string|いいえ |



### TriggerTasksWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### TriggerTask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ProjectId|string|いいえ |
|TaskId|string|いいえ |
|ProjectName|string|いいえ |
|TaskName|string|いいえ |
|TaskCreatedDate|string|いいえ |
|TaskModifieddate|string|いいえ |
|TaskStartDate|string|いいえ |
|TaskFinishDate|string|いいえ |
|TaskPriority|integer|いいえ |
|TaskIsActive|boolean|いいえ |



### NewProject


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|あり |
|説明|string|いいえ |
|開始|string|いいえ |



### NewReource


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|あり |
|IsBudget|boolean|いいえ |
|IsGeneric|boolean|いいえ |
|IsInactive|boolean|いいえ |



### Project


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ApprovedStart|string|いいえ |
|ApprovedEnd|string|いいえ |
|CheckedOutDate|string|いいえ |
|CheckOutDescription|string|いいえ |
|CheckOutId|string|いいえ |
|CreatedDate|string|いいえ |
|ID|string|いいえ |
|IsCheckedOut|boolean|いいえ |
|LastPublishedDate|string|いいえ |
|LastSavedDate|string|いいえ |
|OptimizerDecision|integer|いいえ |
|PlannerDecision|integer|いいえ |
|ProjectType|integer|いいえ |
|名前|string|いいえ |
|WinprojVersion|string|いいえ |



### ProjectsWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### NewTask


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|parameters|未定義|あり |



### TaskParameters


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|はい |
|メモ|string|いいえ |
|開始|string|いいえ |
|時間|string|いいえ |



### EnterpriseResource


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|CanLevel|boolean|いいえ |
|コード|string|いいえ |
|CostAccrual|integer|いいえ |
|CostCenter|string|いいえ |
|作成日時|string|いいえ |
|DefaultBookingType|integer|いいえ |
|電子メール|string|いいえ |
|ExternalId|string|いいえ |
|グループ|string|いいえ |
|HireDate|string|いいえ |
|ID|string|いいえ |
|Initials|string|いいえ |
|IsActive|boolean|いいえ |
|IsBudget|boolean|いいえ |
|IsCheckedOut|boolean|いいえ |
|IsGeneric|boolean|いいえ |
|IsTeam|boolean|いいえ |
|MaterialLabel|string|いいえ |
|修正済み|string|いいえ |
|名前|string|いいえ |
|Phonetics|string|いいえ |
|ResourceType|integer|いいえ |
|TerminationDate|string|いいえ |



### TasksWrapper


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### タスク


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|作成日時|string|いいえ |
|修正済み|string|いいえ |
|開始|string|いいえ |
|完了|string|いいえ |
|名前|string|いいえ |
|ID|string|いいえ |
|優先順位|integer|いいえ |
|PercentComplete|integer|いいえ |
|メモ|string|いいえ |
|連絡先|string|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->