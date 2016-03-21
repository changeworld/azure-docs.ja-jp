<properties
pageTitle="PowerApps Enterprise とロジック アプリに OneDrive API を追加する | Microsoft Azure"
description="OneDrive API と REST API パラメーターの概要"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="03/03/2016"
ms.author="mandia"/>

# OneDrive API の概要

OneDrive に接続して、ファイルのアップロード、取得、削除など、ファイルを管理します。OneDrive API は次のツールから使用できます。

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

&nbsp;

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、こちらの [OneDrive API](../app-service-logic/app-service-logic-connector-onedrive.md) をクリックしてください。

OneDrive では、次の操作を実行できます。

- OneDrive から取得したデータに基づいてビジネス フローを構築できます。 
- ファイルを作成または更新するときにトリガーを使用できます。
- ファイルの作成、ファイルの削除などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、OneDrive で新しいファイルを作成すると、Office 365 を使用してそのファイルを電子メールで送信できます。
- PowerApps Enterprise に OneDrive API を追加できます。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」をご覧ください。

ロジック アプリに操作を追加する方法については、「[SaaS サービスを接続する新しいロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
OneDrive API には、次のトリガーとアクションがあります。

| トリガー | アクション|
| --- | --- |
|<ul><li>ファイルの作成時</li><li>ファイルの変更時</li></ul> | <ul><li>ファイルを作成する</li><li>フォルダー内のファイルを一覧表示する</li><li>ファイルの作成時</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>フォルダーを抽出する</li><li>ID を使用してファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ID を使用してファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>ルート フォルダーを表示する</li><li>ファイルを更新する</li><li>ファイルの変更時</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## OneDrive への接続を作成する

ロジック アプリにこの API を追加するとき、OneDrive に接続するロジック アプリを承認する必要があります。

1. OneDrive アカウントにサインインします。
2. ロジック アプリが OneDrive に接続して使用することを許可します。 

接続を作成したら、フォルダー パスやファイル名など、OneDrive のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。


### ID を使用してファイルのメタデータを取得する
ID を使用して、OneDrive のファイルのメタデータを取得します。```GET: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|OneDrive 内のファイルの一意識別子|

### 応答
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを更新する
OneDrive のファイルを更新します。```PUT: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|OneDrive 内の更新するファイルの一意識別子|
|body| |○|body|なし|OneDrive 内の更新するファイルの内容|


### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### ファイルを削除する
OneDrive からファイルを削除します。```DELETE: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|OneDrive から削除するファイルの一意識別子|


### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルのメタデータを取得する
パスを使用して、OneDrive のファイルのメタデータを取得します。```GET: /datasets/default/GetFileByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし|OneDrive 内のファイルの一意のパス|


### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|




### パスを使用してファイルの内容を取得する
パスを使用して、OneDrive のファイルの内容を取得します。```GET: /datasets/default/GetFileContentByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし|OneDrive 内のファイルの一意のパス|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|




### ID を使用してファイルの内容を取得する
ID を使用して、OneDrive のファイルの内容を取得します。```GET: /datasets/default/files/{id}/content```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|OneDrive 内のファイルの一意識別子|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|




### ファイルを作成する
OneDrive にファイルをアップロードします。```POST: /datasets/default/files```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|○|query|なし|ファイルを OneDrive にアップロードするフォルダーのパス|
|name|string|○|query|なし|OneDrive で作成するファイルの名前|
|body| |○|body|なし|OneDrive にアップロードするファイルの内容|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### ファイルをコピーする
OneDrive にファイルをコピーします。```POST: /datasets/default/copyFile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし|ソース ファイルの URL|
|destination|string|○|query|なし|対象ファイル名を含む OneDrive の宛先ファイル パス|
|overwrite|ブール値|×|query|false|’true’ に設定すると、宛先ファイルが上書きされます|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### ファイルの作成時
OneDrive フォルダーに新しいファイルが作成されたときにフローをトリガーします。```GET: /datasets/default/triggers/onnewfile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|○|query|なし|OneDrive のフォルダーの一意識別子|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### OneDrive フォルダー内のファイルが変更されたときにフローをトリガーする
OneDrive フォルダー内のファイルが変更されたときにフローをトリガーします。```GET: /datasets/default/triggers/onupdatedfile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|○|query|なし|OneDrive のフォルダーの一意識別子|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### フォルダーを抽出する
OneDrive にフォルダーを抽出します。```POST: /datasets/default/extractFolderV2```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし|アーカイブ ファイルのパス|
|destination|string|○|query|なし|アーカイブの内容を抽出する OneDrive のパス|
|overwrite|ブール値|×|query|false|’true’ に設定すると、宛先ファイルが上書きされます|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



## オブジェクト定義

#### DataSetsMetadata

|プロパティ名 | データ型 | 必須|
|---|---|---|
|tabular|未定義|×|
|BLOB|未定義|×|


#### TabularDataSetsMetadata

|プロパティ名 | データ型 |必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|


#### BlobDataSetsMetadata

|プロパティ名 | データ型 |必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|



#### BlobMetadata

|プロパティ名 | データ型 |必須|
|---|---|---|
|ID|string|×|
|名前|string|×|
|DisplayName|string|×|
|パス|string|×|
|LastModified|string|×|
|サイズ|integer|×|
|MediaType|string|×|
|IsFolder|ブール値|×|
|ETag|string|×|
|FileLocator|string|×|


## 次のステップ

[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

[API リスト](apis-list.md)に戻ります。

[5]: https://account.live.com/developers/applications/create
[6]: ./media/create-api-onedrive/onedrive-new-app.png
[7]: ./media/create-api-onedrive/onedrive-app-api-settings.png

<!---HONumber=AcomDC_0309_2016-->