<properties
    pageTitle="ロジック アプリに Azure Blob Storage コネクタを追加する | Microsoft Azure"
    description="Azure Blob Storage コネクタと REST API パラメーターの概要"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/18/2016"
   ms.author="mandia"/>

# Azure Blob Storage コネクタの概要
Azure Blob に接続して、ファイルの作成、ファイルの削除など、BLOB コンテナーのファイルを管理します。Azure Blob Storage コネクタは、次のツールから使用できます。

- Logic Apps 

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

Azure Blob Storage では、次の操作を実行できます。

- BLOB から取得したデータに基づいてビジネス フローを構築することができます。 
- ファイルの作成、ファイル内容の取得などを行うアクションを使用することができます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、BLOB のファイルに含まれる "緊急" を検索し、Office 365 を使用して電子メールに "緊急" と書いてすべてのファイルを送信することができます。 

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
Azure Blob には、次のアクションがあります。トリガーはありません。

| トリガー | アクション|
| --- | --- |
| ありません。 | <ul><li>ファイルを作成する</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>アーカイブをフォルダーに抽出する</li><li>ファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>ファイルを更新する</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## Azure Blob への接続を作成する

>[AZURE.INCLUDE [Azure Blob Storage への接続を作成する手順](../../includes/connectors-create-api-azureblobstorage.md)]

接続を作成したら、フォルダー パスやファイル名など、BLOB のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ BLOB 接続を使用できます。
 

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### ファイルを作成する
ファイルを Azure Blob Storage にアップロードします。```POST: /datasets/default/files```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|○|query| なし |ファイルを Azure Blob Storage にアップロードするフォルダーのパス|
|name|string|○|query|なし |Azure Blob Storage に作成するファイルの名前|
|body|string (binary) |○|body|なし|Azure Blob Storage にアップロードするファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### ファイルをコピーする
ファイルを Azure Blob Storage にコピーします。```POST: /datasets/default/copyFile```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし |ソース ファイルの URL|
|destination|string|○|query| なし|対象のファイル名を含む、Azure Blob Storage の宛先ファイルのパス|
|overwrite|ブール値|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを削除する
Azure Blob Storage からファイルを削除します。```DELETE: /datasets/default/files/{id}```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Azure Blob Storage から削除するファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### アーカイブをフォルダーに抽出する
Azure Blob Storage のフォルダーにアーカイブ ファイル (例: .zip) を展開します。```POST: /datasets/default/ExtractFolderV2```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query| なし|アーカイブ ファイルのパス|
|destination|string|○|query|なし |アーカイブの内容を抽出する Azure Blob Storage のパス|
|overwrite|ブール値|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルの内容を取得する
ID を使用して Azure Blob Storage からファイルの内容を取得します。```GET: /datasets/default/files/{id}/content```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|ファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルの内容を取得する
パスを使用して Azure Blob Storage からファイルの内容を取得します。```GET: /datasets/default/GetFileContentByPath```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし |Azure Blob Storage のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルのメタデータを取得する
ファイル ID を使用して、Azure Blob Storage からファイルのメタデータを取得します。```GET: /datasets/default/files/{id}```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |ファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルのメタデータを取得する
パスを使用して、Azure Blob Storage からファイルのメタデータを取得します。```GET: /datasets/default/GetFileByPath```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし|Azure Blob Storage のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを更新する
Azure Blob Storage のファイルを更新します。```PUT: /datasets/default/files/{id}```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Azure Blob Storage で更新するファイルの一意識別子|
|body|string (binary) |○|body|なし |Azure Blob Storage のアップロードするファイルの内容|

#### Response
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

<!---HONumber=AcomDC_0525_2016-->