<properties
pageTitle="OneDrive for Business | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。OneDrive for Business に接続してファイルを管理します。ファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。"
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# OneDrive for Business コネクタの使用



OneDrive for Business コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [フロー](http://flows.microsoft.com)  

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

OneDrive for Business コネクタは、アクションとして使用できます。OneDrive for Business コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 OneDrive for Business コネクタでは、次のアクションやトリガーを使用できます。

### OneDrive for Business のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata)|ID を使用して、OneDrive for Business のファイルのメタデータを取得します|
|[UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile)|OneDrive for Business のファイルを更新します|
|[DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile)|OneDrive for Business からファイルを削除します|
|[GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath)|パスを使用して、OneDrive for Business のファイルのメタデータを取得します|
|[GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath)|パスを使用して、OneDrive for Business のファイルの内容を取得します|
|[GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent)|ID を使用して、OneDrive for Business のファイルの内容を取得します|
|[CreateFile](connectors-create-api-onedriveforbusiness.md#createfile)|OneDrive for Business にファイルをアップロードします|
|[CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile)|OneDrive for Business にファイルをコピーします|
|[ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder)|OneDrive for Business フォルダーのファイルを一覧表示します|
|[ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder)|OneDrive for Business ルート フォルダーのファイルを一覧表示します|
|[ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2)|OneDrive for Business にフォルダーを抽出します|
### OneDrive for Business のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|
|ファイルの作成時|OneDrive for Business フォルダーに新しいファイルが作成されたときにフローをトリガーします|
|ファイルの変更時|OneDrive for Business フォルダー内のファイルが変更されたときにフローをトリガーします|


## OneDrive for Business への接続の作成
OneDrive for Business を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|あり|OneDrive for Business の資格情報を提供します|
接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

>[AZURE.INCLUDE [OneDrive for Business への接続を作成する手順](../../includes/connectors-create-api-onedriveforbusiness.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## OneDrive for Business のリファレンス
適用されるバージョン: 1.0

## GetFileMetadata
ID を使用してファイルのメタデータを取得: ID を使用して、OneDrive for Business のファイルのメタデータを取得します

```GET: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|ファイルを指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## UpdateFile
ファイルの更新: OneDrive for Business のファイルを更新します

```PUT: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|更新するファイルを指定します|
|body| |○|body|なし|OneDrive for Business 内の更新するファイルの内容|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## DeleteFile
ファイルの削除: OneDrive for Business からファイルを削除します

```DELETE: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|削除するファイルを指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## GetFileMetadataByPath
パスを使用してファイルのメタデータを取得: パスを使用して、OneDrive for Business のファイルのメタデータを取得します

```GET: /datasets/default/GetFileByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし|OneDrive for Business 内のファイルへの一意のパス|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## GetFileContentByPath
パスを使用してファイルの内容を取得: パスを使用して、OneDrive for Business のファイルの内容を取得します

```GET: /datasets/default/GetFileContentByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし|OneDrive for Business 内のファイルへの一意のパス|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## GetFileContent
ID を使用してファイルの内容を取得: ID を使用して、OneDrive for Business のファイルの内容を取得します

```GET: /datasets/default/files/{id}/content```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|ファイルを指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CreateFile
ファイルの作成: OneDrive for Business にファイルをアップロードします

```POST: /datasets/default/files```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|○|query|なし|ファイルを OneDrive for Business にアップロードするフォルダーのパス|
|name|string|○|query|なし|OneDrive for Business で作成するファイルの名前|
|body| |○|body|なし|OneDrive for Business にアップロードするファイルの内容|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## CopyFile
ファイルのコピー: OneDrive for Business にファイルをコピーします

```POST: /datasets/default/copyFile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし|ソース ファイルの URL|
|destination|string|○|query|なし|対象ファイル名を含む OneDrive for Business の宛先ファイル パス|
|overwrite|ブール値|×|query|false|’true’ に設定すると、宛先ファイルが上書きされます|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## OnNewFile
ファイルの作成時: OneDrive for Business フォルダーに新しいファイルが作成されたときにフローをトリガーします

```GET: /datasets/default/triggers/onnewfile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|○|query|なし|フォルダーを指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## OnUpdatedFile
ファイルの変更時: OneDrive for Business フォルダー内のファイルが変更されたときにフローをトリガーします

```GET: /datasets/default/triggers/onupdatedfile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|○|query|なし|フォルダーを指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ListFolder
フォルダー内のファイルの一覧表示: OneDrive for Business フォルダーのファイルを一覧表示します

```GET: /datasets/default/folders/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|フォルダーを指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ListRootFolder
ルート フォルダーの一覧表示: OneDrive for Business ルート フォルダーのファイルを一覧表示します

```GET: /datasets/default/folders```

この呼び出しには、パラメーターはありません
#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## ExtractFolderV2
フォルダーの抽出: OneDrive for Business にフォルダーを抽出します

```POST: /datasets/default/extractFolderV2```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし|アーカイブ ファイルのパス|
|destination|string|○|query|なし|アーカイブの内容を抽出する OneDrive for Business のパス|
|overwrite|ブール値|×|query|false|’true’ に設定すると、宛先ファイルが上書きされます|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## オブジェクト定義 

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



### BlobMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ID|string|いいえ |
|名前|string|いいえ |
|DisplayName|string|いいえ |
|パス|string|いいえ |
|LastModified|string|いいえ |
|サイズ|integer|いいえ |
|MediaType|string|いいえ |
|IsFolder|boolean|いいえ |
|ETag|string|いいえ |
|FileLocator|string|いいえ |



### オブジェクト


| プロパティ名 | データ型 | 必須 |
|---|---|---|


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->