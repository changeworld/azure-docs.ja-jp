---
title: 'チュートリアル: Azure Active Directory と ThousandEyes の統合 | Microsoft Docs'
description: Azure Active Directory と ThousandEyes の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b6af7a3322b1a01c1d822df78d827121c19e21e1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444404"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>チュートリアル: Azure Active Directory と ThousandEyes の統合

このチュートリアルでは、ThousandEyes と Azure Active Directory (Azure AD) を統合する方法について説明します。

ThousandEyes と Azure AD の統合には、次の利点があります。

- ThousandEyes にアクセスするユーザーを Azure AD で管理できます
- ユーザーが自分の Azure AD アカウントで自動的に ThousandEyes にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ThousandEyes と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ThousandEyes でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ThousandEyes の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-thousandeyes-from-the-gallery"></a>ギャラリーからの ThousandEyes の追加
Azure AD への ThousandEyes の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ThousandEyes を追加する必要があります。

**ギャラリーから ThousandEyes を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**ThousandEyes**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. 結果ウィンドウで **ThousandEyes** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ThousandEyes で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ThousandEyes ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ThousandEyes の関連ユーザーの間で、リンク関係が確立されている必要があります。

ThousandEyes で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

ThousandEyes で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[ThousandEyes のテスト ユーザーの作成](#creating-a-thousandeyes-test-user)** - ThousandEyes で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、ThousandEyes アプリケーションでシングル サインオンを構成します。

**ThousandEyes で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ThousandEyes** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. **[ThousandEyes のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    **[サインオン URL]** ボックスに、「`https://app.thousandeyes.com/login/sso`」と入力します。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. **[ThousandEyes 構成]** セクションで、**[ThousandEyes の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. 別の Web ブラウザーのウィンドウで、管理者として **ThousandEyes** 企業サイトにサインオンします。

1. 上部のメニューで **[Settings]** をクリックします。

    ![設定](./media/thousandeyes-tutorial/ic790066.png "Settings")

1. ページの下部にある **[Account]**

    ![Account](./media/thousandeyes-tutorial/ic790067.png "Account")

1. **[Security & Authentication]** タブをクリックします。

    ![セキュリティと認証](./media/thousandeyes-tutorial/ic790068.png "Security & Authentication")

1. **[Setup Single Sign-On]** セクションで、次の手順を実行します。

    ![シングル サインオンの設定](./media/thousandeyes-tutorial/ic790069.png "Setup Single Sign-On")

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[Login Page URL]\(ログイン ページ URL\)** テキストボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    c. **[Logout Page URL]\(ログアウト ページ URL\)** テキストボックスに、Azure portal からコピーした**サインアウト URL** を貼り付けます。

    d. **[Identity Provider Issuer]\(ID プロバイダーの発行者\)** テキストボックスに、Azure portal からコピーした **SAML エンティティ ID** を貼り付けます。

    e. **[Verification Certificate]\(検証証明書\)** で **[Choose file]\(ファイルの選択\)** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。

    f. **[Save]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-thousandeyes-test-user"></a>ThousandEyes テスト ユーザーの作成

このセクションの目的は、ThousandEyesで Britta Simon というユーザーを作成することです。 ThousandEyes では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](thousandeyes-provisioning-tutorial.md)を参照してください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. ThousandEyes 企業サイトに管理者としてログインします。

1. **[設定]** をクリックします。

    ![設定](./media/thousandeyes-tutorial/IC790066.png "Settings")

1. **[アカウント]** クリックします。

    ![Account](./media/thousandeyes-tutorial/IC790067.png "Account")

1. **[Accounts & Users]** タブをクリックします。

    ![アカウントとユーザー](./media/thousandeyes-tutorial/IC790073.png "Accounts & Users")

1. **[Add Users & Accounts]** セクションで、次の手順を実行します。

    ![ユーザー アカウントの追加](./media/thousandeyes-tutorial/IC790074.png "Add User Accounts")

    a. **[Name]\(名前\)** ボックスに、ユーザーの氏名 (**Britta Simon** など) を入力します。

    b. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレス (**brittasimon@contoso.com** など) を入力します。

    b. **[Add New User to Account]** をクリックします。

    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、アカウントを確認してアクティブにするためのリンクを含むメールが送られます。

> [!NOTE]
> ThousandEyes から提供されている他の ThousandEyes ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ThousandEyes へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を ThousandEyes に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[ThousandEyes]** を選択します。

    ![Configure single sign-on](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで ThousandEyes のタイルをクリックすると、自動的に ThousandEyes アプリケーションにサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
