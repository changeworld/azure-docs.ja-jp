<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="Azure AD の B2C プレビューで構築できるアプリケーションの種類"
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
	ms.topic="article"
	ms.date="09/04/2015"
	ms.author="dastrock"/>

# Azure AD B2C プレビュー: アプリケーションの種類

Azure AD B2C は、さまざまなモダン アプリ アーキテクチャ向けの認証をサポートしています。そのすべてが、業界標準のプロトコルである [OAuth 2.0](active-directory-b2c-reference-protocols.md) と [OpenID Connect](active-directory-b2c-reference-protocols.md) のどちらか一方または両方に基づいています。このドキュメントでは、利用する言語やプラットフォームを問わず、作成できるアプリの種類について簡単に説明します。このドキュメントは、[コードの記述を今すぐ始める](active-directory-b2c-overview.md#getting-started)前に、大まかなシナリオを理解するのに役立ちます。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 基本操作
Azure AD B2C を使用するすべてのアプリは、[Azure プレビュー ポータル](https://portal.azure.com)を通して [B2C ディレクトリ](active-directory-b2c-get-started.md)に登録する必要があります。アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

- アプリを一意に識別する**アプリケーション ID**。
- 応答をアプリにリダイレクトして戻すために使用できる**リダイレクト URI**。
- その他シナリオに応じた値。詳細については、[アプリを登録する](active-directory-b2c-app-registration.md)方法を参照してください。

登録済みのアプリは、Azure AD v2.0 エンドポイントに要求を送ることによって、Azure AD と通信します。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Azure AD B2C に送信される各要求は、**ポリシー**を指定します。ポリシーは Azure AD の動作を制御し、これらのエンドポイントを使用して、高度にカスタマイズ可能なユーザー エクスペリエンスのセットを作成できます。一般的なポリシーとして、サインアップ ポリシー、サインイン ポリシー、プロファイル編集ポリシーがあります。これらのポリシーを詳しく理解していない場合は、読み進む前に Azure AD B2C の「[拡張可能ポリシー フレームワーク](active-directory-b2c-reference-policies.md)」を参照する必要があります。

アプリによる v2.0 エンドポイントとのやり取りは、いずれも次のような大まかなパターンに従って行われます。

1. アプリが[ポリシー](active-directory-b2c-reference-policies.md)を実行するためにエンド ユーザーを v2.0 エンドポイントにリダイレクトします。
2. ユーザーがポリシーの定義に従ってポリシーを完了します。
4. アプリが v2.0 エンドポイントから何らかの種類のセキュリティ トークンを受け取ります。
5. アプリがセキュリティ トークンを使って、保護された情報 (リソース) にアクセスします。
6. リソース サーバーがセキュリティ トークンを検証し、アクセスを許可できることを確かめます。
7. アプリがセキュリティ トークンを定期的に更新します。

<!-- TODO: Need a page for libraries to link to -->
これらの手順は、作成するアプリの種類に応じて若干異なりますが、細かい処理を代わりに実行してくれるオープン ソース ライブラリが用意されています。

## Web Apps
サーバーでホストされ、ブラウザーを通してアクセスされる Web アプリ (.NET、PHP、Java、Ruby、Python、Node など) に対して、Azure AD B2C は、サインイン、サインアウト、プロファイル管理を含むすべてのユーザー エクスペリエンスで [OpenID Connect](active-directory-b2c-reference-protocols.md) をサポートします。Azure AD B2C の OpenID Connect の実装では、Web アプリは、これらのユーザーエクスペリエンスを Azure AD に認証要求を発行することで開始します。この要求の結果は `id_token` であり、これはユーザーの ID を表し、クレームの形でユーザーに関する情報を提供するセキュリティ トークンです。

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

アプリで利用できるすべての種類のトークンとクレームについては、「[Azure AD B2C プレビュー: トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。

Web アプリでは、[ポリシー](active-directory-b2c-reference-policies.md)を実行するたびに、次の高レベルの手順が実行されます。

![Web App Swimlanes Image](./media/active-directory-b2c-apps/webapp.png)

Azure AD エンドポイントから受け取った公開署名キーを使用した id\_token の検証は、ユーザーの ID を確認し、以降のページ要求でユーザーを識別するために使用できるセッション Cookie を設定するうえで十分です。

このシナリオを実際に確認するには、「[作業の開始](active-directory-b2c-overview.md#getting-started)」セクションのいずれかの Web アプリ サインイン コード サンプルを試してください。

Web サーバー アプリは、単純なサインインを実行するだけでなく、他のバックエンド Web サービスにアクセスすることが必要な場合もあります。この場合、わずかに異なる [OpenID Connect フロー](active-directory-b2c-reference-oidc.md)を実行して、認証コードを使用するトークンと更新トークンを取得できます。このシナリオについては、次の「[Web API](#web-apis)」セクションで説明します。

<!--, and in our [WebApp-WebAPI Getting Started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## Web API
Azure AD B2C を使用して、アプリの RESTful Web API などの Web サービスもセキュリティで保護できます。Web API では、OAuth 2.0 を使用してデータをセキュリティで保護し、トークンを使用して着信する HTTP 要求を認証できます。Web API の呼び出し元は、HTTP 要求の認証ヘッダーの中にトークンを追加します。

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API はトークンを使用して API の呼び出し元の ID を検証し、トークン内にエンコードされているクレームから呼び出し元に関する情報を抽出します。アプリで利用できるすべての種類のトークンとクレームについては、「[Azure AD B2C プレビュー: トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。

> [AZURE.NOTE]Azure AD B2C プレビューは、現時点では、Web API の既知のクライアントによってアクセスされる Web API のみサポートします。たとえば、完成したアプリには、iOS アプリ、Android アプリ、Web API を含めることができます。このアーキテクチャは完全にサポートされます。現時点でサポートされていないのは、別の iOS アプリなどのサード パーティ クライアントが同じ Web API にアクセスできるようにすることです。実際には、完成したアプリの各コンポーネントは、1 つのアプリケーション ID を共有する必要があります。

Web API は、Web アプリ、デスクトップ アプリ、モバイル アプリ、シングル ページ アプリ、サーバー サイド デーモン、それ以外の Web API など、あらゆる種類のクライアントからトークンを受信できます。例として、Web API を呼び出す Web アプリのフロー全体を見てみましょう。

![Web App Web API Swimlanes Image](./media/active-directory-b2c-apps/webapi.png)

authorization\_code、refresh\_token、およびトークンの詳しい取得手順については、「[Azure AD B2C プレビュー: OAuth 2.0 認証コード フロー](active-directory-b2c-reference-oauth-code.md)」を参照してください。

Azure AD B2C で Web API をセキュリティで保護する方法の詳細については、Web API チュートリアルの「[作業の開始](active-directory-b2c-overview.md#getting-started)」セクションを参照してください。
	
## モバイル アプリとネイティブ アプリ
モバイル アプリやデスクトップ アプリなどのデバイスにインストールされているアプリは、多くの場合、ユーザーの代わりにバックエンド サービスや Web API にアクセスする必要があります。カスタマイズされた ID 管理エクスペリエンスをネイティブ アプリに追加し、Azure AD B2C と[OAuth 2.0 Authorization Code Flow](active-directory-b2c-reference-oauth-code.md)を使用して、バックエンド サービスを安全に呼び出すことができます。.

このフローでは、アプリが[ポリシー](active-directory-b2c-reference-policies.md)を実行し、ユーザーがポリシーを完了した後、Azure AD から authorization\_code を受信します。authorization\_code は、現在サインインしているユーザーに代わってバックエンド サービスを呼び出すためのアプリのアクセス許可を表します。その後、アプリは、authoriztion\_code をバックグラウンドで id\_token と refresh\_token と交換できます。アプリは id\_token を使用して HTTP 要求で Web API に対する認証を実行でき、id\_token の有効期限が切れた場合は、refresh\_token を使用して新しい id\_token を取得できます。

> [AZURE.NOTE]Azure AD B2C プレビューは、現時点では、アプリ独自のバックエンド Web サービスにアクセスするために使用される id\_token の取得のみをサポートします。たとえば、完成したアプリには、iOS アプリ、Android アプリ、Web API を含めることができます。このアーキテクチャは完全にサポートされます。現時点でサポートされていないのは、iOS アプリが OAuth 2.0 access\_token を使用してサード パーティ の Web API にアクセスできるようにすることです。実際には、完成したアプリの各コンポーネントは、1 つのアプリケーション ID を共有する必要があります。

![Native App Swimlanes Image](./media/active-directory-b2c-apps/native.png)

## 現在のプレビューの制限事項
これらの種類のアプリは、現時点では Azure AD B2C ではサポートされていませんが、一般公開に間に合うように準備中です。Azure AD B2C プレビューのその他の制限事項については、「[Azure Active Directory B2C プレビュー: 制限事項](active-directory-b2c-limitations.md)」を参照してください。

### シングル ページ アプリ (JavaScript)
最近多く見かけるようになったシングル ページ アプリのフロントエンドは、主に JavaScript で作成されています。AngularJS、Ember.js、Durandal などの SPA フレームワークが使われることも少なくありません。一般公開される Azure AD サービスでは、これらのアプリは OAuth 2.0 Implicit Flow を使用してサポートされます。ただし、このフローは Azure AD B2C ではまだ利用できません。サポートされるまでには、あと少し時間がかかります。

### デーモン/サーバー サイド アプリ
長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。これらのアプリは、OAuth 2.0 Client Credentials Flow で (ユーザーの委任 ID ではなく) アプリの ID を使用して認証を行い、トークンを取得することができます。

このフローは、現時点では Azure AD B2C ではサポートされていません。つまり、アプリは、対話的なユーザー フローが起こった後でのみトークンを取得できます。近い将来、クライアント資格情報フローが追加されます。

### Web API チェーン (On-Behalf-Of)
Azure AD によって保護された Web API から、同じように保護された別の下流の Web API を呼び出す手法は、多くのアーキテクチャで使われています。このシナリオは、バックエンドの Web API から Microsoft Online サービス (Azure AD Graph API など) を呼び出すネイティブ クライアントでよく見られます。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 Jwt Bearer Credential Grant (On-Behalf-Of Flow) を使用してサポートできます。ただし、現時点では、Azure AD B2C プレビューに On-Behalf-Of Flow は実装されていません。

<!---HONumber=Sept15_HO3-->