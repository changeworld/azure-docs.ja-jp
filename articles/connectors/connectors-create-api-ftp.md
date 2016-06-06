<properties
    pageTitle="ロジック アプリに FTP コネクタを追加する | Microsoft Azure"
    description="FTP コネクタと REST API パラメーターの概要"
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

# FTP コネクタの使用
FTP サーバーに接続して、ファイルのアップロードやファイルの削除など、ファイルを管理します。FTP コネクタは、次のツールから使用できます。

- Logic Apps (このトピックで説明)
- PowerApps (詳細な一覧については、[PowerApps 接続リスト](https://powerapps.microsoft.com/tutorials/connections-list/)を参照してください)

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

FTP では、次の操作を実行できます。

- FTP から取得したデータに基づいてビジネス フローを構築できます。 
- ファイルが更新されたときに、トリガーを使用できます。
- ファイルの作成、ファイルの内容の取得などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、ファイルの内容を取得し、SQL Database をアップロードすることができます。 

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。


## トリガーとアクション
FTP では、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
<ul><li>更新されたファイルを取得する</li></ul> | <ul><li>ファイルを作成する</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>フォルダーを抽出する</li><li>ファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>更新されたファイルを取得する</li><li>ファイルを取得する</li></ul>

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## FTP への接続を作成する


>[AZURE.INCLUDE [FTP 接続を作成する手順](../../includes/connectors-create-api-ftp.md)]

接続を作成したら、ソース ファイルや宛先フォルダーなど、FTP のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ FTP 接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### ファイルを作成する
FTP サーバーにファイルをアップロードします。```POST: /datasets/default/files```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|○|query|なし |ファイルを FTP サーバーにアップロードするフォルダーのパス|
|name|string|○|query| なし|FTP サーバーで作成するファイルの名前|
|body| |○|body|なし |FTP サーバーにアップロードするファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### ファイルをコピーする
FTP サーバーにファイルをコピーします。```POST: /datasets/default/copyFile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし |ソース ファイルの URL|
|destination|string|○|query|なし |対象ファイル名を含む FTP サーバーの宛先ファイル パス|
|overwrite|ブール値|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### ファイルを削除する 
FTP サーバーからファイルを削除します。```DELETE: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |FTP サーバーから削除するファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### フォルダーを抽出する
FTP サーバーのフォルダーにアーカイブ ファイル (例: .zip) を展開します。```POST: /datasets/default/extractFolderV2```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query| なし|アーカイブ ファイルのパス|
|destination|string|○|query| なし|宛先フォルダーのパス|
|overwrite|ブール値|×|query|なし|’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### ファイルの内容を取得する
ID を使用して FTP サーバーからファイルの内容を取得します。```GET: /datasets/default/files/{id}/content```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |ファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルの内容を取得する
パスを使用して FTP サーバーからファイルの内容を取得します。```GET: /datasets/default/GetFileContentByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし |FTP サーバー内のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルのメタデータを取得する 
ファイル ID を使用して FTP サーバーからファイルのメタデータを取得します。```GET: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|ファイルの一意識別子|

#### Response
| 名前 | 説明 |
| --- | --- |
| 200 | OK | 
| default | 操作に失敗しました。


### パスを使用してファイルのメタデータを取得する
パスを使用して FTP サーバーからファイルのメタデータを取得します。```GET: /datasets/default/GetFileByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query| なし|FTP サーバー内のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 更新されたファイルを取得する
更新されたファイルを取得します。```GET: /datasets/default/triggers/onupdatedfile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|○|query|なし |更新されたファイルを検索するフォルダー ID|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを更新する 
FTP サーバーのファイルを更新します。```PUT: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path| なし|FTP サーバー内の更新するファイルの一意識別子|
|body| |○|body|なし |FTP サーバー内の更新するファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## オブジェクト定義

#### DataSetsMetadata

| 名前 | データ型 | 必須 |
|---|---|---|
|tabular|未定義|×|
|BLOB|未定義|×|

#### TabularDataSetsMetadata

| 名前 | データ型 | 必須 |
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|

#### BlobDataSetsMetadata

| 名前 | データ型 | 必須 |
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|

#### BlobMetadata

| 名前 | データ型 | 必須 |
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