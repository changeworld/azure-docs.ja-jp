---
title: Azure Active Directory B2C での組み込みのポリシーへの OpenID Connect ID プロバイダーの追加 | Microsoft Docs
description: OpenID Connect プロバイダーを Azure AD B2C 内の組み込みのポリシーに追加する方法についての概要ガイド。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5baf95cd2426c9753620cba7c5a67b4c1672788
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444227"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: カスタム OpenID Connect ID プロバイダーを組み込みのポリシーに追加する

>[!NOTE]
> この機能はパブリック プレビュー段階にあります。 運用環境で、この機能を使用しないでください。

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) は OAuth 2.0 を基盤として開発された認証プロトコルであり、ユーザーを安全にサインインさせるために利用されます。 Azure AD B2C では、[Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md) など、このプロトコルを使用するほとんどの ID プロバイダーがサポートされています。 この記事では、カスタム OpenID Connect ID プロバイダーを組み込みのポリシーに追加する方法について説明します。

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>カスタム OpenID Connect ID プロバイダーの構成

カスタム OpenID Connect ID プロバイダーを追加するには:

1. Azure portal 上で [Azure AD B2C の設定に移動する](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)には次の手順に従います。
1. **[ID プロバイダー]** をクリックします。
1. **[追加]** をクリックします。
1. **[ID プロバイダーの種類]** で、**[OpenID Connect]** を選択します。

### <a name="setting-up-the-openid-connect-identity-provider"></a>OpenID Connect ID プロバイダーの設定

#### <a name="metadata-url"></a>メタデータ URL

すべての OpenID Connect ID プロバイダーは仕様に従って、サインインを実行するために必要な情報の大半を含むメタデータ ドキュメントを記述します。 これには、使用する URL、サービスの公開署名キーの場所などの情報が含まれます。 OpenID Connect メタデータ ドキュメントは、常に `.well-known\openid-configuration` で終わるエンドポイントに配置されます。

追加する OpenID Connect ID プロバイダーについては、そのメタデータの URL を入力します。

#### <a name="client-id-and-secret"></a>クライアントの ID とシークレット

ユーザーのサインインを許可するために、ID プロバイダーは、開発者が自身のサービスにアプリケーションを登録することを必須にします。 このアプリケーションには、ID (**クライアント ID** と呼ばれます) および**クライアント シークレット**が付与されます。 これらの値を ID プロバイダーからコピーし、対応するフィールドに入力します。

> [!NOTE]
> クライアント シークレットはオプションです。 ただし、[承認コード フロー](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)を使用する場合は、クライアント シークレットを入力する必要があります。承認コード フローでは、トークンのコードの交換にシークレットが使用されます。

#### <a name="scope"></a>スコープ

スコープは、カスタム ID プロバイダーから収集する情報およびアクセス許可を定義します。 OpenID Connect 要求には、ID プロバイダーから ID トークンを受け取るために `openid` スコープ値を含める必要があります。 ID トークンがないと、ユーザーはカスタム ID プロバイダーを使用して Azure AD B2C にサインインすることはできません。

別のスコープを (スペースで区切って) 追加することもできます。 使用可能な他のスコープについては、カスタム ID プロバイダーのドキュメントを参照してください。

#### <a name="response-type"></a>応答の種類

応答の種類は、最初の呼び出しで、カスタム ID プロバイダーの `authorization_endpoint` に送信される情報の種類を表します。 

* `code`: [承認コード フロー](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)に従って、Azure AD B2C にコードが返されます。 次に Azure AD B2C が `token_endpoint` を呼び出して、トークンのコードを交換します。
* `token`: アクセス トークンが、カスタム ID プロバイダーから、Azure AD B2C に返されます。
* `id_token`: ID トークンが、カスタム ID プロバイダーから、Azure AD B2C に返されます。


#### <a name="response-mode"></a>応答モード

応答モードは、カスタム ID プロバイダーから Azure AD B2C へのデータの返送に使用する方法を定義します。

* `form_post`: 最高のセキュリティを得るには、この応答モードをお勧めします。 この応答は、HTTP の `POST` メソッドによって送信され、本文には、`application/x-www-form-urlencoded` 形式を使用してエンコードされたコードまたはトークンが含まれます。
* `query`: このコードまたはトークンは、クエリ パラメーターとして返されます。


#### <a name="domain-hint"></a>ドメインのヒント

ドメインのヒントを使用して、ユーザーは利用可能な ID プロバイダーのリストから選択するのではなく、指定した ID プロバイダーのサインイン ページに直接スキップできます。 このような動作を許可にするには、ドメインのヒントの値を入力します。

カスタム ID プロバイダーにジャンプするには、サインインのために Azure AD B2C を呼び出すときに、要求の最後にパラメーター `domain_hint=<domain hint value>` を追加します。


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>OpenID Connect ID プロバイダーからのクレームのマッピング

カスタム ID プロバイダーが Azure AD B2C に ID トークンを返送した後、Azure AD B2C は、受け取ったトークンからのクレームを Azure AD B2C が認識して使用するクレームにマッピングできる必要があります。 

以下の各マッピングについては、カスタム ID プロバイダーのドキュメントを参照して、ID プロバイダーのトークンに返されるクレームをご理解ください。

* `User ID`: サインインしたユーザーの一意の識別子を指定するクレームを入力します。
* `Display Name`: ユーザーの表示名またはフル ネームを指定するクレームを入力します。
* `Given Name`: ユーザーの名を指定するクレームを入力します。
* `Surname`: ユーザーの姓を指定するクレームを入力します。
* `Email`: ユーザーのメール アドレスを指定するクレームを入力します。

## <a name="next-steps"></a>次の手順

カスタム OpenID Connect ID プロバイダーを[組み込みのポリシー](active-directory-b2c-reference-policies.md)に追加します。
