---
title: 'チュートリアル: Azure Active Directory と Cisco Webex の統合 | Microsoft Docs'
description: Azure Active Directory と Cisco Webex の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 4dcf487afdad899853c97d3d2a1493a6123b3bab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440715"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>チュートリアル: Azure Active Directory と Cisco Webex の統合

このチュートリアルでは、Cisco Webex と Azure Active Directory (Azure AD) を統合する方法について説明します。

Cisco Webex と Azure AD の統合には、次の利点があります。

- Cisco Webex にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Cisco Webex にサインオンできるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Cisco Webex と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Cisco Webex でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使わないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使わないでください。
- Azure AD の評価環境がない場合は、[1 か月の無料試用版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Cisco Webex の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="add-cisco-webex-from-the-gallery"></a>ギャラリーからの Cisco Webex の追加
Azure AD への Cisco Webex の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Webex を追加する必要があります。

**ギャラリーから Cisco Webex を追加するには、次の手順を実行します。**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに、「**Cisco Webex**」と入力します。 

1. 結果パネルから、**Cisco Webex** を選択します。 次に、**[追加]** ボタンを選択してアプリケーションを追加します。

    ![結果リストの Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Cisco Webex で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Cisco Webex ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Cisco Webex の関連ユーザーの間で、リンクが確立されている必要があります。

Cisco Webex で、**Username** の値を、Azure AD の**ユーザー名**と同じ値に指定します。 これで、2 人のユーザー間にリンクが確立されました。 

Cisco Webex で Azure AD のシングル サインオンを構成してテストするには、次の要素を完了します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
1. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
1. [Cisco Webex のテスト ユーザーの作成](#create-a-cisco-webex-test-user) - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Cisco Webex で作成します。
1. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Cisco Webex アプリケーションでシングル サインオンを構成します。

**Cisco Webex で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Cisco Webex** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

1. シングル サインオンを有効にするには、**[シングル サインオン]** ダイアログ ボックスの **[モード]** ドロップダウン リストで **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

1. **[Cisco Webex のドメインと URL]** セクションで、次の手順を実行します。

    ![[Cisco Webex のドメインと URL] のシングル サインオン情報](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.webex.com` というパターンで URL を入力します。

    b. **[識別子]** ボックスに、URL `http://www.webex.com` を入力します。

    c. **[応答 URL]** ボックスに、`https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company` の形式で URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Cisco Webex クライアント サポート チーム](https://www.webex.co.in/support/support-overview.html)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** を選択し、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

1. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
1. **[Cisco Webex Configuration]\(Cisco Webex の構成\)** セクションで、**[Configure Cisco Webex]\(Cisco Webex の構成\)** を選択して **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL**、**SAML エンティティ ID**、**SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Cisco Webex 企業サイトに管理者としてサインインします。

1. 上部のメニューから **[Site Administration]** を選択します。

    ![Site Administration](./media/cisco-webex-tutorial/ic777621.png "Site Administration")

1. **[Manage Site]** セクションで、**[SSO Configuration]** を選択します。
   
    ![SSO Configuration](./media/cisco-webex-tutorial/ic777622.png "SSO Configuration")

1. **[Federated Web SSO Configuration]** セクションで、次の手順を実行します。
   
    ![Federated SSO Configuration](./media/cisco-webex-tutorial/ic777623.png "Federated SSO Configuration")  

    a. **[Federation Protocol]** ボックスの一覧から、**[SAML 2.0]** を選択します。

    b. **[SSO profile]\(SSO プロファイル\)** で、**[SP Initiated]\(SP によって開始\)** を選択します。

    c. ダウンロードした証明書をメモ帳で開き、内容をコピーします。

    d. **[Import SAML Metadata]\(SAML メタデータのインポート\)** を選択し、コピーした証明書の内容を貼り付けます。

    e. **[Issuer for SAML (IdP ID)]\(SAML の発行者 (IdP ID)\)** ボックスに、Azure Portal からコピーした **[SAML Entity ID]\(SAML エンティティ ID\)** の値を貼り付けます。

    f. **[Customer SSO Service Login URL]\(カスタマー SSO サービス ログイン URL\)** に、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    g. **[NameID Format]** ボックスの一覧で、**[Email address]** を選択します。

    h. **[AuthnContextClassRef]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:ac:classes:Password**」と入力します。

    i. **[Customer SSO Service Logout URL]\(カスタマー SSO サービス ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** を貼り付けます。
   
    j. **[Update]\(更新\)** を選択します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。 **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント](https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/cisco-webex-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** を選びます。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/cisco-webex-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/cisco-webex-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-cisco-webex-test-user"></a>Cisco Webex テスト ユーザーの作成

Azure AD ユーザーが Cisco Webex にサインインできるようにするには、ユーザーを Cisco Webex にプロビジョニングする必要があります。 Cisco Webex の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順を実行します。**

1. **Cisco Webex** テナントにサインインします。

1. **[Manage Users]**  >  **[Add User]** の順にクリックします。
   
    ![Add users](./media/cisco-webex-tutorial/ic777625.png "Add users")

1. **[Add User]** セクションで、次の手順を実行します。
   
    ![ユーザーの追加](./media/cisco-webex-tutorial/ic777626.png "ユーザーの追加")   

    a. **[Account Type]** として **[Host]** を選択します。

    b. **[First name]\(名\)** ボックスに、ユーザーの名 (この場合は **Britta**) を入力します。

    c. **[Last name]\(姓\)** ボックスに、ユーザーの姓 (この場合は **Simon**) を入力します。

    d. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール アドレス (この場合は **Brittasimon@contoso.com**) を入力します。

    e. **[Email]\(電子メール\)** ボックスに、ユーザーの電子メール アドレス (この場合は **Brittasimon@contoso.com**) を入力します。

    f. **[パスワード]** ボックスには、ユーザーのパスワードを入力します。

    g. **[Confirm Password]\(パスワードの確認\)** ボックスに、ユーザーのパスワードをもう一度入力します。

    h. **[追加]** を選択します。

>[!NOTE]
>Cisco Webex から提供されている他の Cisco Webex ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon というユーザーに Cisco Webex へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Cisco Webex に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルで、アプリケーション ビューを開きます。 次に、ディレクトリ ビューに移動し、**[エンタープライズ アプリケーション]** に移動します。  

1. **[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Cisco Webex]** を選択します。

    ![アプリケーションの一覧の [Cisco Webex] リンク](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンを選びます。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[ユーザー]** 一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

1. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cisco Webex] タイルを選ぶと、自動的に Cisco Webex アプリケーションにサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

