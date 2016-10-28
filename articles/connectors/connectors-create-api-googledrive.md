<properties
    pageTitle="ロジック アプリに Google Drive コネクタを追加する | Microsoft Azure"
    description="Google Drive コネクタと REST API パラメーターの概要"
    services=""
    suite=""
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
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Google Drive コネクタの使用
Google Drive に接続して、ファイルの作成、行の取得などを行います。Google Drive では、次の操作を実行できます。

- 検索から取得したデータに基づいてビジネス フローを構築することができます。
- 画像、ニュースなどを検索するアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、動画を検索し、Twitter を使用して Twitter フィードに動画を投稿することができます。

ロジック アプリに操作を追加する方法については、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関するページを参照してください。


## トリガーとアクション
Google Drive には、次のアクションがあります。トリガーはありません。

トリガー | アクション
--- | ---
なし | <ul><li>ファイルを作成する</li><li>行を挿入する</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>行を削除する</li><li>アーカイブをフォルダーに抽出する</li><li>ID を使用してファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ID を使用してファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>行を取得する</li><li>ファイルを更新する</li><li>行を更新する</li></ul>

すべてのコネクタは、JSON および XML 形式のデータに対応します。


## Google Drive への接続を作成する

ロジック アプリにこのコネクタを追加するとき、Google Drive に接続するロジック アプリを承認する必要があります。

>[AZURE.INCLUDE [Google Drive への接続を作成する手順](../../includes/connectors-create-api-googledrive.md)]

接続を作成したら、フォルダー パスやファイル名など、Google Drive のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ Google Drive 接続を使用できます。


## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### ファイルを作成する    
Google Drive にファイルをアップロードします。```POST: /datasets/default/files```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|folderPath|string|○|query|なし |ファイルを Google Drive にアップロードするフォルダーのパス|
|name|string|○|query|なし |Google Drive に作成するファイルの名前|
|body|string (binary) |○|body| なし|Google Drive にアップロードするファイルの内容|

#### 応答
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を挿入する    
Google シートに行を挿入します。```POST: /datasets/{dataset}/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|dataset|string|○|path| なし|Google シートファイルの一意識別子|
|テーブル|string|○|path|なし |ワークシートの一意識別子|
|item|ItemInternalId: string |○|body|なし |指定されたシートに挿入する行|

#### 応答
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルをコピーする    
Google Drive のファイルをコピーします。```POST: /datasets/default/copyFile```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|source|string|○|query| なし|ソース ファイルの URL|
|destination|string|○|query|なし |対象ファイル名を含む Google Drive の宛先ファイル パス|
|overwrite|boolean|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### 応答
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを削除する    
Google Drive からファイルを削除します。```DELETE: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Google Drive から削除するファイルの一意識別子|

#### 応答
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を削除する    
Google シートから行を削除します。```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし |Google シートファイルの一意識別子|
|テーブル|string|○|path|なし |ワークシートの一意識別子|
|id|string|○|path|なし |削除する行の一意識別子|

#### 応答
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### アーカイブをフォルダーに抽出する    
Google Drive のフォルダーにアーカイブ ファイル (例: .zip) を抽出します。```POST: /datasets/default/extractFolderV2```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|source|string|○|query|なし |アーカイブ ファイルのパス|
|destination|string|○|query|なし |アーカイブの内容を抽出する Google Drive のパス|
|overwrite|boolean|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### 応答
|Name|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ID を使用してファイルの内容を取得する    
ID を使用して Google Drive からファイルの内容を取得します。```GET: /datasets/default/files/{id}/content```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Google Drive 内の取得するファイルの一意識別子|

#### 応答
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルの内容を取得する    
パスを使用して Google Drive からファイルの内容を取得します。```GET: /datasets/default/GetFileContentByPath```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|path|string|○|query|なし |Google Drive 内のファイルのパス|

#### 応答
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ID を使用してファイルのメタデータを取得する    
ID を使用して Google Drive からファイルのメタデータを取得します。```GET: /datasets/default/files/{id}```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Google Drive 内のファイルの一意識別子|

#### 応答
|Name|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルのメタデータを取得する    
パスを使用して Google Drive からファイルのメタデータを取得します。```GET: /datasets/default/GetFileByPath```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|path|string|○|query|なし |Google Drive 内のファイルのパス|

#### 応答
|Name|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を取得する    
Google シートから 1 行を取得します。```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし |Google シートファイルの一意識別子|
|テーブル|string|○|path|なし |ワークシートの一意識別子|
|id|string|○|path| なし|取得する行の一意識別子|

#### 応答
|Name|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを更新する    
Google Drive 内のファイルを更新します。```PUT: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Google Drive 内の更新するファイルの一意識別子|
|body|string (binary) |○|body| なし|Google Drive にアップロードするファイルの内容|

#### 応答
|Name|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を更新する    
Google シート内の行を更新します。```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし |Google シートファイルの一意識別子|
|テーブル|string|○|path| なし|ワークシートの一意識別子|
|id|string|○|path|なし |更新する行の一意識別子|
|item|ItemInternalId: string |○|body|なし |更新された値のある行|

#### 応答
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
|IsFolder|boolean|×|
|ETag|string|×|
|FileLocator|string|×|

#### TableMetadata

|プロパティ名 | データ型 |必須|
|---|---|---|
|name|string|×|
|title|string|×|
|x-ms-permission|string|×|
|schema|未定義|×|

#### TablesList

|プロパティ名 | データ型 |必須|
|---|---|---|
|値|array|×|

#### テーブル

|プロパティ名 | データ型 |必須|
|---|---|---|
|名前|string|×|
|DisplayName|string|×|

#### 項目

|プロパティ名 | データ型 |必須|
|---|---|---|
|ItemInternalId|string|×|

#### ItemsList

|プロパティ名 | データ型 |必須|
|---|---|---|
|値|array|×|


## 次のステップ

[ロジック アプリを作成する](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API リスト](apis-list.md)に戻ります。


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0824_2016-->