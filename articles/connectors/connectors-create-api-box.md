<properties
    pageTitle="Box コネクタをロジック アプリに追加する | Microsoft Azure"
    description="Box コネクタと REST API パラメーターの概要"
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

# Box コネクタの使用
Box に接続し、ファイルの作成、削除などを行います。Box コネクタは、次のツールから使用できます。

- Logic Apps (このトピックで説明)
- PowerApps (詳細な一覧については、[PowerApps 接続リスト](https://powerapps.microsoft.com/tutorials/connections-list/)を参照してください)

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

Box では、次の操作を実行できます。

- Box から取得したデータに基づいてビジネス フローを構築できます。 
- ファイルを作成または更新するときにトリガーを使用できます。
- ファイルのコピー、ファイルの削除などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、Box でファイルを変更するときに、Office 365 でそのファイルを取得して電子メールで送信することができます。

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
Box には、次のトリガーとアクションがあります。

| トリガー | アクション|
| --- | --- |
|<ul><li>ファイルの作成時</li><li>ファイルの変更時</li></ul> | <ul><li>ファイルを作成する</li><li>ファイルの作成時</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>アーカイブをフォルダーに抽出する</li><li>ID を使用してファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ID を使用してファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>ファイルを更新する</li><li>ファイルの変更時</li></ul>

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## Box への接続を作成する
ロジック アプリにこのコネクタを追加するとき、Box に接続するロジック アプリを承認する必要があります。

>[AZURE.INCLUDE [Box への接続を作成する手順](../../includes/connectors-create-api-box.md)]

接続を作成したら、Box のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ Box 接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### ファイルを作成する
Box にファイルをアップロードします。```POST: /datasets/default/files```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|あり|query|なし |ファイルを Box にアップロードするフォルダーのパス|
|name|string|あり|query|なし |Box で作成するファイルの名前|
|body|string (binary) |あり|body|なし |Box にアップロードするファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルの作成時
Box フォルダーに新しいファイルが作成されたときにフローをトリガーします。```GET: /datasets/default/triggers/onnewfile```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|あり|query|なし |Box のフォルダーの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルをコピーする
ファイルを Box にコピーします。```POST: /datasets/default/copyFile```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|あり|query|なし |ソース ファイルの URL|
|destination|string|あり|query| なし|対象ファイル名を含む Box の宛先ファイル パス|
|overwrite|ブール値|いいえ|query| なし|’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを削除する
Box からファイルを削除します。```DELETE: /datasets/default/files/{id}```


| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|あり|path|なし |Box から削除するファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### アーカイブをフォルダーに抽出する
Box のフォルダーにアーカイブ ファイル (例: .zip) を展開します。```POST: /datasets/default/extractFolderV2```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|あり|query| |アーカイブ ファイルのパス|
|destination|string|あり|query| |アーカイブの内容を抽出する Box 内のパス|
|overwrite|ブール値|いいえ|query| |’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ID を使用してファイルの内容を取得する
ID を使用して Box からファイルの内容を取得します。```GET: /datasets/default/files/{id}/content```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|あり|path|なし |Box 内のファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルの内容を取得する
パスを使用して Box からファイルの内容を取得します。```GET: /datasets/default/GetFileContentByPath```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|あり|query|なし |Box 内のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ID を使用してファイルのメタデータを取得する
ファイル ID を使用して Box からファイルのメタデータを取得します。```GET: /datasets/default/files/{id}```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|あり|path| なし|Box 内のファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルのメタデータを取得する
パスを使用して Box からファイルのメタデータを取得します。```GET: /datasets/default/GetFileByPath```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|あり|query|なし |Box 内のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを更新する
Box 内のファイルを更新します。```PUT: /datasets/default/files/{id}```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|あり|path| なし|Box 内の更新するファイルの一意識別子|
|body|string (binary) |あり|body|なし |Box 内の更新するファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルの変更時
Box フォルダー内のファイルが変更されたときにフローをトリガーします。```GET: /datasets/default/triggers/onupdatedfile```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|あり|query|なし |Box のフォルダーの一意識別子|

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

<!----HONumber=AcomDC_0525_2016-->