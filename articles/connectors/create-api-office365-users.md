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
ms.date="02/25/2016"
ms.author="deonhe"/>

# Office 365 Users API を使ってみる

Office 365 ユーザーに接続して、プロファイルの取得、ユーザーの検索などを行います。Office 365 Users API は、以下のツールから使用できます。

- PowerApps 
- ロジック アプリ 

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、こちらの [Office 365 API](../app-service-logic/app-service-logic-connector-office365.md) をクリックしてください。


Office 365 ユーザーは、次のことを行えます。

- Office 365 ユーザーから取得したデータに基づいてビジネス フローを構築できます。 
- 直属の部下の取得、上司のユーザーのプロファイルの取得などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、ユーザーの直属の部下を取得し、この情報を利用して、SQL Azure Database を更新します。 
- PowerApps Enterprise に Office 365 Users API を追加します。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」を参照してください。

ロジック アプリに操作を追加する方法については、「[SaaS サービスを接続する新しいロジック アプリを作成します。](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション

Office 365 Users API では、次のアクションを使用できます。トリガーはありません。

| トリガー | アクション|
| --- | --- |
|なし | <ul><li>上司の取得</li><li>自分のプロファイルを取得</li><li>直属の部下を取得</li><li>ユーザー プロファイルの取得</li><li>ユーザーの検索</li></ul>|

すべての API は、JSON および XML 形式のデータに対応します。


## Office 365 Users への接続を作成します

### PowerApps に構成を追加する
この API を PowerApps Enterprise に追加するとき、Office 365 Azure Active Directory (AAD) アプリケーションの**クライアント ID**と**クライアント シークレット**の値を入力します。Office 365 アプリケーションには、**リダイレクト URL** 値も使用されます。Office 365 アプリケーションがない場合は、次の手順でアプリケーションを作成できます。

1. [Azure ポータル][5]で **[Active Directory]** を開き、組織のテナント名を開きます。
2. **[アプリケーション]** タブを選択し、**[追加]** をクリックします。  
![AAD テナント アプリケーション][7]

3. **[アプリケーションの追加]** で次の操作を行います。  

	1. アプリケーションの **[名前]** を入力します。  
	2. アプリケーションの種類は **[Web]** のままにします。  
	3. **[次へ]** を選択します。  

	![AAD アプリケーションの追加 - アプリケーション情報][8]

4. **[アプリケーションのプロパティ]** で次の操作を行います。  

	1. アプリケーションの**サインオン URL** を入力します。PowerApps の AAD に対して認証するので、_https://login.windows.net_ のサインオン URL を設定します。  
	2. アプリの有効な**アプリ ID URI** を入力します。  
	3. **[OK]** を選択します。  

	![AAD アプリケーションの追加 - アプリケーションのプロパティ][9]

5. 完了すると、新しい AAD アプリケーションが開きます。**[構成]** をクリックします。  
![Contoso AAD アプリケーション][10]

6. **OAuth 2** セクションの **[応答 URL]** を、Azure ポータルで Office 365 Users API を追加したときに表示されたリダイレクト URL 値に設定します。**[アプリケーションの追加]** をクリックします。  
![Contoso AAD アプリケーションの構成][11]

7. **[他のアプリケーションに対するアクセス許可]** で、**[Office 365 統合 API (プレビュー)]** を選択し、**[OK]** をクリックします。

	構成ページに戻り、_[他のアプリケーションに対するアクセス許可]_ の一覧に _Office 365 統合 API (プレビュー)_ が追加されていることを確認します。

8. **Office 365 Exchange Online** の **[デリゲートされたアクセス許可]** を選択し、**[すべてのユーザーの基本プロファイルの読み取り]** アクセス許可を選択します。

新しい Azure Active Directory アプリケーションが作成されます。**クライアント ID** と**クライアント シークレット**をコピーして、Azure ポータルの Office 365 Users API 構成に貼り付けます。

「[アプリケーションを Azure AD に追加する方法と理由](../active-directory/active-directory-how-applications-are-added.md)」に AAD アプリケーションに関する有用な情報があります。


### ロジック アプリに構成を追加する
この API をロジック アプリに追加する場合は、Office 365 ユーザー アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

1. Office 365 ユーザー アカウントにサインインします。
2. ロジック アプリが Office 365 アカウントに接続して使用することを許可します。 

接続を作成したら、ユーザー ID など、Office 365 ユーザー プロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**を参照してください。

>[AZURE.TIP] 他のロジック アプリでも、前述の Office 365 ユーザー接続を使用できます。


## Office 365 Users REST API リファレンス
適用されるバージョン: 1.0。

### 自分のプロファイルを取得 
現在のユーザーのプロファイルを取得します。  
```GET: /users/me```

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


### ユーザー プロファイルの取得 
特定のユーザー プロファイルを取得します。  
```GET: /users/{userId}```

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


### 上司の取得 
指定されたユーザーの上司のユーザー プロファイルを取得します。  
```GET: /users/{userId}/manager```

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



### 直接の部下を取得 
直接の部下を取得します。  
```GET: /users/{userId}/directReports```

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



### ユーザーの検索 
ユーザー プロファイルの検索結果を取得します。  
```GET: /users```

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



## オブジェクト定義

#### ユーザー: ユーザー モデル クラス

| 名前 | データ型 |必須
|---|---|---|
|DisplayName|string|×|
|GivenName|string|×|
|Surname|string|×|
|Mail|string|×|
|MailNickname|string|×|
|TelephoneNumber|string|×|
|AccountEnabled|ブール値|×|
|ID|string|○
|UserPrincipalName|string|×|
|学科|string|×|
|JobTitle|string|×|
|MobilePhone|string|×|


## 次のステップ
Office 365 API を PowerApps Enterprise に追加したら、この API をアプリで利用する[許可をユーザーに与えます](../power-apps/powerapps-manage-api-connection-user-access.md)。

[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0302_2016-->


