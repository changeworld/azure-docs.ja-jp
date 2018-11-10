---
title: 'チュートリアル: Azure Active Directory と Zscaler Three の統合 | Microsoft Docs'
description: Azure Active Directory と Zscaler Three の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: b148967af0882993d8ab113bdf0fd3ad3835296f
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092612"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>チュートリアル: Azure Active Directory と Zscaler Three の統合

このチュートリアルでは、Zscaler Three と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zscaler Three と Azure AD の統合には、次の利点があります。

- Zscaler Three にアクセスするユーザーを Azure AD で管理できます
- ユーザーが Azure AD アカウントで Zscaler Three に自動的にサインオン (シングル サインオン) できるように設定することが可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zscaler Three と Azure AD の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- Zscaler Three でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zscaler Three の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zscaler-three-from-the-gallery"></a>ギャラリーからの Zscaler Three の追加

Azure AD への Zscaler Three の統合を構成するには、管理対象の SaaS アプリ一覧に Zscaler Three をギャラリーから追加する必要があります。

**ギャラリーから Zscaler Three を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[アプリケーション]][2]

3. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[アプリケーション]][3]

4. 結果ウィンドウで **Zscaler Three** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zscaler Three で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zscaler Three ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zscaler Three の関連ユーザーの間で、リンク関係が確立されている必要があります。

Zscaler Three で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[プロキシ設定の構成](#configuring-proxy-settings)** - Internet Explorer でプロキシ設定を構成します
3. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Zscaler Three のテスト ユーザーの作成](#creating-a-zscaler-three-test-user)** - Zscaler Three で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
6. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Zscaler Three アプリケーションでシングル サインオンを構成します。

**Zscaler Three で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Zscaler Three** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. **SAML** モードを別のモードから変更する必要がある場合は、画面上部の **[シングル サインオン モードの変更]** をクリックします。

    ![Configure single sign-on](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    [サインオン URL] ボックスに、URL (`https://login.zscalerthree.net/sfc_sso`) を入力します。

6. **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして **[証明書 (Base64)]** をダウンロードし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. **[Set up Zscaler Three]\(Zscaler Three のセットアップ\)** セクションで、**[ログイン URL]** をコピーします。

    ![Configure single sign-on](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. 別の Web ブラウザー ウィンドウで、Zscaler Three 企業サイトに管理者としてログインします。

10. 上部のメニューで **[管理]** をクリックします。

    ![Administration](./media/zscaler-three-tutorial/ic800206.png "Administration")

9. **[管理者とロールの管理]** をクリックし、**[ユーザーと認証の管理]** をクリックします。

    ![ユーザーと認証の管理](./media/zscaler-three-tutorial/ic800207.png "Manage Users & Authentication")

10. **[組織の認証オプションの選択]** セクションで、次の手順を実行します。

    ![Authentication](./media/zscaler-three-tutorial/ic800208.png "Authentication")

    a. **[SAML シングル サインオンを使用した認証]** を選択します。

    b. **[SAML シングル サインオン パラメーターの構成]** をクリックします。

11. **[SAML シングル サインオン パラメーターの構成]** ダイアログ ページで、次の手順に従い、**[完了]** をクリックします

    ![シングル サインオン](./media/zscaler-three-tutorial/ic800209.png "Single Sign-On")

    a. Azure portal からコピーした **ログイン URL** の値を、**[URL of the SAML Portal to which users are sent for authentication]\(ユーザーが認証に送られる SAML ポータルの URL\)** テキスト ボックスに貼り付けます。

    b. **[ログイン名を含む属性]** ボックスに「**NameID**」と入力します。

    c. ダウンロードした証明書をアップロードするには、 **[Zscaler pem]** をクリックします。

    d. **[SAML 自動プロビジョニングを有効にする]** を選択します。

12. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![Administration](./media/zscaler-three-tutorial/ic800210.png "Administration")

    a. **[Save]** をクリックします。

    b. **[今すぐ認証する]** をクリックします。

## <a name="configuring-proxy-settings"></a>プロキシ設定の構成

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer でプロキシ設定を構成するには

1. **Internet Explorer**を開始します。

2. **[ツール]** メニューの **[インターネット オプション]** を選択し、**[インターネット オプション]** ダイアログを開きます。

     ![インターネット オプション](./media/zscaler-three-tutorial/ic769492.png "Internet Options")

3. **[接続]** タブをクリックします。
  
     ![接続](./media/zscaler-three-tutorial/ic769493.png "Connections")

4. **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5. [プロキシ サーバー] セクションで、次の手順を実行します。

    ![プロキシ サーバー](./media/zscaler-three-tutorial/ic769494.png "Proxy server")

    a. **[LAN にプロキシ サーバーを使用する]** をオンにします。

    b. [アドレス] テキスト ボックスに「**gateway.zscalerthree.net**」と入力します。

    c. [ポート] ボックスに「 **80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。

    e. **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

6. **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="creating-a-zscaler-three-test-user"></a>Zscaler Three テスト ユーザーの作成

Azure AD ユーザーが Zscaler Three にログインできるようにするには、そのユーザーを Zscaler Three にプロビジョニングする必要があります。 Zscaler Three の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **Zscaler Three** テナントにログインします。

2. **[管理]** をクリックします。

    ![Administration](./media/zscaler-three-tutorial/ic781035.png "Administration")

3. **[ユーザー管理]** をクリックします。

     ![Add](./media/zscaler-three-tutorial/ic781036.png "Add")

4. **[ユーザー]** タブで、**[追加]** をクリックします。

    ![Add](./media/zscaler-three-tutorial/ic781037.png "Add")

5. [ユーザーの追加] セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/zscaler-three-tutorial/ic781038.png "Add User")

    a. プロビジョニングする有効な Azure AD アカウントの **[UserID]\(ユーザー ID\)**、**[User Display Name]\(ユーザー表示名\)**、**[Password]\(パスワード\)**、**[Confirm Password]\(確認パスワード\)** を入力し、**[Groups]\(グループ\)** と **[Department]\(部署\)** を選びます。

    b. **[Save]** をクリックします。

> [!NOTE]
> Zscaler Three から提供されている他の Zscaler Three ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zscaler Three へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で、**[Zscaler Three]** を選択します。

    ![Configure single sign-on](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[ユーザーの追加]** ボタンをクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Zscaler Three のタイルをクリックすると、自動的に Zscaler Three アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png