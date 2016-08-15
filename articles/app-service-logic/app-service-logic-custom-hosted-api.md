<properties
	pageTitle="ロジック アプリでのカスタム API の呼び出し"
	description="App Service でホストされたカスタム API のロジック アプリでの使用"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="logic-apps"
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="stepsic"/>

# App Service でホストされたカスタム API のロジック アプリでの使用

ロジック アプリには、さまざまなサービス用に 40 以上のコネクタの豊富なセットがありますが、独自のカスタム API を呼び出して独自のコードを実行することもできます。独自のカスタム Web API をホストする最も簡単かつスケーラブルな方法の 1 つは、App Service を使用することです。この記事では、App Service の API アプリ、Web アプリ、またはモバイル アプリでホストされる任意の Web API を呼び出す方法について説明します。

ロジック アプリ 内のトリガーまたはアクションとして API を構築する方法の詳細については、[この記事](app-service-logic-create-api-app.md)をご覧ください。

## Web アプリのデプロイ

最初に、App Service で Web アプリとして API をデプロイする必要があります。「[Azure App Service での ASP.NET Web アプリの作成](../app-service-web/web-sites-dotnet-get-started.md)」に、基本的なデプロイの手順が説明されています。ロジック アプリ から任意の API を呼び出すことができますが、最適の結果を得るには、ロジック アプリ アクションと簡単に統合するために Swagger メタデータを追加することをお勧めします。[Swagger の追加](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)に関する詳細を確認できます。

### API 設定

