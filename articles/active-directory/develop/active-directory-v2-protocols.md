---
title: Azure AD v2.0 でサポートされる認証プロトコルについて | Microsoft Docs
description: Azure AD v2.0 エンドポイントでサポートされるプロトコルについて説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 2d0530e2d7b97c4b3fda84f796de1a0e2857e2ac
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872001"
---
# <a name="v20-protocols---oauth-20-and-openid-connect"></a>v2.0 プロトコル - OAuth 2.0 と OpenID Connect

v2.0 エンドポイントは、業界標準のプロトコルである OpenID Connect と OAuth 2.0 を使用し、Azure Active Directory (Azure AD) を Identity-as-a-Service (サービスとしての ID) として使用することできます。 このサービスは標準に準拠していますが、これらのプロトコルには、実装によって微妙な違いが存在する場合があります。 ここでは、Microsoft のオープンソース ライブラリを使うのではなく、コードから直接 HTTP 要求を送信して処理するか、サード パーティの[オープンソース ライブラリ](reference-v2-libraries.md)を使用する場合に役立つ情報を紹介します。

> [!NOTE]
> Azure AD のシナリオと機能のすべてが、v2.0 エンドポイントでサポートされるわけではありません。 v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。

## <a name="the-basics"></a>基本

OAuth 2.0 と OpenID Connect におけるフローはほぼすべて、情報のやり取りに 4 つの当事者が関係します。

![OAuth 2.0 Roles](../../media/active-directory-v2-flows/protocols_roles.png)

* **承認サーバー**は、v2.0 エンドポイントとして、ユーザーの本人性確認、リソースへのアクセス権の付与と取り消し、トークンの発行という役割を担います。 承認サーバーは、ID プロバイダーと呼ばれることもあります。ユーザーの情報とそのアクセス、そしてフロー内の当事者間の信頼関係に関するすべてのことは、承認サーバーによって安全に処理されます。
* **リソース所有者** とは通常、エンド ユーザーを指します。 データを所有し、そのデータ (またはリソース) へのアクセスをサード パーティに許可する権限を持つ当事者です。
* **OAuth クライアント**は、皆さんが開発するアプリです。対応するアプリケーション ID で識別されます。 通常は、OAuth クライアントがエンド ユーザーと情報をやり取りし、承認サーバーにトークンを要求します。 クライアントには、リソース所有者がリソースへのアクセス権を付与する必要があります。
* **リソース サーバー**は、リソースまたはデータが存在する場所です。 承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。リソースへのアクセスを許可するためにベアラー トークン (access_tokens) が使用されます。

## <a name="app-registration"></a>アプリケーションの登録
v2.0 エンドポイントを使うアプリはすべて、[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)、または [Azure portal](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) の新しい**アプリの登録 (プレビュー)** エクスペリエンスを通じて登録する必要があります。登録後、OAuth または OpenID Connect を使用して対話できるようになります。 アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

* アプリを一意に識別する**アプリケーション ID**。
* **リダイレクト URI** または**パッケージ識別子** (アプリに戻す応答をリダイレクトする際に使用)。
* その他シナリオに応じた値。

詳細については、 [アプリの登録](quickstart-v2-register-an-app.md)方法を参照してください。

## <a name="endpoints"></a>エンドポイント

登録済みのアプリは、v2.0 エンドポイントに要求を送ることによって、Azure AD と通信を行います。

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

ここで、 `{tenant}` は次の 4 つの値のいずれかになります。

| 値 | 説明 |
| --- | --- |
| `common` | 個人の Microsoft アカウントと Azure AD の職場/学校アカウントのどちらでもアプリケーションにサインインできます。 |
| `organizations` | Azure AD の職場/学校アカウントを持つユーザーのみがアプリケーションにサインインできます。 |
| `consumers` | 個人の Microsoft アカウント (MSA) を持つユーザーのみがアプリケーションにサインインできます。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` または `contoso.onmicrosoft.com` | 特定の Azure AD テナントの職場/学校アカウントを持つユーザーのみがアプリケーションにサインインできます。 Azure AD テナントのフレンドリ ドメイン名か、テナントの GUID 識別子のいずれかを使用できます。 |

これらのエンドポイントと対話する方法について学習するには、「[プロトコル](#protocols)」セクションで特定のアプリの種類を選択し、リンクから詳細情報にアクセスしてください。

## <a name="tokens"></a>トークン

OAuth 2.0 および OpenID Connect の v2.0 実装では、ベアラー トークンが広範囲に使われています (JWT として表現されたベアラー トークンなど)。 ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。 この意味で、"ベアラー" はトークンを提示できる任意の利用者を表します。 利用者がベアラー トークンを受信するには、まず Azure AD による認証が必要となりますが、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。 一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがないため、トランスポート層セキュリティ (HTTPS) などのセキュリティで保護されたチャネルで転送する必要があります。 ベアラー トークンが暗号化されずに転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。 後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。 アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。 ベアラー トークンのセキュリティに関する考慮事項の詳細については、 [RFC 6750 セクション 5](https://tools.ietf.org/html/rfc6750)をご覧ください。

v2.0 エンドポイントで使われている各種トークンの詳細については、 [v2.0 エンドポイント トークン リファレンス](v2-id-and-access-tokens.md)を参照してください。

## <a name="protocols"></a>プロトコル

要求の例を理解できる状態になったら、最初に以下のチュートリアルのどれかをご覧ください。 いずれも、特定の認証シナリオに対応しています。 どのフローを見ればよいかわからない場合は、 [v2.0 で作成できるアプリの種類](v2-app-types.md)を確認してください。

* [OAuth 2.0 でモバイル アプリケーションおよびネイティブ アプリケーションを作成する](v2-oauth2-auth-code-flow.md)
* [OpenID Connect で Web アプリを作成する](v2-protocols-oidc.md)
* [OAuth 2.0 暗黙的フローでシングルページ アプリを作成する](v2-oauth2-implicit-grant-flow.md)
* [OAuth 2.0 クライアント資格情報フローでデーモンまたはサーバー側プロセスを作成する](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 の On-Behalf-Of フローを使用して Web API でトークンを取得する](v2-oauth2-on-behalf-of-flow.md)
