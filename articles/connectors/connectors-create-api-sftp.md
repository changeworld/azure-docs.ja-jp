<properties
	pageTitle="ロジック アプリに SFTP API を追加する | Microsoft Azure"
	description="SFTP API と REST API パラメーターの概要"
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
   ms.date="02/25/2016"
   ms.author="mandia"/>

# SFTP API の概要
SFTP サーバーに接続して、ファイルを管理します。SFTP サーバーでは、ファイルのアップロード、ファイルの削除など、さまざまなタスクを実行できます。SFTP API は次のツールから使用できます。

- Logic Apps

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、「[SFTP コネクタの使用開始とロジック アプリへの追加](../app-service-logic/app-service-logic-connector-sftp.md)」をご覧ください。

SFTP では、次の操作を実行できます。

- SFTP から取得したデータに基づいてビジネス フローを構築できます。 
- ファイルが更新されたときに、トリガーを使用できます。
- ファイルの作成、ファイルの削除などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、ファイルの内容を取得し、SQL Database をアップロードすることができます。 

ロジック アプリに操作を追加する方法については、「[SaaS サービスを接続する新しいロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」をご覧ください。


## トリガーとアクション
SFTP では、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
<ul><li>ファイルの作成時または変更時</li></ul> | <ul><li>ファイルを作成する</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>フォルダーを抽出する</li><li>ファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>ファイルを更新する</li><li>ファイルの作成時または変更時</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。


## SFTP への接続を作成する
この API をロジック アプリに追加するときに、次の値を入力します。

|プロパティ| 必須|説明|
| ---|---|---|
|ホスト サーバー アドレス| あり | 完全修飾ドメイン (FQDN) または SFTP サーバーの IP アドレスを入力します。|
|ユーザー名| あり | ユーザー名を入力して、SFTP サーバーに接続します。|
|パスワード | あり | ユーザー名のパスワードを入力します。|
|SSH サーバー ホスト キーのフィンガー プリント | あり | SSH サーバーのパブリック ホスト キーのフィンガープリントを入力します。<br/><br/>通常、このキーはサーバー管理者から入手できます。```WinSCP``` または ```ssh-keygen-g3 -F``` ツールを使用して、キーのフィンガープリントを取得することもできます。 | 

接続を作成したら、フォルダー パスやファイルなど、SFTP のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ SFTP 接続を使用できます。


## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### ファイルを作成する
SFTP のファイルをアップロードします。```POST: /datasets/default/files```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|○|query|なし |SFTP のフォルダーの一意のパス|
|name|string|○|query| なし|ファイルの名前|
|body|string (binary) |○|body|なし |SFTP に作成するファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### ファイルをコピーする
ファイルを SFTP にコピーします。```POST: /datasets/default/copyFile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query| なし|ソース ファイルのパス|
|destination|string|○|query|なし |ファイル名を含む、宛先ファイルのパス|
|overwrite|ブール値|×|query|なし|’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### ファイルを削除する 
SFTP のファイルを削除します。```DELETE: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |SFTP 内のファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### フォルダーを抽出する
SFTP を使用して、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。```POST: /datasets/default/extractFolderV2```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし |アーカイブ ファイルのパス|
|destination|string|○|query|なし |宛先フォルダーのパス|
|overwrite|ブール値|×|query|なし|’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### ファイルの内容を取得する
ID を使用して SFTP からファイルの内容を取得します。```GET: /datasets/default/files/{id}/content```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |SFTP 内のファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルの内容を取得する
パスを使用して SFTP からファイルの内容を取得します。```GET: /datasets/default/GetFileContentByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query| なし|SFTP 内のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルのメタデータを取得する 
ファイル ID を使用して、SFTP からファイルのメタデータを取得します。```GET: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path| なし|SFTP 内のファイルの一意識別子|

#### Response
| 名前 | 説明 |
| --- | --- |
| 200 | OK | 
| default | 操作に失敗しました。


### パスを使用してファイルのメタデータを取得する
パスを使用して、SFTP からファイルのメタデータを取得します。```GET: /datasets/default/GetFileByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし |SFTP 内のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを更新する
SFTP を使用してファイルの内容を更新します。```PUT: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |SFTP 内のファイルの一意識別子|
|body|string (binary) |○|body| なし|SFTP 内の更新するファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルの作成時または変更時 
SFTP のファイルが変更されたときにフローをトリガーします。```GET: /datasets/default/triggers/onupdatedfile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|○|query|なし |フォルダーの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## オブジェクト定義

#### DataSetsMetadata

| 名前 | データ型 | 必須|
|---|---|---|
|tabular|未定義|×|
|BLOB|未定義|×|

#### TabularDataSetsMetadata

| 名前 | データ型 | 必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|

#### BlobDataSetsMetadata

| 名前 | データ型 | 必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|

#### BlobMetadata

| 名前 | データ型 | 必須|
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

<!---HONumber=AcomDC_0323_2016-->