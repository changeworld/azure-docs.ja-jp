---
title: 'チュートリアル: Azure Active Directory と NetSuite の統合 | Microsoft Docs'
description: Azure Active Directory と NetSuite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 511fdcf587d16a59ff2bb11dfc55504b2218a569
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431411"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>チュートリアル: Azure Active Directory と NetSuite の統合

このチュートリアルでは、NetSuite と Azure Active Directory (Azure AD) を統合する方法について説明します。

NetSuite と Azure AD の統合には、次の利点があります。

- NetSuite にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に NetSuite にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と NetSuite の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- NetSuite でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの NetSuite の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-netsuite-from-the-gallery"></a>ギャラリーからの NetSuite の追加
Azure AD への NetSuite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に NetSuite を追加する必要があります。

**ギャラリーから NetSuite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]

1. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「**NetSuite**」と入力し、結果ウィンドウで **[NetSuite]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の NetSuite](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、NetSuite で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する NetSuite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと NetSuite の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を NetSuite の **[ユーザー名]** の値として割り当てます。

NetSuite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[NetSuite テスト ユーザーの作成](#creating-a-netsuite-test-user)** - NetSuite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、NetSuite アプリケーションでシングル サインオンを構成します。

**NetSuite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **NetSuite** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

1. **[NetSuite のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URL で値を更新します。 これらの値を取得するには、[NetSuite サポート チーム](http://www.NetSuite.com/portal/services/support.shtml)に連絡してください。

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/tutorial_general_400.png)

1. **[NetSuite 構成]** セクションで、**[NetSuite の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

1. ブラウザーで新しいタブを開き、NetSuite の会社のサイトに管理者としてサインインします。

1. ページの上部にあるツール バーで、**[セットアップ]** をクリックして、**[会社]** に移動し、**[機能の有効化]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setupsaml.png)

1. ページの中央にあるツール バーで、**[SuiteCloud]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-suitecloud.png)

1. **[Manage Authentication]\(認証の管理)** セクションで、**[SAML シングル サインオン]** を選択して、NetSuite での SAML シングル サインオン オプションを有効にします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-ticksaml.png)

1. ページの上部にあるツール バーで、**[セットアップ]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

1. **[セットアップ タスク]** 一覧で、**[統合]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-integration.png)

1. **[Manage Authentication]\(認証の管理\)** セクションで、**[SAML シングル サインオン]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-saml.png)

1. **[SAML Setup]\(SAML セットアップ\)** ページの **[NetSuite の構成]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **[PRIMARY AUTHENTICATION METHOD]\(プライマリ認証方法\)** を選択します。

    b. **[SAMLV2 IDENTITY PROVIDER METADATA]\(SAMLV2 ID プロバイダー メタデータ\)** というラベルが付いたフィールドで、**[UPLOAD IDP METADATA FILE]\(IDP メタデータ ファイルのアップロード\)** を選択します。 **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    c. **[送信]** をクリックします。

1. Azure AD で **[その他のすべてのユーザー属性を表示および編集する]** チェック ボックスをオンにして、属性を追加します。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-attributes.png)

1. **[属性名]** フィールドに、「`account`」と入力します。 **[属性値]** フィールドに、NetSuite アカウント ID を入力します。 この値は定数であり、アカウントによって異なります。 アカウント ID を確認する方法を次に示します。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. NetSuite で **[セットアップ]** をクリックし、**[会社]** に移動して、上部の ナビゲーション メニューから **[会社情報]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-com.png)

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-account-id.png)

    b. 右側の列の **[会社情報]** ページで、**アカウント ID** をコピーします。

    c. NetSuite アカウントからコピーした**アカウント ID** を Azure AD の**属性値**フィールドに貼り付けます。 

1. ユーザーは NetSuite にシングル サインオンする前に、まず、NetSuite で適切なアクセス許可が割り当てられている必要があります。 次の手順に従って、アクセス許可を割り当てます。

    a. ナビゲーション メニューで、**[セットアップ]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

    b. 左側のナビゲーション メニューで、**[Users/Roles]**、**[Manage Roles]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. **[New Role]** をクリックします。

    d. 新しいロールの**名前**を入力します:

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-new-role.png)

    e. **[Save]** をクリックします。

    f. 上部のメニューで、 **[Permissions]** をクリックします。 **[Setup]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-sso.png)

    g. **[SAML シングル サインオン]** を選択して、**[追加]** をクリックします。

    h. **[Save]** をクリックします。

    i. 上部のナビゲーション メニューで、**[Setup]**、**[Setup Manager]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

    j. 左側のナビゲーション メニューで、**[Users/Roles]**、**[Manage Users]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-manage-users.png)

    k. テスト ユーザーを選択します。 次に、**[編集]** をクリックし、**[アクセス]** タブに移動します。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-edit-user.png)

    l. [ロール] ダイアログで、作成した適切なロールを割り当てます。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-add-role.png)

    m. **[Save]** をクリックします。
 
### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/NetSuite-tutorial/create_aaduser_01.png) 

1.  **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/NetSuite-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/NetSuite-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。 

### <a name="creating-a-netsuite-test-user"></a>NetSuite テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを NetSuite に作成します。 NetSuite では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。
このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ NetSuite に存在しない場合は、NetSuite にアクセスしようとしたときに新しいユーザーが作成されます。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に NetSuite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**NetSuite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[NetSuite]** を選択します。

    ![Configure single sign-on](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

シングル サインオンの設定をテストするために、アクセス パネル ([https://myapps.microsoft.com](https://myapps.microsoft.com/)) を開き、テスト アカウントにサインインし、**[NetSuite]** をクリックします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

