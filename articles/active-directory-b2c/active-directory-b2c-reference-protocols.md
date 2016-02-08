<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="Azure AD B2C プレビューによってサポートされるプロトコルを直接使用してアプリを作成する方法。"
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Azure AD B2C プレビュー: 認証プロトコル

Azure AD B2C では、業界標準のプロトコルである OpenID Connect と OAuth 2.0 をサポートすることによって Identity-as-a-Service (サービスとしての ID) 機能がアプリに提供されます。このサービスは標準に準拠していますが、これらのプロトコルには、実装によって微妙な違いが存在する場合があります。ここでは、いずれかのオープン ソース ライブラリを使うのではなく、コードから直接 HTTP 要求を送信して処理する場合に役立つ情報を紹介します。各プロトコルの詳細に進む前にこのページの概要情報を読むことをお勧めしますが、Azure AD B2C について既に詳しい場合は[プロトコルのリファレンス ガイド](#protocols)にすぐに進んでもかまいません。

<!-- TODO: Need link to libraries above -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 基本操作
Azure AD B2C を使用するすべてのアプリは、[Azure ポータル](https://portal.azure.com/)で B2C ディレクトリに登録する必要があります。アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

- アプリを一意に識別する**アプリケーション ID**。
- **リダイレクト URI** または**パッケージ識別子** (アプリに戻す応答をリダイレクトする際に使用)。
- その他シナリオに応じた値。詳細については、[アプリの登録](active-directory-b2c-app-registration.md)方法を参照してください。

登録済みのアプリは、v2.0 エンドポイントに要求を送ることによって、Azure AD と通信を行います。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

OAuth と OpenID Connect におけるフローはほぼすべて、情報のやり取りに 4 つの当事者が関係します。

![OAuth 2.0 Roles](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- **承認サーバー**は Azure AD v2.0 エンドポイントです。ユーザーの本人性確認、リソースへのアクセス権の付与と取り消し、トークンの発行という役割を担います。ID プロバイダーと呼ばれることもあります。ユーザーの情報とそのアクセス、そしてフロー内の当事者間の信頼関係に関するすべてのことは、承認サーバーによって安全に処理されます。
- **リソース所有者**とは通常、エンド ユーザーを指します。データを所有し、そのデータ (またはリソース) へのアクセスをサード パーティに許可する権限を持つ当事者です。
- **OAuth クライアント**は、皆さんが開発するアプリです。対応するアプリケーション ID で識別されます。通常はこの当事者がエンド ユーザーと情報をやり取りし、承認サーバーにトークンを要求します。クライアントには、リソース所有者がリソースへのアクセス権を付与する必要があります。
- **リソース サーバー**は、リソースまたはデータが存在する場所です。承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。リソースへのアクセスを許可するためにベアラー トークン (access\_tokens) が使用されます。

## ポリシー

おそらく、Azure AD B2C の**ポリシー**は、サービスの最も重要な機能です。Azure AD B2C は、ポリシーを導入することによって、標準の OAuth 2.0 や OpenID Connect プロトコルを拡張します。ポリシーにより、Azure AD B2C は単なる認証および承認以外の多くの機能を実行できます。ポリシーでは、サインアップ、サインイン、プロファイルの編集など、コンシューマーの ID エクスペリエンスが完全に記述されています。ポリシーは、管理 UI で定義し、HTTP 認証要求の特別なクエリ パラメーターを使用して実行できます。ポリシーは、OAuth 2.0 および OpenID Connect の標準機能ではないので、理解するために時間をかける必要があります。詳細については、「[Azure AD B2C ポリシー リファレンス ガイド](active-directory-b2c-reference-policies.md)」をご覧ください。


## Tokens
Azure AD B2C での OAuth 2.0 および OpenID Connect の実装では、ベアラー トークンが広範囲に使われています (JWT として表現されたベアラー トークンなど)。ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。この意味で、"ベアラー" はトークンを提示できる任意の利用者を表します。利用者がベアラー トークンを受信するには、まず Azure AD による認証が必要となりますが、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがないため、トランスポート層セキュリティ (HTTPS) などのセキュリティで保護されたチャネルで転送する必要があります。ベアラー トークンが暗号化されずに転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。ベアラー トークンのセキュリティに関する考慮事項の詳細については、[RFC 6750 セクション 5](http://tools.ietf.org/html/rfc6750) をご覧ください。

Azure AD B2C で使われている各種トークンの詳細については、[Azure AD のトークン リファレンス](active-directory-b2c-reference-tokens.md)をご覧ください。

## プロトコル

要求の例を理解できる状態になったら、最初に以下のチュートリアルのどれかをご覧ください。いずれも、特定の認証シナリオに対応しています。どのフローを見ればよいかわからない場合は、[Azure AD B2C で作成できるアプリの種類](active-directory-b2c-apps.md)を確認してください。

- [OAuth 2.0 でモバイル アプリケーションおよびネイティブ アプリケーションを作成する](active-directory-b2c-reference-oauth-code.md)
- [Open ID Connect で Web Apps を作成する](active-directory-b2c-reference-oidc.md)
- OAuth 2.0 Implicit Flow で単一ページのアプリを作成する (近日公開予定)
- OAuth 2.0 Client Credentials Flow でデーモンまたはサーバー側プロセスを作成する (近日公開予定)
- OAuth 2.0 Resource Owner Password Credentials Flow でユーザー名とパスワードを使用してトークンを取得する (近日公開予定)
- OAuth 2.0 On Behalf Of Flow を使用して Web API でトークンを取得する (近日公開予定)

<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=AcomDC_0128_2016-->