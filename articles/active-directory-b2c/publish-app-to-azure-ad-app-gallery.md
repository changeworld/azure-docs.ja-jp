---
title: Azure Active Directory B2C アプリを Azure Active Directory アプリ ギャラリーに公開する
description: シングル サインオンをサポートする Azure AD B2C アプリを Azure Active Directory アプリ ギャラリーに掲載する方法について説明します。
titleSuffix: Azure AD B2C
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 7ac57eb6e52acee3154a1970947e17e1f5947283
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035671"
---
# <a name="publish-your-azure-ad-b2c-app-to-the-azure-ad-app-gallery"></a>Azure AD B2C アプリを Azure AD アプリ ギャラリーに公開します。

Azure Active Directory (Azure AD) アプリ ギャラリーは、数千ものアプリのカタログです。 アプリ ギャラリーにより、シングル サインオン (SSO) のデプロイおよび構成と、ユーザー設定の自動化が簡単になっています。 ギャラリーでは、Workday、ServiceNow、Zoom など、人気のあるクラウド アプリを見つけることができます。

この記事では、Azure Active Directory B2C (Azure AD B2C) アプリを Azure AD アプリ ギャラリーで公開する方法について説明します。 アプリは、発行されると、顧客が自分の Azure AD テナントにアプリを追加するときに選択できるオプションの一覧内に表示されます。

以下に、アプリ ギャラリーに Azure AD B2C アプリを追加する利点の一部を示します。  

- アプリの Microsoft との統合が検証されます。
- お使いのアプリと Azure AD アプリの間で SSO アクセスが有効になります。
- 顧客はクイック検索を使用して、ギャラリーでアプリを見つけることができます。
- アプリの構成は単純かつ最小限です。
- 顧客に、手順を追った構成のチュートリアルが提供されます。
- 顧客は、組織内のさまざまなユーザーやグループにアプリを割り当てることができます。
- テナント管理者はアプリに、テナント全体の管理者の同意を付与できます。

## <a name="sign-in-flow-overview"></a>サインイン フローの概要

サインイン フローでは、次の手順が実行されます。

1. ユーザーが[マイ アプリ ポータル](https://myapps.microsoft.com/)にアクセスしてアプリを選択すると、アプリのサインイン URL が開かれます。
1. アプリのサインイン URL によって承認要求が開始され、ユーザーは Azure AD B2C 認証エンドポイントにリダイレクトされます。
1. ユーザーは Azure AD の "Corporate" アカウントでサインインすることを選択します。 彼らは Azure AD B2C によって Azure AD 認証エンドポイントに移動され、そこで、職場のアカウントを使用してサインインします。
1. Azure AD セッションがアクティブになっている場合、ユーザーに再度のサインインが求められることなく、Azure AD によってアクセス トークンが発行されます。 Azure AD セッションが有効期限切れまたは無効になった場合、ユーザーは再度サインインするように求められます。

![OpenID のサインイン接続フローの図。](./media/publish-app-to-azure-ad-app-gallery/app-gallery-sign-in-flow.png)

ユーザーの SSO セッションと Azure AD ID の設定によっては、以下のことを求めるダイアログが表示される場合があります。

- メール アドレスまたは電話番号を入力する。
- パスワードを入力するか、[Microsoft 認証アプリ](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)でサインインする。
- 多要素認証を完了する。
- 同意ページを受け入れます。 顧客のテナント管理者は、[アプリにテナント全体の管理者の同意を付与](../active-directory/manage-apps/grant-admin-consent.md)できます。 同意が付与されると、同意ページはユーザーに表示されなくなります。

サインインに成功すると、Azure AD から Azure AD B2C にトークンが返されます。 Azure AD B2C では、トークンの要求を検証して読み取ってから、アプリケーションにトークンを返します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="step-1-register-your-application-in-azure-ad-b2c"></a>ステップ 1: Azure AD B2C にアプリケーションを登録する

アプリで Azure AD B2C を使用したサインインを行えるようにするには、Azure AD B2C ディレクトリにアプリを登録します。 アプリを登録すると、アプリと Azure AD B2C の間に信頼関係が確立されます。 

[Web アプリケーションを登録](tutorial-register-applications.md)し、[ID トークンの暗黙的な許可を有効化](tutorial-register-applications.md#enable-id-token-implicit-grant)します (まだこれらを行っていない場合)。 後で、このアプリを Azure アプリ ギャラリーに登録します。

## <a name="step-2-set-up-sign-in-for-multitenant-azure-ad"></a>ステップ 2: マルチテナント Azure AD のサインインを設定する

任意の Azure AD テナントから、従業員とコンシューマーが Azure AD B2C を使用してサインインできるようにするには、[マルチ テナント Azure AD のサインインの設定](identity-provider-azure-ad-multi-tenant.md?pivots=b2c-custom-policy)に関するガイダンスに従ってください。

## <a name="step-3-prepare-your-app"></a>ステップ 3: アプリを準備する

アプリでサインイン エンドポイントの URL をコピーします。 [Web アプリケーションのサンプル](configure-authentication-sample-web-app.md)を使用する場合、サインイン URL は `https://localhost:5001/MicrosoftIdentity/Account/SignIn?` です。 この URL が、アプリにサインインするため、ユーザーが Azure AD アプリ ギャラリーによって移動される場所です。

運用環境では、アプリ登録のリダイレクト URI は一般に、`https://woodgrovedemo.com/Account/SignIn` など、パブリックにアクセスできる、アプリが実行中のエンドポイントです。 応答 URL は、`https` で始まる必要があります。

## <a name="step-4-publish-your-azure-ad-b2c-app"></a>ステップ 4: Azure AD B2C アプリを公開する

最後に、マルチテナント アプリを Azure AD アプリ ギャラリーに追加します。 「[アプリを Azure AD アプリ ギャラリーで公開する](../active-directory/develop/v2-howto-app-gallery-listing.md)」の手順に従ってください。 アプリ ギャラリーにアプリを追加するには、以下を実行します。

1. [ドキュメントを作成して公開します](../active-directory/develop/v2-howto-app-gallery-listing.md#step-5---create-and-publish-documentation)。
1. 以下の情報を使用して[アプリを送信](../active-directory/develop/v2-howto-app-gallery-listing.md#step-6---submit-your-app)します。

    |Question  |指定する必要がある回答  |
    |---------|---------|
    |送信する要求の種類を選択してください。| **[ギャラリーの一覧にアプリケーションを表示する]** を選択します。|
    |ギャラリーの一覧にアプリケーションを表示するときには、どの機能を有効にしますか? | **[フェデレーション SSO (SAML、WS-Fed と OpenID Connect)]** を選択します。 | 
    | アプリケーションのフェデレーション プロトコルを選択します| **[OpenID Connect と OAuth 2.0]** を選択します。 |
    | アプリケーション (クライアント) ID | [Azure AD B2C アプリケーション](#step-1-register-your-application-in-azure-ad-b2c)の ID を指定します。 |
    | アプリケーションのサインイン URL|「[ステップ 3. アプリを準備する](#step-3-prepare-your-app)」で構成したように、アプリのサインイン URL を指定します。|
    | マルチテナント| **[はい]** を選択します。 |
    | | |

## <a name="next-steps"></a>次のステップ

- [アプリを Azure AD アプリ ギャラリーに公開する](../active-directory/develop/v2-howto-app-gallery-listing.md)方法を確認します。
