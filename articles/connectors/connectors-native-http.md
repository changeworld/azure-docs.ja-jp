<properties
	pageTitle="ロジック アプリに HTTP アクションを追加する | Microsoft Azure"
	description="HTTP アクションとプロパティの概要"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# HTTP アクションの概要

HTTP アクションを使用すると、組織のワークフローを拡張し、HTTP 経由で任意のエンドポイントと通信することができます。

- 管理対象の Web サイトがダウンしたときにアクティブ化する (トリガーする) ロジック アプリ ワークフローを作成します。
- HTTP 経由で任意のエンドポイントと通信して、ワークフローを他のサービスに拡張します。

ロジック アプリでの HTTP アクションの使用を開始するには、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事を参照してください。

---

## HTTP トリガーの使用

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。

ロジック アプリ デザイナーで HTTP トリガーをセットアップする方法の例を次に示します。

1. ロジック アプリで HTTP トリガーを追加します。
1. ポーリングする HTTP エンドポイントのパラメーターを入力します。
1. ポーリング頻度の繰り返し間隔を変更します。
1. これで、各チェック中に返される内容を含むロジック アプリが起動します。

![HTTP トリガー](./media/connectors-native-http/using-trigger.png)

### HTTP トリガーのしくみ

HTTP トリガーは、定期的な実行間隔で HTTP エンドポイントを呼び出します。既定では、HTTP 応答コードが 300 より小さい場合にロジック アプリが実行されます。HTTP 呼び出しの後に、ロジック アプリを起動するかどうかを判断する判断するために評価する条件をコードビューに追加することができます。返された状態コードが `400` 以上の場合に常に起動する HTTP トリガーの例を次に示します。

```javascript
"Http": 
{
	"conditions": [
		{
			"expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
		}
	],
	"inputs": {
		"method": "GET",
		"uri": "https://blogs.msdn.microsoft.com/logicapps/",
		"headers": {
			"accept-language": "en"
		}
	},
	"recurrence": {
		"frequency": "Second",
		"interval": 15
	},
	"type": "Http"
}
```

HTTP トリガーのパラメーターの詳細については、[MSDN を参照](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger)してください。

## HTTP アクションの使用
	
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらを参照してください](connectors-overview.md)。

1. **[New Step (新しいステップ)]** ボタンを選択します。
1. **[Add an action (アクションの追加)]** を選択します。
1. アクションの検索ボックスに「HTTP」と入力して、HTTP アクションの一覧を表示します。

	![Select HTTP action](./media/connectors-native-http/using-action-1.png)

1. HTTP 呼び出しに必要なすべてのパラメーターを追加します。

	![Complete HTTP action](./media/connectors-native-http/using-action-2.png)

1. ツール バーの左上隅にある [保存] をクリックすると、ロジック アプリが保存および発行 (アクティブ化) されます。

---

## 技術的な詳細

このコネクタでサポートされているトリガーとアクションの詳細を次に示します。

## HTTP トリガー

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。 HTTP コネクタには 1 つのトリガーがあります。

|トリガー|説明|
|---|---|
|HTTP|HTTP 呼び出しを実行し、応答コンテンツを返します|

## HTTP アクション

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらを参照してください](connectors-overview.md)。 HTTP コネクタには、使用可能なアクションが 1 つあります。

|アクション|説明|
|---|---|
|HTTP|HTTP 呼び出しを実行し、応答コンテンツを返します|

## HTTP の詳細

HTTP コネクタには、使用可能なアクションが 1 つ用意されています。各アクションの情報、必須および任意の入力フィールド、アクションの使用に関連した対応する出力の詳細を次に示します。

### HTTP 要求
HTTP 送信要求を実行します。* は必須フィールドを意味します。

