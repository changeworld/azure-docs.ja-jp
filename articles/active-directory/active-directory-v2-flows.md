<properties
	pageTitle="v2.0 エンドポイントの種類 | Microsoft Azure"
	description="Azure AD v2.0 エンドポイントでサポートされているアプリの種類とシナリオ。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# v2.0 エンドポイントの種類
v2.0 エンドポイントは、さまざまなモダン アプリ アーキテクチャ向けの認証をサポートしています。そのいずれも、業界標準のプロトコルである [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) と [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) のどちらか一方または両方に基づいています。このドキュメントでは、利用する言語やプラットフォームを問わず、作成できるアプリの種類について簡単に説明します。このドキュメントは、[コードを詳しく確認する](active-directory-appmodel-v2-overview.md#getting-started)前に、大まかなシナリオを理解するのに役立ちます。

> [AZURE.NOTE]
	Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。

## 基本
v2.0 エンドポイントを使うアプリは、すべて [apps.dev.microsoft.com](https://apps.dev.microsoft.com) で登録する必要があります。アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

- アプリを一意に識別する**アプリケーション ID**。
- 応答をアプリにリダイレクトして戻すために使用できる**リダイレクト URI**。
- その他シナリオに応じた値。詳細については、[アプリの登録](active-directory-v2-app-registration.md)方法を参照してください。

登録済みのアプリは、Azure Active Directory v2.0 エンドポイントに要求を送ることによって、Azure AD と通信します。これらの要求の詳細に対処するオープン ソース フレームワークとライブラリをご用意しています。これらのエンドポイントへの要求を作成して、自分で認証ロジックを実装してもかまいません。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## Web Apps
ブラウザーからアクセスされる Web アプリ (.NET、PHP、Java、Ruby、Python、Node など) の場合、[OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) を使ってユーザーのサインインを実行することができます。OpenID Connect では、Web アプリは `id_token` を受け取ります。これは、ユーザーの ID を検証し、要求の形でユーザーに関する情報を提供するセキュリティ トークンです。

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

アプリから利用できるすべての種類のトークンと要求については、[v2.0 トークン リファレンス](active-directory-v2-tokens.md)を参照してください。

Web サーバー アプリにおけるサインイン認証フローは、主に次のステップで構成されます。

![Web アプリのスイムレーン イメージ](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

v2.0 エンドポイントから受け取った公開署名キーを使用した id\_token の検証は、ユーザーの ID を確認し、後続のページ要求でユーザーを識別するために使用できるセッション Cookie を設定するうえで十分です。

このシナリオを実際に確認するには、「[使用の開始](active-directory-appmodel-v2-overview.md#getting-started)」セクションのいずれかの Web アプリ サインイン コード サンプルを試してください。

Web サーバー アプリは、単純なサインインを実行するだけでなく、REST API をはじめとする他の Web サービスにアクセスすることが必要な場合もあります。この場合、Web サーバー アプリは、OpenID Connect と OAuth 2.0 を組み合わせたフローに関与できます。その際使用されるのが[OAuth 2.0 承認コード フロー](active-directory-v2-protocols.md#oauth2-authorization-code-flow)です。このシナリオは、以下の [WebApp-WebAPI の使用の開始](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)に関するトピックで取り上げています。

## Web API
v2.0 エンドポイントを使用すると、アプリの RESTful Web API など、Web サービスもセキュリティで保護できます。Web API では、id\_token とセッション Cookie の代わりに OAuth 2.0 access\_token を使ってデータをセキュリティ保護し、受信要求を認証します。Web API の呼び出し元は、HTTP 要求の承認ヘッダーの末尾に access\_token を追加します。

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API はその access\_token を使って API の呼び出し元の ID を検証し、access\_token でエンコードされている要求から呼び出し元に関する情報を抽出します。アプリから利用できるすべての種類のトークンと要求については、[v2.0 トークン リファレンス](active-directory-v2-tokens.md)を参照してください。

Web API は、アクセス許可 ([スコープ](active-directory-v2-scopes.md)とも呼ばれる) を公開することで、特定の機能またはデータをオプトイン/オプトアウトできるようにします。呼び出し元のアプリがスコープに対するアクセス許可を取得するには、ユーザーがフローの途中でスコープに同意する必要があります。v2.0 エンドポイントはユーザーにアクセス許可を求め、Web API が受け取るすべての access\_token にこれらのアクセス許可を記録します。Web API で必要なことは、各呼び出しで受け取った access\_token を検証することと、適切な承認確認を行うことだけです。

Web API は、すべての種類のアプリ (Web サーバー アプリ、デスクトップ アプリとモバイル アプリ、シングル ページ アプリ、サーバー側のデーモン、さらにそれ以外の Web API など) から access\_token を受信できます。Web API 認証の大まかな流れは次のとおりです。

![Web API のスイムレーン イメージ](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

authorization\_code と refresh\_token についてのほか、access\_tokens の詳しい取得手順については、[OAuth 2.0 プロトコルの説明](active-directory-v2-protocols-oauth-code.md)を参照してください。

OAuth2 access\_token で Web API をセキュリティ保護する方法については、[使用の開始セクション](active-directory-appmodel-v2-overview.md#getting-started)で Web API コード サンプルを確認してください。


## モバイル アプリとネイティブ アプリ
多くの場合、モバイル アプリやデスクトップ アプリなど、デバイスにインストールされているアプリは、データを格納し、ユーザーの代わりにさまざまな処理を実行するバックエンド サービスや Web API にアクセスする必要があります。これらのアプリは、[OAuth 2.0 承認コード フロー](active-directory-v2-protocols-oauth-code.md)を使ってバックエンド サービスにサインインと承認を追加します。

このフローでは、アプリはユーザーのサインインの際に v2.0 エンドポイントから authorization\_code を受け取ります。これは、現在サインインしているユーザーの代わりにバックエンド サービスを呼び出すアプリのアクセス許可を表します。次に、アプリはバックグラウンドで authoriztion\_code を OAuth 2.0 access\_token および refresh\_token と交換します。アプリはその access\_token を使って HTTP 要求で Web API に対して認証を実行します。また、access\_token の有効期限が切れた場合は、refresh\_token を使って新しい access\_token を取得します。

![ネイティブ アプリのスイムレーン イメージ](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## シングル ページ アプリ (JavaScript)
最近多く見かけるようになったシングル ページ アプリ (SPA) のフロントエンドは、主に JavaScript で作成されています。AngularJS、Ember.js、Durandal などのフレームワークが使われることも少なくありません。Azure AD v2.0 エンドポイントは、[OAuth 2.0 暗黙的フロー](active-directory-v2-protocols-implicit.md)を使用してこれらのアプリをサポートします。

このフローでは、アプリは v2.0 認証エンドポイントから直接トークンを受け取るので、バックエンドのサーバー間の交換は実行されません。これにより、すべての認証ロジックとセッション処理は、余分なページのリダイレクトを実行することなく、JavaScript クライアント内ですべて行うことができます。

![暗黙的なフロー スイムレーン イメージ](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

このシナリオを実際に確認するには、「[使用の開始](active-directory-appmodel-v2-overview.md#getting-started)」セクションのいずれかの単一ページ アプリ コード サンプルを試してください。

## 現時点での制限事項
このような種類のアプリは、現時点では、v2.0 エンドポイントでサポートされていませんが、ロードマップが示されています。v2.0 エンドポイントのその他の制限事項については、[v2.0 の制限事項に関する記事](active-directory-v2-limitations.md)を参照してください。

### デーモン/サーバー サイド アプリ
長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。これらのアプリは、OAuth 2.0 Client Credentials Flow で (ユーザーの委任 ID ではなく) アプリの ID を使用して認証を行い、トークンを取得することができます。

クライアント資格情報フローは、現時点では、v2.0 エンドポイントでサポートされていません。一般提供版 Azure AD サービスにおけるこのフローの動作については、[GitHub のデーモン コード サンプル](https://github.com/AzureADSamples/Daemon-DotNet)を参照してください。

### Web API の連鎖 (On-Behalf-Of)
v2.0 エンドポイントによって保護された Web API から、同じように保護された別の下流の Web API を呼び出す手法は、多くのアーキテクチャで使われています。このシナリオは、バックエンドの Web API から Microsoft Online サービス (Office 365、Graph API など) を呼び出すネイティブ クライアントでよく見られます。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 Jwt Bearer Credential Grant ([On-Behalf-Of フロー](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow)) を使用してサポートできます。しかし現時点では v2.0 エンドポイントに On-Behalf-Of フローが実装されていません。一般提供版 Azure AD サービスにおけるこのフローの動作については、[GitHub の On-Behalf-Of コード サンプル](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)を参照してください。

<!---HONumber=AcomDC_0921_2016-->