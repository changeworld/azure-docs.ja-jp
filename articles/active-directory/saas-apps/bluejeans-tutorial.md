---
title: 'チュートリアル: Azure Active Directory と BlueJeans の統合 | Microsoft Docs'
description: Azure Active Directory と BlueJeans の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095230"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>チュートリアル: Azure Active Directory と BlueJeans の統合

このチュートリアルでは、BlueJeans と Azure Active Directory (Azure AD) を統合する方法について説明します。

BlueJeans と Azure AD の統合には、次の利点があります。

- BlueJeans にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に BlueJeans にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

BlueJeans と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- BlueJeans でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの BlueJeans の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-bluejeans-from-the-gallery"></a>ギャラリーからの BlueJeans の追加

Azure AD への BlueJeans の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BlueJeans を追加する必要があります。

**ギャラリーから BlueJeans を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**BlueJeans**」と入力し、結果パネルで **[BlueJeans]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、BlueJeans で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する BlueJeans ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BlueJeans の関連ユーザーの間で、リンク関係が確立されている必要があります。

BlueJeans で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[BlueJeans のテスト ユーザーの作成](#creating-a-bluejeans-test-user)** - BlueJeans で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、BlueJeans アプリケーションでシングル サインオンを構成します。

**BlueJeans で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **BlueJeans** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_general_301.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[BlueJeans のドメインと URL] のシングル サインオン情報](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.BlueJeans.com` のパターンを使用して URL を入力します。

    > [!NOTE]
    > サインオン値は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[BlueJeans クライアント サポート チーム](https://support.bluejeans.com/contact)に問い合わせください。

6. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. **[Set up BlueJeans]\(BlueJeans の設定\)** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![BlueJeans の構成](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. 別の Web ブラウザー ウィンドウで、 **BlueJeans** 企業サイトに管理者としてログインします。

9. **[管理] \> [グループ設定] \> [セキュリティ]** の順にクリックします。

    ![管理](./media/bluejeans-tutorial/IC785868.png "Admin")

10. **[セキュリティ]** セクションで、次の手順を実行します。

    ![SAML シングル サインオン](./media/bluejeans-tutorial/IC785869.png "SAML Single Sign On")

    a. **[SAML シングル サインオン]** を選択します。

    b. **[自動プロビジョニングの有効化]** を選択します。

11. 次の手順を実行します。

    ![証明書パス](./media/bluejeans-tutorial/IC785870.png "Certificate Path")

    a. **[ファイルの選択]** を選択して、Azure portal からダウンロードした base 64 でエンコードされた証明書をアップロードします。

    b. **[ログイン URL]** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. **[Password Change URL]\(パスワード変更 URL\)** テキスト ボックスに、Azure portal からコピーした**パスワードの変更 URL** の値を貼り付けます。

    d. **[ログアウト URL]** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

12. 次の手順を実行します。

    ![変更を保存](./media/bluejeans-tutorial/IC785874.png "Save Changes")

    a. **[User id]** (ユーザー ID) ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    b. **[Email]** (電子メール) ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    c. **[SAVE CHANGES]\(変更の保存\)** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-bluejeans-test-user"></a>BlueJeans のテスト ユーザーの作成

このセクションの目的は、BlueJeans で Britta Simon というユーザーを作成することです。 BlueJeans では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](bluejeans-provisioning-tutorial.md)で確認できます。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. **BlueJeans** 企業サイトに管理者としてログインします。

2. **[管理] \> [ユーザーの管理] \> [ユーザーの追加]** の順にクリックします。

    ![管理](./media/bluejeans-tutorial/IC785877.png "Admin")

    >[!IMPORTANT]
    >**[ユーザーの追加]** タブは、**[セキュリティ]** タブの **[自動プロビジョニングの有効化]** がオフになっている場合にのみ使用できます。 

3. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/bluejeans-tutorial/IC785886.png "Add User")

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    c. **[Pick a BlueJeans Username]\(BlueJeans ユーザー名の選択\)** テキスト ボックスに、ユーザーのユーザー名 (**Brittasimon** など) を入力します

    d. **[パスワードの作成]** テキスト ボックスにパスワードを入力します。

    e. **[会社]** テキスト ボックスに会社名を入力します。

    f. **[電子メール アドレス]** ボックスに、ユーザーの電子メール アドレスを入力します (この例では **brittasimon@contoso.com**)。

    g. **[Create a BlueJeans Meeting I.D]\(BlueJeans 会議 I.D の作成\)** テキスト ボックスに、会議の ID を入力します。

    h. **[Pick a Moderator Passcode]\(モデレーター パスコードの選択\)** テキスト ボックスに、パスコードを入力します。

    i. **[続行]** をクリックします。

    ![ユーザーの追加](./media/bluejeans-tutorial/IC785887.png "ユーザーの追加")

    J. **[ユーザーの追加]** をクリックします。

>[!NOTE]
>BlueJeans から提供されている他の BlueJeans ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、BlueJeans へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[BlueJeans]** を選択します。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで BlueJeans のタイルをクリックすると、自動的に BlueJeans アプリケーションにサインオンします。
アクセス パネルの詳細については、「[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
