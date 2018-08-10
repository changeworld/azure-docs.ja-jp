---
title: 'チュートリアル: Azure Active Directory と Procore SSO の統合 | Microsoft Docs'
description: Azure Active Directory と Procore SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 871fde799f064f52b11b75e190b9b7c8a73a843d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445207"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>チュートリアル: Azure Active Directory と Procore SSO の統合

このチュートリアルでは、Procore SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。

Procore SSO と Azure AD を統合すると、次の利点が得られます。

- Procore SSO にアクセス可能な Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Procore SSO へサインオンされる (シングル サインオン) ようにできます
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Procore SSO と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Procore SSO でのシングル サインオン (SSO) が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Procore SSO の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-procore-sso-from-the-gallery"></a>ギャラリーからの Procore SSO の追加
Azure AD への Procore SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Procore SSO を追加する必要があります。

**ギャラリーから Procore SSO を追加するには、次の手順を実行します。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Procore SSO**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/procoresso-tutorial/tutorial_procoresso_search.png)

1. 結果ウィンドウで **[Procore SSO]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーを基に、Procore SSO で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Procore SSO ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Procore SSO の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Procore SSO の **[Username]** の値として割り当てます。

Procore SSO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Procore SSO のテスト ユーザーの作成](#creating-a-procore-sso-test-user)** - Procore SSO で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Procore SSO アプリケーションでシングル サインオンを構成します。

**Procore SSO で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Procore SSO** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/procoresso-tutorial/tutorial_procoresso_samlbase.png)

1. アプリは Azure と事前に統合済みであるため、**[Procore SSO のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![Configure single sign-on](./media/procoresso-tutorial/tutorial_procoresso_url.png)

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/procoresso-tutorial/tutorial_general_400.png)

1. **[Procore SSO 構成]** セクションで、**[Procore SSO の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/procoresso-tutorial/tutorial_procoresso_configure.png) 

1. **Procore SSO** 側のシングル サインオンを構成するために、Procore の企業サイトに管理者としてログインします。

1. [TOOLBOX] \(ツールボックス) ドロップ ダウンから **[Admin] \(管理)** をクリックして、SSO 設定ページを開きます。

    ![Configure single sign-on](./media/procoresso-tutorial/procore_tool_admin.png)

1. 以下の説明に従って、各ボックスに値を貼り付けます。

    ![Configure single sign-on](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. **[Single Sign On Issuer URL] \(シングル サインオン発行者の URL)** ボックスに、Azure Portal でコピーした SAML エンティティ ID を貼り付けます。

    b. **[SAML Sign On Target URL] \(SAML サインオンのターゲット URL)** ボックスに、Azure Portal でコピーした SAML シングル サインオン サービスの URL を貼り付けます。

    c. 次に、先ほど Azure Portal からダウンロードした **Metadata XML** を開いて、**X509Certificate** という名前のタグの証明書をコピーします。 コピーした値を、**[Single Sign On x509 Certificate] \(シングル サインオン x509 証明書)** ボックスに貼り付けます。

1. **[Save Changes] \(変更を保存)** をクリックします。

1. 上記の設定後、Procore へのログインで経由する**ドメイン名** (例: **contoso.com**) を [Procore のサポート チーム](https://support.procore.com/)に送信して、このドメインのフェデレーション SSO を有効化してもらう必要があります。

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/procoresso-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/procoresso-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/procoresso-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/procoresso-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-procore-sso-test-user"></a>Procore SSO のテスト ユーザーの作成

以下の手順に従って、Procore の側でテスト ユーザーを作成します。

1. Procore 企業サイトに管理者としてログインします。  

1. [TOOLBOX] \(ツールボックス) ドロップ ダウンで **[Directory] \(ディレクトリ)** をクリックして、企業のディレクトリ ページを開きます。

    ![Configure single sign-on](./media/procoresso-tutorial/Procore_sso_directory.png)

1. **[Add a Person] \(ユーザーの追加)** オプションをクリックしてフォームを開き、次の手順を実行します。

    ![Configure single sign-on](./media/procoresso-tutorial/Procore_user_add.png)

    a. **[First Name] \(名)** テキストボックスに、ユーザーの名を入力します (この例では **Britta**).

    b. **[Last Name] \(姓)** テキストボックスに、ユーザーの姓を入力します (この例では **Simon**).

    c. **[Email Address] \(メール アドレス)** テキストボックスに、ユーザーのメール アドレスを入力します (この例では **BrittaSimon@contoso.com**)。

    d. **[Permission Template] \(アクセス許可テンプレート)** で **[Apply Permission Template Later] \(アクセス許可テンプレートは後で適用する)** を選択します。

    e. **Create** をクリックしてください。

1. 新しく追加された連絡先の詳細を確認して更新します。

    ![Configure single sign-on](./media/procoresso-tutorial/Procore_user_check.png)

1. メールによる招待状が必要な場合は **[Save and Send Invitiation] \(保存して招待状を送信)** を、直接保存する場合は **[Save] \(保存)** をクリックして、ユーザー登録を完了します。
    
    ![Configure single sign-on](./media/procoresso-tutorial/Procore_user_save.png)  

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Procore SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Procore SSO に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Procore SSO]** を選択します。

    ![Configure single sign-on](./media/procoresso-tutorial/tutorial_procoresso_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)を参照してください。 アクセス パネルで [Procore SSO] タイルをクリックすると、自動的に Procore SSO アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/procoresso-tutorial/tutorial_general_01.png
[2]: ./media/procoresso-tutorial/tutorial_general_02.png
[3]: ./media/procoresso-tutorial/tutorial_general_03.png
[4]: ./media/procoresso-tutorial/tutorial_general_04.png

[100]: ./media/procoresso-tutorial/tutorial_general_100.png

[200]: ./media/procoresso-tutorial/tutorial_general_200.png
[201]: ./media/procoresso-tutorial/tutorial_general_201.png
[202]: ./media/procoresso-tutorial/tutorial_general_202.png
[203]: ./media/procoresso-tutorial/tutorial_general_203.png

