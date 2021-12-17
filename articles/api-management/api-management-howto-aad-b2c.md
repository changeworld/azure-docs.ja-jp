---
title: Azure Active Directory B2C を使用して開発者アカウントを承認する
titleSuffix: Azure API Management
description: Azure Active Directory B2C を使用して、Azure API Management の開発者ポータルのユーザーを認可する方法を説明します
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 09/28/2021
ms.author: danlep
ms.openlocfilehash: e286cbb64078a2568d2aa3d0c523d782445470d4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049142"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Azure API Management で Azure Active Directory B2C を使用して開発者アカウントを承認する方法


Azure Active Directory B2C は、コンシューマー向け Web アプリケーションおよびモバイル アプリケーション用のクラウド ID 管理ソリューションです。 これを使用して API Management 開発者ポータルへのアクセスを管理できます。 

このチュートリアルでは、Azure Active Directory B2C との統合のために API Management サービスで必要な構成について説明します。 この記事で後述するように、非推奨のレガシ開発者ポータルを使用している場合は、手順の一部が異なります。

従来の Azure Active Directory を使用して開発者ポータルへのアクセスを有効にする方法については、[Azure Active Directory を使用して開発者アカウントを承認する方法](api-management-howto-aad.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* アプリケーションを作成する Azure Active Directory B2C テナント。 詳細については、[Azure Active Directory B2C の概要](../active-directory-b2c/overview.md)に関する記事をご覧ください。
* API Management インスタンス。 お持ちでない場合は、[Azure API Management インスタンスを作成](get-started-create-service-instance.md)します。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="configure-sign-up-and-sign-in-user-flow"></a>サインアップとサインインのユーザー フローを設定する

このセクションでは、サインアップとサインイン ポリシーの両方が存在する Azure Active Directory B2C テナント内で、ユーザー フローを作成します。 詳しい手順は、[Azure Active Directory B2C でユーザー フローとカスタム ポリシーを作成する方法](../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-us)に関する記事をご覧ください。

1. [Azure portal](https://portal.azure.com) で、Azure Active Directory B2C テナントにアクセスします。
1. **[Policies]\(ポリシー\)** で **[User flows]\(ユーザー フロー\)**  >  **[+ New user flow]\(+ 新しいユーザー フロー\)** を順にクリックします。
1. **[ユーザー フローを作成する]** ページで、 **[サインアップとサインイン]** ユーザー フローを選択します。
1. 次の情報を指定します。
    1. ユーザー フローの一意の名前を入力します。
    1. **[Identity providers]\(ID プロバイダー\)** で **[Email signup]\(メール サインアップ\)** をクリックします。
    1. **[User attributes and token claims]\(ユーザーの属性とトークンのクレーム\)** で、API Management 開発者ポータルに必要な属性とクレームを選択します (レガシ開発者ポータルでは不要です)。
         ![アプリケーション要求](./media/api-management-howto-aad-b2c/api-management-application-claims.png)
        * **Attributes** (属性): Given Name (下の名前)、Surname (名字)
        * **Claims** (クレーム): Email Addresses (メール アドレス)、Given Name (下の名前)、Surname (名字)、User’s Object ID (ユーザーの オブジェクト ID)
1. **［作成］** を選択します

## <a name="configure-identity-provider-for-developer-portal"></a>開発者ポータルの ID プロバイダーを設定する

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[Developer portal]\(開発者ポータル\)** で **[Identities]\(ID\)**  >  **[+ Add]\(+ 追加\)** の順にクリックします。
1. **[Add identity provider]\(ID プロバイダーの追加\)** ページで **[Azure Active Directory B2C]** をクリックします。
1. **[Add identity provider]\(ID プロバイダーの追加\)** ウィンドウで、 **[Redirect URL]\(リダイレクト URL\)** をコピーします。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-identity-provider-redirect-url.png" alt-text="リダイレクト URL をコピーする":::    

1. ブラウザーで、Azure portal の Azure Active Directory B2C テナントのタブに戻ります。 **[App registrations]\(アプリの登録\)**  >   **[+ New registration]\(+ 新しい登録\)** を順にクリックします。
1. **[Register an application]\(アプリケーションの登録\)** ページでアプリケーションの登録情報を入力します。
    * **[Name]\(名前\)** セクションで選んだアプリの名前を入力します。
    * **[Supported account types]\(サポートするアカウントの種類\)** セクションで、各自の使用条件に合うアカウントの種類を選択します。 幅広い顧客を対象にするには、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]/(あらゆる ID プロバイダーおよび組織のディレクトリのアカウント \(ユーザー フローでのユーザー認証用\)\)** を選択します。 詳細については、[アプリケーションの登録](../active-directory/develop/quickstart-register-app.md#register-an-application)に関するトピックを参照してください。
    * **[Redirect URI]\(リダイレクト URI\)** で API Management インスタンスからコピーしたリダイレクト URI を入力します。
    * **[Permissions]\(権限\)** で **[Grant admin consent to openid and offline_access permissions]\(openid および offline_access 権限に管理者の同意を与える\)** を選択します。
    * **[登録]** を選択して、アプリケーションを作成します。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-registration.png" alt-text="新しいアプリケーションの登録":::

1. アプリの **[Overview]\(概要\)** ページで **[Application (client) ID]\(アプリケーション \(クライアント\) ID\)** を見つけて、その値をクリップボードにコピーします。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-id.png" alt-text="アプリケーション ID":::
1. API Management の **[Add identity provider]\(ID プロバイダーの追加\)** ページに戻り、ID を **[Client Id]\(クライアント ID\)** 記入欄に貼り付けます。
1. 登録した B2C アプリの画面に戻ります。 **[Certificates & secrets]\(証明書 & シークレット\)**  >  **[+ New client secret]\(+ 新しいクライアント シークレット\)** の順にクリックします。 
    :::image type="content" source="media/api-management-howto-aad-b2c/generate-app-key.png" alt-text="クライアント シークレットを作成する"::: 
   * **[Add a client secret]\(クライアント シークレットの追加\)** ページで **[Description]\(説明\)** を入力して **[Add]\(追加\)** をクリックします。
   * キーを安全な場所に記録します。 このページからの移動後は、このシークレットの値は "二度と表示されません"。
1. API Management の **[Add identity provider]\(ID プロバイダーの追加\)** ページに戻り、 **[Client secret]\(クライアント シークレット\)** の入力欄にキーを貼り付けます。
1. 登録した B2C アプリの画面に戻ります。 左側のメニューの **[管理]** セクションで、 **[認証]** を選択します。
    * **[Implicit grant and hybrid flows]\(暗黙的な許可およびハイブリッド フロー\)** で、 **[アクセス トークン]** と **[ID トークン]** の両方のチェック ボックスをオンにします。
    * **[保存]** を選択します。
1. API Management の **[Add identity provider]\(ID プロバイダーの追加\)** ページに戻ります。
    * **[Signin tenant]\(テナントへのサインイン\)** で、Azure Active Directory B2C テナントのドメイン名を指定します。
    * **[Authority]\(拠点\)** フィールドでは、使用する Azure Active Directory B2C ログイン URL を指定できます。 値を **< your_b2c_tenant_name >. b2clogin.com** に設定します。
    * B2C テナント ポリシーの **Signup Policy (サインアップ ポリシー)** と **Signin Policy (サインイン ポリシー)** を対応する欄に入力します。
    * **Profile Editing Policy (プロファイル編集ポリシー)** と **Password Reset Policy (パスワード リセット ポリシー)** も、必要ならば指定します。

         :::image type="content" source="media/api-management-howto-aad-b2c/add-identity-provider.png" alt-text="Active Directory B2C ID プロバイダー構成":::
1. 必要な構成内容を入力したら **[Add]\(追加\)** をクリックします。

変更が保存されると、開発者は新しいアカウントを作成し、Azure Active Directory B2C を使用して開発者ポータルにサインインできるようになります。

## <a name="developer-portal---add-azure-active-directory-b2c-account-authentication"></a>開発者ポータル - Azure Active Directory B2C アカウント認証の追加

> [!IMPORTANT]
> Azure Active Directory B2C の構成設定を作成したとき、または変更を有効にするためにこれを更新したときは、[開発者ポータルを再公開する](api-management-howto-developer-portal-customize.md#publish)必要があります。

開発者ポータルでは、**Sign-in button: OAuth (サインイン ボタン: OAuth)** ウィジェットにより、Azure Active Directory B2C を利用したサインインができます。 このウィジェットは、既定の開発者ポータル コンテンツのサインイン ページに既に含まれています。

1. Azure Active Directory B2C を使用してサインインするには、 新しいブラウザー ウィンドウを開いて開発者ポータルに移動します。 **[サインイン]** をクリックします。

1. **[Sign in]\(サインイン\)** ページで **[Azure Active Directory B2C]** をクリックします。

    :::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-sign-in.png" alt-text="開発者ポータルにサインインする":::
1. 前のセクションで構成したサインアップ ポリシーにリダイレクトされます。 Active Directory B2C テナントのメール アドレスを使用してサインアップします。

サインアップが完了すると、開発者ポータルにリダイレクトされます。 これで、API Management サービス インスタンスの開発者ポータルにサインインしました。

:::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-home.png" alt-text="開発者ポータルへのサインインを完了する":::

新しいユーザーが Azure Active Directory B2C を使用してサインインするたびに新しいアカウントが自動的に作成される仕組みになってはいますが、同じウィジェットをサインアップ ページに追加することも検討できます。

**サインアップ フォーム:OAuth** ウィジェットでは、OAuth によるサインアップに使用されるフォームが示されます。

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-active-directory-b2c"></a>従来の開発者ポータル - Azure Active Directory B2C を使用してサインアップする方法

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

> [!NOTE]
> B2C をレガシ開発者ポータルと適切に統合するには、Azure Active Directory B2C を使用して開発者アカウントにサインアップまたはサインインする前の [パスワードのリセット](../active-directory-b2c/add-password-reset-policy.md)の有効化と組み合わせて **standard v1** のユーザー フローを使用します。 

1. 新しいブラウザー ウィンドウを開き、レガシ開発者ポータルにアクセスします。 **[サインアップ]** ボタンをクリックします。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal.png" alt-text="レガシ開発者ポータルにサインアップする":::

1. **Azure Active Directory B2C** によるサインアップを選択します。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-button.png" alt-text="Azure Active Directory B2C でサインアップする":::

1. 前のセクションで構成したサインアップ ポリシーにリダイレクトされます。 サインアップの方法として、電子メール アドレスか既存のソーシャル アカウントを選択します。

   > [!NOTE]
   > Azure Active Directory B2C が Azure portal の **[id]** タブで有効になっている唯一のオプションである場合は、サインアップポリシーに直接リダイレクトされます。

   :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-options.png" alt-text="レガシ開発者ポータルのサインアップ オプション":::

   サインアップが完了すると、開発者ポータルにリダイレクトされます。 これで、API Management サービス インスタンスの開発者ポータルにサインインしました。



## <a name="next-steps"></a>次のステップ

*  [Azure Active Directory B2C の概要]
*  [Azure Active Directory B2C: 拡張可能なポリシー フレームワーク]
*  [Azure Active Directory B2C で ID プロバイダーとして Microsoft アカウントを使用する]
*  [Azure Active Directory B2C で ID プロバイダーとして Google アカウントを使用する]
*  [Azure Active Directory B2C で ID プロバイダーとして LinkedIn アカウントを使用する]
*  [Azure Active Directory B2C で ID プロバイダーとして Facebook アカウントを使用する]


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Azure Active Directory B2C の概要]: ../active-directory-b2c/overview.md
[How to authorize developer accounts using Azure Active Directory]: ./api-management-howto-aad.md
[Azure Active Directory B2C: 拡張可能なポリシー フレームワーク]: ../active-directory-b2c/user-flow-overview.md
[Azure Active Directory B2C で ID プロバイダーとして Microsoft アカウントを使用する]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Azure Active Directory B2C で ID プロバイダーとして Google アカウントを使用する]: ../active-directory-b2c/identity-provider-google.md
[Azure Active Directory B2C で ID プロバイダーとして Facebook アカウントを使用する]: ../active-directory-b2c/identity-provider-facebook.md
[Azure Active Directory B2C で ID プロバイダーとして LinkedIn アカウントを使用する]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
