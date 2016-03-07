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
ms.date="02/22/2016"
ms.author="deonhe"/>

# OneDrive API の概要

OneDrive に接続してファイルを管理します。OneDrive のファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。

OneDrive API は PowerApps Enterprise とロジック アプリから使用できます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、こちらの [OneDrive API](../app-service-logic/app-service-logic-connector-onedrive.md) をクリックしてください。

OneDrive では、次の操作を実行できます。

- OneDrive から取得したデータに基づいてビジネス フローを構築できます。 
- ファイルを作成または更新するときにトリガーを使用できます。
- ファイルの作成、ファイルの削除などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、OneDrive で新しいファイルを作成すると、Office 365 を使用してそのファイルを電子メールで送信できます。
- PowerApps Enterprise に OneDrive API を追加できます。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」を参照してください。

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
OneDrive API には、次のトリガーとアクションがあります。

| トリガー | アクション|
| --- | --- |
|<ul><li>ファイルの作成時</li><li>ファイルの変更時</li></ul> | <ul><li>ファイルを作成する</li><li>フォルダー内のファイルを一覧表示する</li><li>ファイルの作成時</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>フォルダーを抽出する</li><li>ID を使用してファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ID を使用してファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>ルート フォルダーを一覧表示する</li><li>ファイルを更新する</li><li>ファイルの変更時</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## OneDrive への接続を作成する

### PowerApps に構成を追加する
OneDrive を PowerApps Enterprise に追加するときに、OneDrive アプリケーションの**アプリ キー**と**アプリ シークレット**の値を入力します。OneDrive アプリケーションには、**リダイレクト URL** 値も使用されます。OneDrive アプリケーションがない場合は、次の手順でアプリケーションを作成できます。

1. _Microsoft アカウント デベロッパー センター_の[アプリケーション作成ページ][5]に移動し、_Microsoft アカウント_でサインインします。

2. **アプリケーション名**を入力し、契約に同意します。![新しい OneDrive アプリケーション][6]

3. 次のように設定します。

	1. **[API 設定]** を選択します。  
	2. **[リダイレクト URL]** を Azure ポータルで新しい OneDrive API を追加するときに表示される値に設定します。  
	3. 変更を**保存**します。  

	![OneDrive アプリケーションの API 設定][7]

**アプリ キー**値と**アプリ シークレット**値をコピーして、Azure ポータルの OneDrive 構成に貼り付けます。

### ロジック アプリに構成を追加する
ロジック アプリにこの API を追加するとき、OneDrive に接続するロジック アプリを承認する必要があります。

1. OneDrive アカウントにサインインします。
2. ロジック アプリが OneDrive に接続して使用することを許可します。 

接続を作成したら、フォルダー パスやファイル名など、OneDrive のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**を参照してください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ接続を使用できます。

## Swagger REST API リファレンス
#### このドキュメントの対象バージョン: 1.0


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
OneDrive でファイルを更新します。```PUT: /datasets/default/files/{id}```

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
|source|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|


#### BlobDataSetsMetadata

|プロパティ名 | データ型 |必須|
|---|---|---|
|source|string|×|
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
OneDrive API を PowerApps Enterprise に追加したら、この API をアプリで利用する[許可をユーザーに与えます](../power-apps/powerapps-manage-api-connection-user-access.md)。

[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。


[5]: https://account.live.com/developers/applications/create
[6]: ./media/create-api-onedrive/onedrive-new-app.png
[7]: ./media/create-api-onedrive/onedrive-app-api-settings.png

<!---HONumber=AcomDC_0224_2016-->