<properties
	pageTitle="Logic Apps への Office 365 Users API の追加 | Microsoft Azure"
	description="Office 365 Users API と REST API パラメーターの概要"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Office 365 Users API を使ってみる

Office 365 のユーザー接続プロバイダーを使用すると、Office 365 アカウントを使用して、組織のユーザー プロファイルにアクセスできます。自分のプロファイル、ユーザーのプロファイル、ユーザーの上司または直接の部下の取得、ユーザー プロファイルの更新など、さまざまな操作を実行できます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、「[Office 365 API](../app-service-logic/app-service-logic-connector-office365.md)」をクリックしてください。


Office 365 ユーザーは、次のことを行えます。

* ロジック アプリの構築
* パワー アプリの構築

PowerApps Enterprise での API の追加方法について詳しくは、「[Register an API in PowerApps (PowerApps での API の登録)](../power-apps/powerapps-register-from-available-apis.md)」をご覧ください。

ロジック アプリでの操作の追加については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」をご覧ください。

## トリガーとアクション

Office 365 Users API はアクションとして使用できます。トリガーはありません。すべての API は、JSON および XML 形式のデータに対応します。

 Office 365 Users API では、次のアクションやトリガーを使用できます。

### Office 365 Users アクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|MyProfile|現在のユーザーのプロファイルを取得します|
|UserProfile|特定のユーザー プロファイルを取得します|
|Manager|指定されたユーザーの上司のユーザー プロファイルを取得します|
|DirectReports|直接の部下を取得します|
|SearchUser|ユーザー プロファイルの検索結果を取得します|
## Office 365 Users への接続を作成します
Office 365 Users API を使用するには、最初に**接続**を作成し、以下のプロパティの詳細を指定します。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|あり|Office 365 の資格情報を指定します|


>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Office 365 Users REST API リファレンス
#### このドキュメントはバージョン 1.0 を対象としています。


### 自分のプロファイルを取得 


 現在のユーザーのプロファイルを取得します ```GET: /users/me```

この呼び出しには、パラメーターはありません。
#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### ユーザー プロファイルの取得 


 特定のユーザー プロファイルを取得します ```GET: /users/{userId}```



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userId|string|○|path|なし|ユーザー プリンシパル名または電子メール ID|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### 上司の取得 


 指定されたユーザーの上司のユーザー プロファイルを取得します ```GET: /users/{userId}/manager```



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userId|string|○|path|なし|ユーザー プリンシパル名または電子メール ID|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### 直接の部下を取得 


 直接の部下を取得します ```GET: /users/{userId}/directReports```



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userId|string|○|path|なし|ユーザー プリンシパル名または電子メール ID|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### ユーザーの検索 


 ユーザー プロファイルの検索結果を取得します ```GET: /users```



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|searchTerm|string|×|query|なし|検索文字列 (以下に適用されます: 表示名、姓を含まない名前、姓、電子メール、メールのニックネーム、ユーザー プリンシパル名)|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



## オブジェクト定義: 

 **ユーザー**: ユーザー モデル クラス。

ユーザーの必須プロパティ:

ID

**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|DisplayName|string|
|GivenName|string|
|Surname|string|
|Mail|string|
|MailNickname|string|
|TelephoneNumber|string|
|AccountEnabled|ブール値|
|ID|string|
|UserPrincipalName|string|
|学科|string|
|JobTitle|string|


## 次のステップ
Office 365 API を PowerApps Enterprise に追加したら、この API をアプリで利用する[許可をユーザーに与えます](../power-apps/powerapps-manage-api-connection-user-access.md)。

[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

<!---HONumber=AcomDC_0224_2016-->