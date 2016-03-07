<properties
	pageTitle="PowerApps またはロジック アプリに Google Drive API を追加する | Microsoft Azure"
	description="Google Drive API と REST API パラメーターの概要"
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
   ms.date="02/11/2016"
   ms.author="mandia"/>

# Google Drive API の概要
Google Drive に接続して、ファイルの作成、行の取得などを行います。

Google Drive API は、PowerApps とロジック アプリから使用できます。

Google Drive では、次の操作を実行できます。

- 検索から取得したデータに基づいてビジネス フローを構築することができます。 
- 画像、ニュースなどを検索するアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、動画を検索し、Twitter を使用して Twitter フィードに動画を投稿することができます。
- PowerApps Enterprise に Google Drive API を追加できます。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」を参照してください。

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。


## トリガーとアクション
Google Drive には、次のアクションがあります。トリガーはありません。

トリガー | アクション
--- | ---
なし | <ul><li>ファイルを作成する</li><li>行を挿入する</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>行を削除する</li><li>アーカイブをフォルダーに抽出する</li><li>ID を使用してファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ID を使用してファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>行を取得する</li><li>ファイルを更新する</li><li>行を更新する</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。


## Google Drive への接続を作成する

### PowerApps に構成を追加する
Google Drive を PowerApps Enterprise に追加するときに、Google Drive アプリケーションの**アプリ キー**と**アプリ シークレット**の値を入力します。Google アプリケーションには、**リダイレクト URL** 値も使用されます。Google Drive アプリケーションがない場合は、次の手順でアプリケーションを作成できます。

1. [Google Developers Console][5] にサインインし、**[Create an empty project]** を選択します。![Google Developers Console][6]

2. アプリケーションのプロパティを入力し、**[Create]** を選択します。
3. **[Use Google APIs]** を選択します。![Google API を使用する][8]  
4. 概要で **[Drive API]** を選択します。![Google Drive API の概要][9]  
5. **[Enable API]** を選択します。 ![Google Drive API を有効にする][10]  
6. Drive API を有効にするときは、**[Credentials]** および **[OAuth 2.0 Client ID]** を選択します。 ![資格情報を追加する][12]  
7. **[Configure consent screen]** を選択します。
8. **[OAuth consent screen]** の **[Product Name]** を入力し、**[Save]** を選択します。![Configure consent screen][13]  
9. [Create Client ID] ページで次のようにします。  

	1. **[Application type]** で **[Web application]** を選択します。
	2. クライアントの名前を入力します。
	3. Azure ポータルで Google Drive API を追加したときに表示されるリダイレクト URL 値を入力します。
	4. **[作成]** を選択します。  

	![クライアント ID を作成する][14]

11. 登録したアプリケーションのクライアント ID とクライアント シークレットが表示されます。

**アプリ キー**値と**アプリ シークレット**値をコピーして、Azure ポータルの Google Drive API 構成に貼り付けます。


### ロジック アプリに構成を追加する
ロジック アプリにこの API を追加するとき、Google Drive に接続するロジック アプリを承認する必要があります。

1. Google Drive アカウントにサインインします。
2. **[Authorize]** を選択して、ロジック アプリが Google Drive に接続して使用することを許可します。 

接続を作成したら、フォルダー パスやファイル名など、Google Drive のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**を参照してください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ Google Drive 接続を使用できます。


## Swagger REST API リファレンス

### ファイルを作成する    
Google Drive にファイルをアップロードします。```POST: /datasets/default/files```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|○|query|なし |ファイルを Google Drive にアップロードするフォルダーのパス|
|name|string|○|query|なし |Google Drive に作成するファイルの名前|
|body|string (binary) |○|body| なし|Google Drive にアップロードするファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を挿入する    
Google シートに行を挿入します。```POST: /datasets/{dataset}/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path| なし|Google シートファイルの一意識別子|
|テーブル|string|○|path|なし |ワークシートの一意識別子|
|item|ItemInternalId: string |○|body|なし |指定されたシートに挿入する行|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルをコピーする    
Google Drive のファイルをコピーします。```POST: /datasets/default/copyFile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query| なし|ソース ファイルの URL|
|destination|string|○|query|なし |対象ファイル名を含む Google Drive の宛先ファイル パス|
|overwrite|ブール値|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを削除する    
Google Drive からファイルを削除します。```DELETE: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Google Drive から削除するファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を削除する    
Google シートから行を削除します。```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし |Google シートファイルの一意識別子|
|テーブル|string|○|path|なし |ワークシートの一意識別子|
|id|string|○|path|なし |削除する行の一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### アーカイブをフォルダーに抽出する    
Google Drive のフォルダーにアーカイブ ファイル (例: .zip) を抽出します。```POST: /datasets/default/extractFolderV2```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし |アーカイブ ファイルのパス|
|destination|string|○|query|なし |アーカイブの内容を抽出する Google Drive のパス|
|overwrite|ブール値|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ID を使用してファイルの内容を取得する    
ID を使用して Google Drive からファイルの内容を取得します。```GET: /datasets/default/files/{id}/content```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Google Drive 内の取得するファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルの内容を取得する    
パスを使用して Google Drive からファイルの内容を取得します。```GET: /datasets/default/GetFileContentByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし |Google Drive 内のファイルのパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ID を使用してファイルのメタデータを取得する    
ID を使用して Google Drive からファイルのメタデータを取得します。```GET: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Google Drive 内のファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルのメタデータを取得する    
パスを使用して Google Drive からファイルのメタデータを取得します。```GET: /datasets/default/GetFileByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし |Google Drive 内のファイルのパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を取得する    
Google シートから 1 行を取得します。```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし |Google シートファイルの一意識別子|
|テーブル|string|○|path|なし |ワークシートの一意識別子|
|id|string|○|path| なし|取得する行の一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを更新する    
Google Drive 内のファイルを更新します。```PUT: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Google Drive 内の更新するファイルの一意識別子|
|body|string (binary) |○|body| なし|Google Drive にアップロードするファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を更新する    
Google シート内の行を更新します。```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし |Google シートファイルの一意識別子|
|テーブル|string|○|path| なし|ワークシートの一意識別子|
|id|string|○|path|なし |更新する行の一意識別子|
|item|ItemInternalId: string |○|body|なし |更新された値がある行|

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
Google Drive を PowerApps Enterprise に追加したら、この API をアプリで利用する[許可をユーザーに与えます](../power-apps/powerapps-manage-api-connection-user-access.md)。

[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/create-api-googledrive/google-developers-console.png
[8]: ./media/create-api-googledrive/use-google-apis.png
[9]: ./media/create-api-googledrive/googledrive-api-overview.png
[10]: ./media/create-api-googledrive/enable-googledrive-api.png
[12]: ./media/create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/create-api-googledrive/configure-consent-screen.png
[14]: ./media/create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0224_2016-->