---
title: セッションの動作を構成する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でセッションの動作を構成する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961036"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でセッションの動作を構成する

Azure Active Directory B2C (Azure AD B2C) で[シングル サインオン (SSO) セッション](session-overview.md)管理を使用すると、管理者は、ユーザーが認証された後にそのユーザーとの対話を制御できます。 たとえば、管理者は、選択した ID プロバイダーを表示するかどうか、アカウントの詳細を再入力する必要があるかどうかを制御できます。 この記事では、Azure AD B2C の SSO 設定を構成する方法について説明します。

## <a name="session-behavior-properties"></a>セッションの動作のプロパティ

次のプロパティを使用して、Web アプリケーション セッションを管理できます。

- **[Web アプリのセッションの有効期間 (分)]** - 認証の成功時にユーザーのブラウザーに格納される Azure AD B2C のセッション Cookie の有効期間。
    - 既定値 = 1,440 分。
    - 最小値 (この値を含む) = 15 分。
    - 最大値 (この値を含む) = 1,440 分。
- **[Web アプリのセッション タイムアウト]** - [セッションの有効期限の種類](session-overview.md#session-expiry-type)、 *[ローリング]* または *[絶対]* 。 
- **[シングル サインオン構成]** - Azure AD B2C テナント内の複数のアプリとユーザー フローにまたがるシングル サインオン (SSO) の動作の[セッション スコープ](session-overview.md#session-scope)。


## <a name="configure-the-properties"></a>プロパティを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー]** を選択します。
5. あらかじめ作成しておいたユーザー フローを開きます。
6. **[プロパティ]** を選択します。
7. **[Web アプリのセッションの有効期間 (分)]** 、 **[Web アプリのセッション タイムアウト]** 、 **[シングル サインオン構成]** 、 **[ログアウト要求に ID トークンが必要]** を必要に応じて構成します。

    ![Azure portal のセッション動作プロパティの設定](./media/session-behavior/session-behavior.png)

8. **[保存]** をクリックします。

## <a name="configure-sign-out-behavior"></a>サインアウト動作の構成

### <a name="secure-your-logout-redirect"></a>ログアウトのリダイレクトをセキュリティで保護する

ログアウト後、ユーザーは、アプリケーションに対して指定されている応答 URL に関係なく、`post_logout_redirect_uri` パラメーターに指定された URI にリダイレクトされます。 ただし、有効な `id_token_hint` が渡され、 **[ログアウト要求に ID トークンが必要]** が有効になっている場合、Azure AD B2C では、リダイレクトの実行前に、`post_logout_redirect_uri` の値がいずれかのアプリケーションの構成済みのリダイレクト URI と一致するかどうかが検証されます。 一致する応答 URL がアプリケーションで構成されていない場合は、エラー メッセージが表示され、ユーザーはリダイレクトされません。 ログアウト要求で ID トークンを求めるようにするには、以下の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. あらかじめ作成しておいたユーザー フローを開きます。
1. **[プロパティ]** を選択します。
1. **[ログアウト要求に ID トークンが必要]** を有効にします。
1. **[Azure AD B2C]** に戻ります。
1. **[アプリの登録]** を選択してから、お客様のアプリケーションを選択します。
1. **[認証]** を選択します。
1. **[ログアウト URL]** テキスト ボックスに、ログアウト後のリダイレクト URI を入力し、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C セッション](session-overview.md)の詳細について学習します。
