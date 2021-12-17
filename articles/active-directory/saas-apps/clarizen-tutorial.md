---
title: 'チュートリアル: Azure Active Directory と Clarizen One の統合 | Microsoft Docs'
description: Azure Active Directory と Clarizen One の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ff7953abac61f08f6d609de5722db2bd7f6bc5b6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321697"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>チュートリアル: Azure Active Directory と Clarizen One の統合

このチュートリアルでは、Clarizen One と Azure Active Directory (Azure AD) を統合する方法について説明します。 Clarizen One と Azure AD を統合すると、次のことができます。

* Clarizen One にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Clarizen One に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Clarizen One でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Clarizen One では、**IDP** Initiated SSO がサポートされます。
* Clarizen One では、[**自動化された** ユーザー プロビジョニングとプロビジョニング解除](clarizen-one-provisioning-tutorial.md) (推奨) がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-clarizen-one-from-the-gallery"></a>ギャラリーからの Clarizen One の追加

Azure AD への Clarizen One の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Clarizen One を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Clarizen One**」と入力します。
1. 結果のパネルから **[Clarizen One]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Clarizen One の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Clarizen One に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Clarizen One の関連ユーザーとの間にリンク関係を確立する必要があります。

Clarizen One に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Clarizen One の SSO の構成](#configure-clarizen-one-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Clarizen One のテスト ユーザーの作成](#create-clarizen-one-test-user)** - Clarizen One で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Clarizen One** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    a. **[識別子]** ボックスに、値として「`Clarizen`」を入力します。

    b. **[応答 URL]** ボックスに、URL として「`https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`」と入力します。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Clarizen One のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Clarizen One へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションのリストで **[Clarizen One]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-clarizen-one-sso"></a>Clarizen One の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Clarizen One 企業サイトに管理者としてサインインします。

1. 自分のユーザー名をクリックし、**[Settings (設定)]** をクリックします。

    ![ユーザー名の下にある [設定] のクリック](./media/clarizen-tutorial/setting.png "設定")

1. **[Global Settings (グローバル設定)]** タブをクリックします。次に、 **[Federated Authentication (フェデレーション認証)]** の横の **[edit (編集)]** をクリックします。

    ![[グローバル設定] タブ](./media/clarizen-tutorial/authentication.png "グローバル設定")

1. **[Federated Authentication (フェデレーション認証)]** ダイアログ ボックスで、次の手順を実行します。

    ![[フェデレーション認証] ダイアログ ボックス](./media/clarizen-tutorial/federated-authentication.png "[フェデレーション認証]")

    a. **[Enable Federated Authentication (フェデレーション認証を有効にする)]** をオンにします。

    b. [**Upload**] をクリックして、ダウンロードした証明書をアップロードします。

    c. **[Sign-in URL]\(サインイン URL\)** ボックスに、Azure AD アプリケーション構成ウィンドウの **[ログイン URL]** の値を入力します。

    d. **[Sign-out URL]\(サインアウト URL\)** ボックスに、Azure AD アプリケーション構成ウィンドウの **[ログアウト URL]** の値を入力します。

    e. [**Use POST**] を選択します。

    f. **[保存]** をクリックします。

### <a name="create-clarizen-one-test-user"></a>Clarizen One のテスト ユーザーの作成

このセクションの目的は、Clarizen One で Britta Simon というユーザーを作成することです。

**ユーザーを手動で作成する必要がある場合、次の手順を実行します。**

Azure AD ユーザーが Clarizen One にサインインできるようにするには、ユーザー アカウントをプロビジョニングする必要があります。 Clarizen One の場合、プロビジョニングは手動で行います。

1. Clarizen One 企業サイトに管理者としてサインインします。

2. **[People]\(ユーザー\)** をクリックします。

    ![[ユーザー] のクリック](./media/clarizen-tutorial/people.png "ユーザー")

3. [**Invite User**] をクリックします。

    ![[ユーザーの招待] ボタン](./media/clarizen-tutorial/user.png "ユーザーの招待")

1. **[Invite People (ユーザーの招待)]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザーの招待] ダイアログ ボックス](./media/clarizen-tutorial/invite-people.png "[ユーザーの招待]")

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    b. **[招待]** をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Clarizen One に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Clarizen One] タイルをクリックすると、SSO を設定した Clarizen One に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Clarizen One を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