ロジック アプリ デザイナーで Swagger を解析するには、CORS を有効にし、Web アプリの APIDefinition プロパティを設定することが重要です。これは、Azure ポータル内で簡単に設定できます。Web アプリの設定ブレードを開いて、API セクションで "API の定義" を swagger.json ファイルの URL (通常は https://{name}.azurewebsites.net/swagger/docs/v1) に設定し、"*" でロジック アプリ デザイナーからの要求を許可する CORS ポリシーを追加するだけです。

## API の呼び出し

ロジック アプリ ポータル内に CORS と API の定義プロパティを設定している場合は、フロー内にカスタム API アクションを簡単に追加できます。デザイナーでは、サブスクリプションの Web サイトを参照して、定義した Swagger URL で Web サイトを一覧表示するよう選択できます。また、HTTP と Swagger アクションを使用して Swagger をポイントして、使用可能なアクションと入力を一覧表示することもできます。最後に、HTTP アクションを使用して、要求をいつでも作成して、Swagger doc のない、または公開しない API を含むすべての API を呼び出すことができます。

API をセキュリティで保護する場合は、他にもいくつかの方法があります。

1. コードの変更が不要 - Azure Active Directory を使用すると、コードの変更や再デプロイを必要とせずに API を保護できます。
1. API のコードで基本認証、AAD 認証、または証明書認証を適用します。

## コード変更を伴わない API の呼び出しのセキュリティ保護

このセクションでは、ロジック アプリ用と Web アプリ用の 2 つの Azure Active Directory アプリケーションを作成します。Web アプリの呼び出しの認証は、ロジック アプリの AAD アプリケーションに関連付けられたサービス プリンシパル (クライアント ID とシークレット) を使用して行います。最後に、ロジック アプリの定義にアプリケーション ID を追加します。

### パート 1: ロジック アプリのアプリケーション ID の設定

これは、ロジック アプリが Active Directory に対する認証に使用する ID です。ID の設定は、ディレクトリに対して 1 回だけ行えば済みます。たとえば、必要に応じてロジック アプリごとに一意の ID を作成することもできますが、すべてのロジック アプリに同じ ID を使用できます。この操作は UI で、または PowerShell を使用して実行できます。

#### Azure クラシック ポータルを使用してアプリケーション ID を作成する

1. [Azure クラシック ポータルの Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) に移動して、Web アプリに使用するディレクトリを選択します。
2. **[アプリケーション]** タブをクリックします。
3. ページの下部にあるコマンド バーで、**[追加]** をクリックします。
4. ID に使用する名前を付けて、[次へ] 矢印をクリックします。
5. ドメイン形式の一意の文字列を 2 つのテキスト ボックスに入力し、チェックマークをクリックします。
6. このアプリケーションの **[構成]** タブをクリックします。
7. **[クライアント ID]** をコピーします。この ID はロジック アプリで使用します。
8. **[キー]** セクションで **[時間の選択]** をクリックし、1 年または 2 年を選択します。
9. 画面の下部にある **[保存]** ボタンをクリックします (保存に数秒かかることがあります)。
10. ここで、ボックス内のキーをコピーしてください。このキーもロジック アプリで使用します。

#### PowerShell を使用したアプリケーション ID の作成
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. **テナント ID**、**アプリケーション ID**、使用したパスワードをコピーしてください。

### パート 2: AAD アプリの ID による Web アプリの保護

Web アプリが既にデプロイされている場合、ポータルでそのアプリを有効にすることができます。それ以外の場合、Azure リソース マネージャーによるデプロイの中で承認を有効にすることができます。

#### Azure ポータルで承認を有効にする

1. Web アプリに移動し、コマンド バーで **[設定]** クリックします。
2. **[承認/認証]** をクリックします。
3. この部分を **[オン]** にします。

この時点で、アプリケーションが自動的に作成されます。このアプリケーションのクライアント ID は、パート 3 で必要となるため、次の手順を実行する必要があります。

1. [Azure クラシック ポータルの Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) に移動してディレクトリを選択します。
2. 検索ボックスでアプリを検索します。
3. 一覧からアプリをクリックします。
4. **[構成]** タブをクリックします。
5. **クライアント ID** が表示されます。

#### ARM テンプレートを使用して Web アプリをデプロイする

最初に、Web アプリ用のアプリケーションを作成する必要があります。これは、ロジック アプリに使用するアプリケーションとは別に作成します。まず上記のパート 1 の手順を実行しますが、今回は **HomePage** と **IdentifierUris** に Web アプリの実際の https://**URL** を使用します。

>[AZURE.NOTE]Web アプリ用にアプリケーションを作成する際、PowerShell コマンドレットではユーザーが Web サイトにサインインするのに必要なアクセス許可が設定されないため、[Azure クラシック ポータルによるアプローチ](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)を使用する必要があります。

クライアント ID とテナント ID を取得したら、Web アプリのサブ リソースとして、デプロイ テンプレートに次のコードを追加します。

```
"resources" : [
	{
		"apiVersion" : "2015-08-01",
		"name" : "web",
		"type" : "config",
		"dependsOn" : [
			"[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
		],
		"properties" : {
			"siteAuthEnabled": true,
			"siteAuthSettings": {
			  "clientId": "<<clientID>>",
			  "issuer": "https://sts.windows.net/<<tenantID>>/",
			}
		}
	}
]
```

デプロイを自動的に実行して、AAD を使用する空の Web アプリとロジック アプリを一緒にデプロイするには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

完全なテンプレートについては、[ロジック アプリによる、App Service でホストされ AAD によって保護されたカスタム API の呼び出し](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)に関するページを参照してください。


### パート 3: ロジック アプリの承認セクションの入力

**HTTP** アクションの **[承認]** セクションで、`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }` を指定します。

| 要素 | 説明 |
|---------|-------------|
| type | 認証の種類。ActiveDirectoryOAuth 認証の場合、値は ActiveDirectoryOAuth です。 |
| テナント | AD テナントを識別するためのテナント識別子。 |
| 対象となる読者 | 必須。接続するリソース。 |
| clientID | Azure AD アプリケーションのクライアント識別子。 |
| secret | 必須。トークンを要求しているクライアントのシークレット。 |

これは、上記のテンプレートで既に設定されていますが、直接ロジック アプリを作成する場合は、承認セクション全体を追加する必要があります。

## コード内の API の保護

### 証明書認証

クライアント証明書を使用して、Web アプリへの受信要求を検証できます。コードの設定方法については、「[Web アプリの TLS 相互認証を構成する方法](../app-service-web/app-service-web-configure-tls-mutual-auth.md)」を参照してください。

*[承認]* セクションで `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}` を指定する必要があります。

| 要素 | 説明 |
|---------|-------------|
| type | 必須。認証の種類。SSL クライアント証明書の場合、値には ClientCertificate を使用する必要があります。 |
| pfx | 必須。Base64 でエンコードされた PFX ファイルのコンテンツ。 |
| パスワード | 必須。PFX ファイルにアクセスするためのパスワード。 |

### 基本認証

基本認証 (ユーザー名とパスワードなど) を使用して、受信要求を検証できます。基本認証は一般的なパターンで、アプリをビルドする任意の言語で実行できます。

*[承認]* セクションで `{"type": "basic","username": "test","password": "test"}` を指定する必要があります。

| 要素 | 説明 |
|---------|-------------|
| type | 必須。認証の種類。基本認証の場合、値には Basic を使用する必要があります。 |
| ユーザー名 | 必須。認証するユーザー名。 |
| パスワード | 必須。認証するパスワード。 |

### コード内での AAD 認証の処理

既定では、ポータルで有効にする Azure Active Directory 認証では、きめ細かい承認は実行されません。たとえば、この認証では、API が特定のユーザーまたはアプリにはロックされず、特定のテナントのみにロックされます。

たとえば、コード内で API をロジック アプリのみに制限したい場合は、JWT を含むヘッダーを抽出して呼び出し元を照会し、一致しないすべての要求を拒否できます。

高度になりますが、自分のコードにこの認証全体を実装し、ポータルの機能を利用しない場合は、「[Azure App Service での認証には、Active Directory を使用します](../app-service-web/web-sites-authentication-authorization.md)」を参照してください。

ただし、ロジック アプリのアプリケーション ID を作成して API の呼び出しに使用する場合は、このページの手順に従う必要があります。

<!---HONumber=AcomDC_0803_2016-->