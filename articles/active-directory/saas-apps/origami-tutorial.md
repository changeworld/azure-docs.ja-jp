---
title: 'チュートリアル: Azure Active Directory と Origami の統合 | Microsoft Docs'
description: Azure Active Directory と Origami の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: afdb590b29884acc5a768ee2bbf0da79a5f3cb1b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287622"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>チュートリアル: Azure Active Directory と Origami の統合

このチュートリアルでは、Origami と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Origami を統合すると、次のことが可能になります。

* Origami にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Origami に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Origami でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Origami では、**SP** によって開始される SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-origami-from-the-gallery"></a>ギャラリーからの Origami の追加

Azure AD への Origami の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Origami を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Origami**」と入力します。
1. 結果のパネルから **[Origami]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-origami"></a>Origami の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Origami に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Origami の関連ユーザーとの間にリンク関係を確立する必要があります。

Origami に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Origami SSO の構成](#configure-origami-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Origami のテスト ユーザーの作成](#create-origami-test-user)** - Origami で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Origami** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://live.origamirisk.com/origami/account/login?account=<COMPANY_NAME>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Origami クライアント サポート チーム](https://wordpress.org/support/theme/origami)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Origami の設定]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に Origami へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Origami]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-origami-sso"></a>Origami SSO の構成

1. 管理者権限で Origami アカウントにログインします。

2. 上部のメニューで **[Admin]** をクリックします。
   
    ![Origami ホーム ページのスクリーンショット。[Admin]\(管理者\) が選択されています。](./media/origami-tutorial/admin.png)

3. [Single Sign On Setup] ダイアログ ページで、次の手順に従います。
   
    ![[Single Sign On Setup]\(シングル サインオンの設定\) ページのスクリーンショット。[Enable Single Sign-on]\(シングル サインオンを有効にする\) が選択され、テキスト ボックスが強調表示されています。](./media/origami-tutorial/configuration.png)

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[Identity Provider's Sign-in Page URL]\(ID プロバイダー サインイン ページ URL\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[Identity Provider's Sign-out Page URL (ID プロバイダー シングル サインアウト ページ URL)]** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[Browse (参照する)]** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。

    e. **[変更を保存]** をクリックします。

### <a name="create-origami-test-user"></a>Origami テスト ユーザーの作成

このセクションでは、Origami で Britta Simon というユーザーを作成します。 

1. 管理者権限で Origami アカウントにログインします。

2. 上部のメニューで **[Admin]** をクリックします。
   
    ![Origami アカウント ホーム ページのスクリーンショット。[Admin]\(管理者\) が選択されています。](./media/origami-tutorial/admin.png)

3. **[Users and Security]** ダイアログ ボックスで、 **[Users]** をクリックします。
   
    ![[Users and Security]\(ユーザーとセキュリティ\) ダイアログのスクリーンショット。[ユーザー] が選択されています。](./media/origami-tutorial/user.png)

4. **[新しいユーザーの追加]** をクリックします。
   
    ![[新規ユーザーの追加] ボタンが選択されている画面のスクリーンショット。](./media/origami-tutorial/add-user.png)

5. [新規ユーザーの追加] ダイアログで、次の手順を実行します。
   
    ![[新規ユーザーの追加] ダイアログのスクリーンショット。[ユーザー名]、[名]、[姓] テキスト ボックスが強調表示されています。](./media/origami-tutorial/new-user.png)

    a. **[ユーザー名]** テキスト ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    b. **[パスワード]** ボックスに、パスワードを入力します。

    c. **[パスワードの確認]** ボックスに、パスワードを再度入力します。

    d. **[名]** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    e. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    f. **[保存]** をクリックします。
   
    ![[保存] ボタンが選択されている画面のスクリーンショット。](./media/origami-tutorial/save.png)

6. **ユーザー ロール** と **クライアント アクセス** をユーザーに割り当てます。 
   
    ![Configure single sign-on](./media/origami-tutorial/user-roles.png)

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Origami のサインオン URL にリダイレクトされます。 

* Origami のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Origami] タイルをクリックすると、Origami のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Origami を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
