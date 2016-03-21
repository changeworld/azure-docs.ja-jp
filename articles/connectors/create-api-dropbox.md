<properties
	pageTitle="PowerApps Enterprise またはロジック アプリに Dropbox を追加する | Microsoft Azure"
	description="Dropbox API と REST API パラメーターの概要"
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
   ms.date="03/02/2016"
   ms.author="mandia"/>

# Dropbox API の概要 
Dropbox に接続して、ファイルの作成、ファイルの取得など、ファイルを管理します。Dropbox API は次のツールから使用できます。

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

Dropbox では、次の操作を実行できます。

- Dropbox から取得したデータに基づいてビジネス フローを構築できます。 
- ファイルを作成または更新するときにトリガーを使用できます。
- ファイルの作成、ファイルの削除などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、Dropbox で新しいファイルを作成すると、Office 365 を使用してそのファイルを電子メールで送信できます。
- Dropbox API を PowerApps Enterprise に追加する | Microsoft Azure追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」をご覧ください。

ロジック アプリに操作を追加する方法については、「[SaaS サービスを接続する新しいロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」をご覧ください。

## トリガーとアクション
Dropbox には、次のトリガーとアクションがあります。

トリガー | アクション
--- | ---
<ul><li>ファイルの作成時</li><li>ファイルの変更時</li></ul> | <ul><li>ファイルを作成する</li><li>ファイルの作成時</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>アーカイブをフォルダーに抽出する</li><li>ID を使用してファイルの内容を取得する</li><li>パスを使用してファイルを取得する</li><li>ID を使用してファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>ファイルを更新する</li><li>ファイルの変更時</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## Dropbox への接続を作成する

### PowerApps に構成を追加する
Dropbox を PowerApps Enterprise に追加するときに、Dropbox アプリケーションの**アプリ キー**と**アプリ シークレット**の値を入力します。Dropbox アプリケーションでは、**リダイレクト URL** 値も使用されます。Dropbox アプリケーションがない場合は、次の手順でアプリケーションを作成できます。

1. [Dropbox][1] にサインインします。
2. Dropbox 開発者向けサイトに移動し、**[My Apps]** を選択します。![Dropbox 開発者向けサイト][8]  
3. **[Create app]** をクリックします。![Dropbox でのアプリケーションの作成][9]  
4. **[Create a new app on the Dropbox platform]** で次の操作を行います。  

	1. **[Choose API]** で、**[Dropbox API]** を選択します。
	2. **[Choose the type of access you need]** で **[Full Dropbox...]** を選択します。  
	3. アプリの名前を入力します。  

	![Dropbox のアプリケーション作成ページ 1][10]

5. アプリケーション設定ページで次の操作を行います。

	1. **[OAuth 2]** に、Azure ポータルで Dropbox API を追加したときに表示された**リダイレクト URL** 値を入力します。**[追加]** を選択します。  
	2. **[Show]** リンクをクリックして、**アプリ シークレット**を表示します。  

	![Dropbox のアプリケーション作成ページ 2][11]

**アプリ キー**値と**アプリ シークレット**値をコピーして、Azure ポータルの Dropbox 構成に貼り付けます。

### ロジック アプリに構成を追加する
ロジック アプリにこの API を追加するとき、Dropbox に接続するロジック アプリを承認する必要があります。

1. Dropbox アカウントにサインインします。
2. **[Authorize]** を選択し、ロジック アプリが Dropbox に接続して使用することを許可します。 

接続を作成したら、フォルダー パスやファイル名など、Dropbox のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ Dropbox 接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### ファイルを作成する    
Dropbox にファイルをアップロードします。```POST: /datasets/default/files```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderPath|string|○|query|なし |ファイルを Dropbox にアップロードするフォルダーのパス|
|name|string|○|query|なし |Dropbox で作成するファイルの名前|
|body|string (binary) |○|body|なし |Dropbox にアップロードするファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルの作成時    
Dropbox フォルダーに新しいファイルが作成されたときにフローをトリガーします。```GET: /datasets/default/triggers/onnewfile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|○|query|なし |Dropbox のフォルダーの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルをコピーする    
Dropbox にファイルをコピーします。```POST: /datasets/default/copyFile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし |ソース ファイルの URL|
|destination|string|○|query| なし|対象ファイル名を含む Dropbox の宛先ファイル パス|
|overwrite|ブール値|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを削除する    
Dropbox からファイルを削除します。```DELETE: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし|Dropbox から削除するファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### アーカイブをフォルダーに抽出する    
Dropbox のフォルダーにアーカイブ ファイル (例: .zip) を抽出します。**```POST: /datasets/default/extractFolderV2```**

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|source|string|○|query|なし |アーカイブ ファイルのパス|
|destination|string|○|query|なし |アーカイブの内容を抽出する Dropbox 内のパス|
|overwrite|ブール値|×|query|なし |’true’ に設定すると、宛先ファイルが上書きされます|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ID を使用してファイルの内容を取得する    
ID を使用して Dropbox からファイルの内容を取得します。```GET: /datasets/default/files/{id}/content```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Dropbox 内のファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルの内容を取得する    
パスを使用して Dropbox からファイルの内容を取得します。```GET: /datasets/default/GetFileContentByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし |Dropbox 内のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ID を使用してファイルのメタデータを取得する    
ファイル ID を使用して、Dropbox からファイルのメタデータを取得します。```GET: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path|なし |Dropbox 内のファイルの一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### パスを使用してファイルのメタデータを取得する    
パスを使用して、Dropbox からファイルのメタデータを取得します。```GET: /datasets/default/GetFileByPath```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|path|string|○|query|なし |Dropbox 内のファイルの一意のパス|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルを更新する    
Dropbox 内のファイルを更新します。```PUT: /datasets/default/files/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|id|string|○|path| なし|Dropbox 内の更新するファイルの一意識別子|
|body|string (binary) |○|body|なし |Dropbox 内の更新するファイルの内容|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### ファイルの変更時    
Dropbox フォルダー内のファイルが変更されたときにフローをトリガーします。```GET: /datasets/default/triggers/onupdatedfile```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|folderId|string|○|query|なし |Dropbox のフォルダーの一意識別子|

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
Dropbox API を PowerApps Enterprise に追加したら、この API をアプリで使用するための[アクセス許可をユーザーに付与](../power-apps/powerapps-manage-api-connection-user-access.md)します。

[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0302_2016-->