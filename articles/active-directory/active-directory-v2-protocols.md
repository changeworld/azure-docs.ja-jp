<properties
	pageTitle="アプリ モデル v2.0 のプロトコル | Microsoft Azure"
	description="Azure AD v2.0 アプリ モデル パブリック プレビューでサポートされているプロトコル。"
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
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: プロトコル - OAuth 2.0 と OpenID Connect

v2.0 アプリ モデルは、業界標準のプロトコルである OpenID Connect と OAuth 2.0 をサポートすることによって Identity-as-a-Service (サービスとしての ID) 機能をアプリに提供します。このサービスは標準に準拠していますが、これらのプロトコルには、実装によって微妙な違いが存在する場合があります。ここでは、いずれかのオープン ソース ライブラリを使うのではなく、コードから直接 HTTP 要求を送信して処理する場合に役立つ情報を紹介しています。<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。一般公開されている Azure AD サービスとの統合手順については、「[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)」を参照してください。

## 基本操作
v2.0 アプリ モデルを使うアプリは、すべて [apps.dev.microsoft.com](https://apps.dev.microsoft.com) で登録する必要があります。アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

- アプリを一意に識別する**アプリケーション ID**。
- **リダイレクト URI** または**パッケージ識別子** (アプリに戻す応答をリダイレクトする際に使用)。
- その他シナリオに応じた値。詳細については、[アプリの登録](active-directory-v2-app-registration.md)方法を参照してください。

登録済みのアプリは、v2.0 エンドポイントに要求を送ることによって、Azure AD と通信を行います。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

OAuth と OpenID Connect におけるフローはほぼすべて、情報のやり取りに 4 つの当事者が関係します。

![OAuth 2.0 Roles](../media/active-directory-v2-flows/protocols_roles.png)

- **承認サーバー**は v2.0 エンドポイントです。ユーザーの本人性確認、リソースへのアクセス権の付与と取り消し、トークンの発行という役割を担います。ID プロバイダーと呼ばれることもあります。ユーザーの情報とそのアクセス、そしてフロー内の当事者間の信頼関係に関するすべてのことは、承認サーバーによって安全に処理されます。
- **リソース所有者**とは通常、エンド ユーザーを指します。データを所有し、そのデータ (またはリソース) へのアクセスをサード パーティに許可する権限を持つ当事者です。
- **OAuth クライアント**は、皆さんが開発するアプリです。対応するアプリケーション ID で識別されます。通常はこの当事者がエンド ユーザーと情報をやり取りし、承認サーバーにトークンを要求します。クライアントには、リソース所有者がリソースへのアクセス権を付与する必要があります。
- **リソース サーバー**は、リソースまたはデータが存在する場所です。承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。リソースへのアクセスを許可するためにベアラー トークン (access\_tokens) が使用されます。


## トークン
v2.0 アプリ モデルで実装している OAuth 2.0 と OpenID Connect では、ベアラー トークンが広範囲に使われています (JWT として表現されたベアラー トークンなど)。ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。この意味で、"ベアラー" はトークンを提示できる任意の利用者を表します。利用者がベアラー トークンを受信するには、まず Azure AD による認証が必要となりますが、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがないため、トランスポート層セキュリティ (HTTPS) などのセキュリティで保護されたチャネルで転送する必要があります。ベアラー トークンが暗号化されずに転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。ベアラー トークンのセキュリティに関する考慮事項の詳細については、[RFC 6750 セクション 5](http://tools.ietf.org/html/rfc6750) をご覧ください。

v2.0 アプリ モデルで使われている各種トークンの詳細については、[v2.0 アプリ モデルのトークン リファレンス](active-directory-v2-tokens.md)を参照してください。

## プロトコル

要求の例を理解できる状態になったら、最初に以下のチュートリアルのどれかをご覧ください。いずれも、特定の認証シナリオに対応しています。どのフローを見ればよいかわからない場合は、[アプリ モデル v2.0 で作成できるアプリの種類](active-directory-v2-flows.md)を確認してください。

- [OAuth 2.0 でモバイル アプリケーションおよびネイティブ アプリケーションを作成する](active-directory-v2-protocols-oauth-code.md)
- [Open ID Connect で Web Apps を作成する](active-directory-v2-protocols-oidc.md)
- [OAuth 2.0 Implicit Flow で単一ページのアプリを作成する](active-directory-v2-protocols-implicit.md)
- OAuth 2.0 Client Credentials Flow でデーモンまたはサーバー側プロセスを作成する (近日公開予定)
- OAuth 2.0 On Behalf Of Flow を使用して Web API でトークンを取得する (近日公開予定)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) -->
<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=AcomDC_1217_2015-->