|表示名|プロパティ名|説明|
|---|---|---|
|メソッド*|method|使用する HTTP 動詞|
|URI*|uri|HTTP 要求の URI|
|ヘッダー|headers|含める HTTP ヘッダーの JSON オブジェクト|
|本文|body|HTTP 要求の本文|
|認証|authentication|[詳細についてはこちらを参照](#authentication)|
<br>

**出力の詳細**

HTTP 応答

|プロパティ名|データ型|説明|
|---|---|---|
|ヘッダー|オブジェクト|応答ヘッダー|
|本文|オブジェクト|応答オブジェクト|
|状態コード|int|HTTP 状態コード|

## 認証

Logic Apps では、HTTP エンドポイントに対してさまざまな種類の認証を使用できます。この認証は、HTTP、[HTTP + Swagger](./connectors-native-http-swagger.md)、[HTTP Webhook](./connectors-native-webhook.md) の各コネクタで使用できます。次の種類の認証を構成できます。

* [基本認証](#basic-authentication)
* [ClientCertificate 認証](#client-certificate-authentication)
* [ActiveDirectoryOAuth 認証](#azure-active-directory-oauth-authentication)

#### 基本認証

基本認証には、次の認証オブジェクトが必要です。* は必須フィールドを示しています。

|プロパティ名|データ型|説明|
|---|---|---|
|Type*|type|認証の種類。基本認証の場合、値として `Basic` を指定する必要があります。|
|Username*|username|認証するユーザー名。|
|Password*|パスワード|認証するパスワード。|

>[AZURE.TIP] 定義から取得できないパスワードを使用する場合は、`securestring` パラメーターと `@parameters()` [ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。

認証フィールドで次のようなオブジェクトを作成します。

```javascript
{
	"type": "Basic",
	"username": "user",
	"password": "test"
}
```

#### クライアント証明書認証

クライアント証明書認証には、次の認証オブジェクトが必要です。* は必須フィールドを示しています。

|プロパティ名|データ型|説明|
|---|---|---|
|Type*|type|認証の種類。SSL クライアント証明書の場合、値として `ClientCertificate` を指定する必要があります。|
|PFX*|pfx|Base64 でエンコードされた PFX ファイルのコンテンツ。|
|Password*|パスワード|PFX ファイルにアクセスするためのパスワード。|

>[AZURE.TIP] 保存後に定義内で読み取ることができなくなるパラメーターを使用するには、`securestring` パラメーターと `@parameters()` [ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。

次に例を示します。

```javascript
{
	"type": "ClientCertificate",
	"pfx": "aGVsbG8g...d29ybGQ=",
	"password": "@parameters('myPassword')"
}
```

#### Azure Active Directory OAuth 認証

Azure Active Directory OAuth 認証には、次の認証オブジェクトが必要です。* は必須フィールドを示しています。

|プロパティ名|データ型|説明|
|---|---|---|
|Type*|type|認証の種類。ActiveDirectoryOAuth 認証の場合、値として `ActiveDirectoryOAuth` を指定する必要があります。|
|Tenant*|テナント|Azure AD テナントのテナント識別子。|
|Audience*|対象となる読者|`https://management.core.windows.net/` に設定します。|
|Client ID*|clientId|Azure AD アプリケーションのクライアント識別子を指定します。|
|Secret*|secret|トークンを要求しているクライアントのシークレット。|

>[AZURE.TIP] 保存後に定義内で読み取ることができなくなるパラメーターを使用するには、`securestring` パラメーターと `@parameters()` [ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。

次に例を示します。

```javascript
{
	"type": "ActiveDirectoryOAuth",
	"tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
	"audience": "https://management.core.windows.net/",
	"clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
	"secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

---

## 次のステップ

ロジック アプリに取り組む方法とコミュニティの詳細を次に示します。

## ロジック アプリを作成します

プラットフォームを試用し、[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)してください。[API リスト](apis-list.md)を参照すると、ロジック アプリで使用可能な他のコネクタについて確認できます。

<!---HONumber=AcomDC_0727_2016-->