<properties
pageTitle="PowerApps Enterprise とロジック アプリに Salesforce API を追加する | Microsoft Azure"
description="Salesforce API と REST API パラメーターの概要"
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

# Salesforce API の概要
Salesforce に接続し、オブジェクトの作成、オブジェクトの取得などを行います。

Salesforce API は PowerApps Enterprise とロジック アプリから使用できます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、こちらの [Salesforce API](../app-service-logic/app-service-logic-connector-salesforce.md) をクリックしてください。

Salesforce では、次の操作を実行できます。

- Salesforce から取得したデータに基づいてビジネス フローを構築できます。 
- オブジェクトを作成または更新するときにトリガーを使用できます。
- Azure Blob の作成、オブジェクトの削除などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、Salesforce で新しいオブジェクトを作成するときに、Office 365 を使用して電子メールを送信できます。
- PowerApps Enterprise に Salesforce API を追加できます。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」を参照してください。

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
Salesforce API には、次のトリガーとアクションがあります。

| トリガー | アクション|
| --- | --- |
|<ul><li>オブジェクトの作成時</li><li>オブジェクトの変更時</li></ul> | <ul><li>オブジェクトを作成する</li><li>オブジェクトを取得する</li><li>オブジェクトの作成時に</li><li>オブジェクトの変更時</li><li>オブジェクトを削除する</li><li>オブジェクトを取得する</li><li>オブジェクトの種類 (SObjects) を取得する</li><li>オブジェクトを更新する</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## Salesforce への接続を作成する 

### PowerApps に構成を追加する
Salesforce を PowerApps Enterprise に追加するときに、Salesforce アプリケーションの**アプリ キー**と**アプリ シークレット**の値を入力します。Salesforce アプリケーションには、**リダイレクト URL** 値も使用されます。Salesforce アプリケーションがない場合は、次の手順でアプリケーションを作成できます。

1. [Salesforce の開発者向けホームページにサインイン][5]し、プロファイルを選択して、**[Setup]** を選択します。![Salesforce のホーム ページ][6]

3. **[Create]** を選択し、**[Apps]** を選択します。**[Apps]** ページで、**[Connected Apps]** の **[New]** を選択します。![Salesforce がアプリを作成する][7]

4. **[New Connected App]** で次のようにします。

	1. **[Connected App Name]** の値を入力します。  
	2. **[API Name]** の値を入力します。  
	3. **[Contact Email]** の値を入力します。  
	4. _[API (Enable OAuth Settings)]_ で **[Enable OAuth Settings]** を選択し、**[Callback URL]** を Azure ポータルに新しい Salesforce API を追加したときに表示された値に設定します。  

5. _[Selected OAuth scopes]_ で、次のスコープを **[Selected OAuth Scopes]** に追加します。

	- 自分の Chatter データにアクセスして管理する (chatter\_api)
	- 自分のデータにアクセスして管理する (api)
	- 自分の一意の識別子へのアクセスを許可する (openid)
	- いつでも自分に代わって要求を実行する (refresh\_token、offline\_access)

6. 変更を**保存**します。 ![Salesforce の新しいアプリ][8]

**アプリ キー**値と**アプリ シークレット**値をコピーして、Azure ポータルの Salesforce 構成に貼り付けます。

### ロジック アプリに構成を追加する
ロジック アプリにこの API を追加するとき、Salesforce に接続するロジック アプリを承認する必要があります。

1. Salesforce アカウントにサインインします。
2. ロジック アプリが Salesforce アカウントに接続して使用することを許可します。 

接続を作成したら、テーブル名など、Salesforce のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**を参照してください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ接続を使用できます。

## Swagger REST API リファレンス
#### このドキュメントの対象バージョン: 1.0


### オブジェクトを作成する
Salesforce オブジェクトを作成します。```POST: /datasets/default/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|Salesforce SObject の種類 (例: 'Lead')|
|item| |○|body|なし|作成する Salesforce オブジェクト|

### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### オブジェクトを取得する
Salesforce オブジェクトを取得します。```GET: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|Salesforce SObject の種類 (例: 'Lead')|
|id|string|○|path|なし|取得する Salesforce オブジェクトの一意識別子|

### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### オブジェクトを削除する
Salesforce オブジェクトを削除します。```DELETE: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|Salesforce SObject の種類 (例: 'Lead')|
|id|string|○|path|なし|削除する Salesforce オブジェクトの一意識別子|

### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### オブジェクトを更新する
Salesforce オブジェクトを更新します。```PATCH: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|Salesforce SObject の種類 (例: 'Lead')|
|id|string|○|path|なし|更新する Salesforce オブジェクトの一意識別子|
|item| |○|body|なし|変更されたプロパティがある Salesforce オブジェクト|

### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### オブジェクトの作成時
Salesforce にオブジェクトが作成されたときにフローをトリガーします。```GET: /datasets/default/tables/{table}/onnewitems```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|Salesforce SObject の種類 (例: 'Lead')|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|

### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### オブジェクトの変更時 
Salesforce のオブジェクトが変更されたときにフローをトリガーします。```GET: /datasets/default/tables/{table}/onupdateditems```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|Salesforce SObject の種類 (例: 'Lead')|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|

### Response
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
|source|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|


#### BlobDataSetsMetadata

| 名前 | データ型 | 必須|
|---|---|---|
|source|string|×|
|displayName|string|×|
|urlEncoding|string|×|


#### TableMetadata

| 名前 | データ型 | 必須|
|---|---|---|
|name|string|×|
|title|string|×|
|x-ms-permission|string|×|
|schema|未定義|×|


#### DataSetsList

| 名前 | データ型 | 必須|
|---|---|---|
|値|array|×|


#### DataSet

| 名前 | データ型 | 必須|
|---|---|---|
|名前|string|
|DisplayName|string|×|


#### テーブル

| 名前 | データ型 | 必須|
|---|---|---|
|名前|string|×|
|DisplayName|string|×|


#### 項目

| 名前 | データ型 | 必須|
|---|---|---|
|ItemInternalId|string|×|


#### ItemsList

| 名前 | データ型 | 必須|
|---|---|---|
|値|array|×|


#### TablesList

| 名前 | データ型 | 必須|
|---|---|---|
|値|array|×|


## 次のステップ
Salesforce API を PowerApps Enterprise に追加したら、この API をアプリで利用する[許可をユーザーに与えます](../power-apps/powerapps-manage-api-connection-user-access.md)。

[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。


[5]: https://developer.salesforce.com
[6]: ./media/create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/create-api-salesforce/salesforce-create-app.png
[8]: ./media/create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0224_2016-->