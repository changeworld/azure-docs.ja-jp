---
title: 'チュートリアル: Azure Active Directory と LockPath Keylight の統合 | Microsoft Docs'
description: Azure Active Directory と LockPath Keylight の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/11/2021
ms.author: jeedes
ms.openlocfilehash: abaa441410f5cbccad3374fc75a074a7678e0d8f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294782"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>チュートリアル: Azure Active Directory と LockPath Keylight の統合

このチュートリアルでは、LockPath Keylight と Azure Active Directory (Azure AD) を統合する方法について説明します。 LockPath Keylight と Azure AD を統合すると、次のことができるようになります。

* LockPath Keylight にアクセスできるユーザーを Azure AD ユーザーで制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LockPath Keylight に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

LockPath Keylight と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LockPath Keylight シングル サインオン対応のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LockPath Keylight では、**SP** Initiated SSO がサポートされています。
* LockPath Keylight では、**Just-In-Time** ユーザー プロビジョニングがサポートされています。

## <a name="add-lockpath-keylight-from-the-gallery"></a>ギャラリーからの LockPath Keylight の追加

Azure AD への LockPath Keylight の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LockPath Keylight を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LockPath Keylight**」と入力します。
1. 結果パネルで **[LockPath Keylight]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-lockpath-keylight"></a>LockPath Keylight の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、LockPath Keylight を使用した Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと LockPath Keylight の関連ユーザーとの間にリンク関係を確立する必要があります。

LockPath Keylight を使用した Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LockPath Keylight SSO の構成](#configure-lockpath-keylight-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[LockPath Keylight テスト ユーザーの作成](#create-lockpath-keylight-test-user)** - LockPath Keylight で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **LockPath Keylight** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<COMPANY_NAME>.keylightgrc.com`

    b. **[応答 URL]** ボックスに、`https://<COMPANY_NAME>.keylightgrc.com/Login.aspx` のパターンを使用して URL を入力します。

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<COMPANY_NAME>.keylightgrc.com/`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 これらの値を取得するには、[LockPath Keylight クライアント サポート チーム](https://www.lockpath.com/contact/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (未加工)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[LockPath Keylight のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に LockPath Keylight へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LockPath Keylight]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-lockpath-keylight-sso"></a>LockPath Keylight SSO の構成

1. LockPath Keylight で SSO を有効にするには、次の手順に従います。

    a. 管理者として LockPath Keylight アカウントにサインオンします。

    b. 上部のメニューで **[人]** をクリックして **[Keylight セットアップ]** を選択します。

    !["歯車" アイコンが選択され、ドロップダウンの [Keylight Setup]\(Keylight セットアップ\) が選択されているスクリーンショット。](./media/keylight-tutorial/setup-icon.png)

    c. 左側のツリー ビューで **[SAML]** をクリックします。

    ![ツリー ビューで [SAML] が選択されているスクリーンショット。](./media/keylight-tutorial/treeview.png)

    d. **[SAML 設定]** ダイアログで **[編集]** をクリックします。

    ![[Edit]\(編集\) ボタンが選択されている [SAML Settings]\(SAML の設定\) ウィンドウを示すスクリーンショット。](./media/keylight-tutorial/edit-icon.png)

1. **[SAML 設定の編集]** ダイアログ ページで、次の手順を実行します。

    ![Configure single sign-on](./media/keylight-tutorial/settings.png)

    a. **[SAML 認証]** を **[アクティブ]** に設定します。

    b. **[ID プロバイダーのログイン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[ID プロバイダーのログアウト URL]** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[Choose File]\(ファイルの選択\)** をクリックし、ダウンロードした LockPath Keylight 証明書を選択して、 **[Open]\(開く\)** をクリックして証明書をアップロードします。

    e. **[SAML ユーザー ID の場所]** を **[Subject ステートメントの NameIdentifier 要素]** に設定します。

    f. **Keylight サービス プロバイダー** を `https://<CompanyName>.keylightgrc.com` の形式で指定します。

    g. **[ユーザーの自動プロビジョニング]** を **[アクティブ]** に設定します。

    h. **[アカウント タイプの自動プロビジョニング]** を **[すべてのユーザー]** に設定します。

    i. **[Auto-provision security role]\(セキュリティ ロールの自動プロビジョニング\)** を設定し、 **[Standard User with SAML]\(SAML を使用する標準ユーザー\)** を選択します。

    j. **[Auto-provision security config]\(セキュリティ構成の自動プロビジョニング\)** を設定し、 **[Standard User Configuration]\(標準ユーザー構成\)** を選択します。

    k. **[Email Attribute]\(電子メール属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    l. **[First name attribute]\(名属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    m. **[Last name attribute]\(姓属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。

    n. **[保存]** をクリックします。

### <a name="create-lockpath-keylight-test-user"></a>LockPath Keylight テスト ユーザーを作成する

このセクションでは、LockPath Keylight で Britta Simon というユーザーを作成します。 LockPath Keylight では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 LockPath Keylight にユーザーがまだ存在していない場合は、認証後に新規に作成されます。 ユーザーを手動で作成する必要がある場合は、[LockPath Keylight クライアント サポート チーム](https://www.lockpath.com/contact/)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる LockPath Keylight のサインオン URL にリダイレクトされます。 

* LockPath Keylight のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [LockPath Keylight] タイルをクリックすると、LockPath Keylight のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

LockPath Keylight を構成した後、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
