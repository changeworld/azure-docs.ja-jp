<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="Azure Active Directory B2C プレビューで構築できるアプリケーションの種類。"
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/25/2016"
	ms.author="dastrock"/>

# Azure Active Directory B2C プレビュー: アプリケーションの種類

Azure Active Directory (Azure AD) B2C は、さまざまな最新アプリ アーキテクチャの認証をサポートします。すべての認証は、業界標準のプロトコルである [OAuth 2.0](active-directory-b2c-reference-protocols.md) または [OpenID Connect](active-directory-b2c-reference-protocols.md) に基づいています。このドキュメントでは、利用する言語やプラットフォームを問わず、作成できるアプリの種類について簡単に説明します。また、[アプリケーションの構築を始める](active-directory-b2c-overview.md#getting-started)前にシナリオの概要を理解することもできます。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 基本
Azure AD B2C を使用するすべてのアプリは、[Azure ポータル](https://portal.azure.com/)で [B2C ディレクトリ](active-directory-b2c-get-started.md)に登録する必要があります。アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

- アプリを一意に識別する**アプリケーション ID**。
- 応答をアプリにリダイレクトして戻すために使用できる**リダイレクト URI**。
- その他のシナリオに応じた値。詳細については、[アプリの登録](active-directory-b2c-app-registration.md)方法を参照してください。

登録の済んだアプリは、Azure AD v2.0 エンドポイントに要求を送ることによって、Azure AD と通信します。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Azure AD B2C に送信される各要求は、**ポリシー**を指定します。ポリシーは、Azure AD の動作を制御します。これらのエンドポイントを使用して、高度にカスタマイズ可能なユーザー エクスペリエンスのセットを作成することもできます。一般的なポリシーとしては、サインアップ ポリシー、サインイン ポリシー、プロファイル編集ポリシーなどがあります。これらのポリシーを詳しく理解していない場合は、先に進む前に Azure AD B2C の「[拡張可能ポリシー フレームワーク](active-directory-b2c-reference-policies.md)」を参照する必要があります。

すべてのアプリによる v2.0 エンドポイントとのやり取りは、次のような大まかなパターンに従って行われます。

1. アプリは、[ポリシー](active-directory-b2c-reference-policies.md)を実行するためにユーザーを v2.0 エンドポイントにリダイレクトします。
2. ユーザーがポリシーの定義に従ってポリシーを完了します。
4. アプリは、v2.0 エンドポイントから何らかの種類のセキュリティ トークンを受け取ります。
5. アプリは、セキュリティ トークンを使って、保護された情報またはリソースにアクセスします。
6. リソース サーバーは、セキュリティ トークンを検証し、アクセスを許可できることを確認します。
7. アプリは、セキュリティ トークンを定期的に更新します。

<!-- TODO: Need a page for libraries to link to -->
これらの手順は、構築しているアプリの種類によりわずかに異なることがあります。細部は、オープン ソース ライブラリが自動的に対応できます。

## Web Apps
サーバーでホストされ、ブラウザーを通してアクセスされる Web アプリ (.NET、PHP、Java、Ruby、Python、Node.js など) に対して、Azure AD B2C は、すべてのユーザー エクスペリエンスで [OpenID Connect](active-directory-b2c-reference-protocols.md) をサポートします。これには、サインイン、サインアップ、およびプロファイル管理が含まれます。Azure AD B2C の OpenID Connect の実装では、Web アプリは、これらのユーザーエクスペリエンスを Azure AD に認証要求を発行することで開始します。要求の結果は `id_token` です。このセキュリティ トークンは、ユーザーの ID を表します。また、要求の形式でユーザーに関する情報も提供します。

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

アプリで利用できるトークンとクレームの種類の詳細については、「[Azure AD B2C プレビュー: トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。

Web アプリでは、[ポリシー](active-directory-b2c-reference-policies.md)を実行するたびに、概要で見ると次の手順が実行されます。

![Web アプリのスイムレーン イメージ](./media/active-directory-b2c-apps/webapp.png)

ユーザーの身元を確認するには、Azure AD から受け取った公開署名キーを使用して `id_token` を検証するだけで十分です。これにより、以降のページ要求でユーザーを識別するために使用できるセッション Cookie も設定されます。

このシナリオを実際に確認するには、「[作業の開始](active-directory-b2c-overview.md#getting-started)」セクションのいずれかの Web アプリ サインイン コード サンプルを試してください。

Web サーバー アプリは、サインインを簡単にするだけでなく、他のバックエンド Web サービスにアクセスすることが必要な場合もあります。この場合、わずかに異なる [OpenID Connect フロー](active-directory-b2c-reference-oidc.md)を実行し、承認コードと更新トークンを使用してトークンを取得できます。このシナリオについては、次の「[Web API](#web-apis)」セクションで説明します。

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## Web API
Azure AD B2C を使用して、アプリの RESTful Web API などの Web サービスをセキュリティで保護できます。Web API は、OAuth 2.0 を使用してデータをセキュリティ保護できます。また、トークンを使用して受信した HTTP 要求を認証することもできます。Web API の呼び出し元は、HTTP 要求の承認ヘッダーの中にトークンを追加します。

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API はトークンを使用して API の呼び出し元の ID を検証し、トークン内にエンコードされているクレームから呼び出し元に関する情報を抽出します。アプリで利用できるトークンとクレームの種類の詳細については、「[Azure AD B2C プレビュー: トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。

> [AZURE.NOTE]
	Azure AD B2C プレビューは、現時点では、Web API の既知のクライアントによってアクセスされる Web API のみをサポートします。たとえば、完成したアプリには、iOS アプリ、Android アプリ、Web API を含めることができます。このアーキテクチャは完全にサポートされます。別の iOS アプリなどのパートナー クライアントが同じ Web API にアクセスできるようにすることは、現時点ではサポートされていません。完成したアプリのすべてのコンポーネントは、1 つのアプリケーション ID を共有する必要があります。

Web API は、Web アプリ、デスクトップ アプリ、モバイル アプリ、シングル ページ アプリ、サーバー サイド デーモン、それ以外の Web API など、多くの種類のクライアントからトークンを受信できます。ここでは、Web API を呼び出す Web アプリの完全なフローの例を示します。

![Web API のスイムレーン イメージ](./media/active-directory-b2c-apps/webapi.png)

承認コード、更新トークン、およびトークンの取得手順については、「[Azure AD B2C プレビュー: OAuth 2.0 認証コード フロー](active-directory-b2c-reference-oauth-code.md)」を参照してください。

Azure AD B2C を使用して Web API をセキュリティ保護する方法の詳細については、Web API チュートリアルの「[作業の開始](active-directory-b2c-overview.md#getting-started)」セクションを参照してください。

## モバイル アプリとネイティブ アプリ
モバイル アプリやデスクトップ アプリなどのデバイスにインストールされているアプリは、多くの場合、ユーザーの代わりにバックエンド サービスや Web API にアクセスする必要があります。カスタマイズされた ID 管理エクスペリエンスをネイティブ アプリに追加し、Azure AD B2C と [OAuth 2.0 の承認コード フロー](active-directory-b2c-reference-oauth-code.md)を使用して、バックエンド サービスを安全に呼び出すことができます。

このフローでは、アプリが[ポリシー](active-directory-b2c-reference-policies.md)を実行し、ユーザーがポリシーを完了した後、Azure AD から `authorization_code` を受け取ります。`authorization_code` は、現在サインインしているユーザーに代わってバックエンド サービスを呼び出すためのアプリのアクセス許可を表します。アプリは、バックグラウンドの `authorization_code` を `id_token` および `refresh_token` と交換できます。アプリは、`id_token` を使用して、HTTP 要求のバックエンド Web API に対する認証を行うことができます。また、古い `id_token` の有効期限が切れたときは、`refresh_token` を使用して新しいものを取得できます。

> [AZURE.NOTE]
	Azure AD B2C プレビューは、現時点では、アプリ独自のバックエンド Web サービスにアクセスするために使用される ID トークンだけをサポートします。たとえば、完成したアプリには、iOS アプリ、Android アプリ、Web API を含めることができます。このアーキテクチャは完全にサポートされます。iOS アプリが OAuth 2.0 アクセス トークンを使用してパートナー の Web API にアクセスできるようにすることは、現時点ではサポートされていません。完成したアプリのすべてのコンポーネントは、1 つのアプリケーション ID を共有する必要があります。

![ネイティブ アプリのスイムレーン イメージ](./media/active-directory-b2c-apps/native.png)

## 現在のプレビューの制限事項
現時点では Azure AD B2C は以下の種類のアプリをサポートしていませんが、一般公開に間に合うように準備中です。Azure AD B2C プレビューに関するその他の制限事項については、「[Azure Active Directory B2C プレビュー: 制限事項および制約事項](active-directory-b2c-limitations.md)」を参照してください。

### シングル ページ アプリ (JavaScript)
最新アプリの多くには、主に JavaScript で記述されたシングル ページ アプリのフロントエンドがあります。多くの場合、AngularJS、Ember.js、Durandal などのフレームワークを使用します。一般に利用可能な Azure AD サービスは、OAuth 2.0 の暗黙的なフローを使用して、これらのアプリをサポートします。ただし、このフローは Azure AD B2C ではまだ使用できません。間もなく使用できるようになる予定です。

### デーモン/サーバー側アプリ
長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。これらのアプリは、(ユーザーの委任 ID ではなく) アプリの ID と OAuth 2.0 のクライアント資格情報フローを使用して、認証を行い、トークンを取得することができます。

このフローは現在、Azure AD B2C ではサポートされていません。これらのアプリは、対話型ユーザー フローが行われた後でのみ、トークンを取得できます。近い将来、クライアント資格情報フローが追加される予定です。

### Web API チェーン (On-Behalf-Of フロー)
多くのアーキテクチャには別のダウンストリーム Web API を呼び出す必要がある Web API が含まれ、その場合は両方とも Azure AD B2C によってセキュリティ保護されます。このシナリオは、Web API バックエンドのあるネイティブ クライアントで一般的なものです。その後は、Azure AD Graph API などの Microsoft オンライン サービスを呼び出します。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 JWT Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートできます。ただし、現時点では、Azure AD B2C プレビューに On-Behalf-Of フローは実装されていません。

<!---HONumber=AcomDC_0302_2016-->