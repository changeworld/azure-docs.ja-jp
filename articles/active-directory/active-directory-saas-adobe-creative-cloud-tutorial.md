---
title: "チュートリアル: Azure Active Directory と Adobe Creative Cloud の統合 | Microsoft Docs"
description: "Azure Active Directory と Adobe Creative Cloud の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9ba1171e-56b1-4475-b308-58637d35e5a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 3d13608612c77236346b0e98551d7fc427d602e1
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>チュートリアル: Azure Active Directory と Adobe Creative Cloud の統合

このチュートリアルでは、Adobe Creative Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adobe Creative Cloud と Azure AD の統合には、次の利点があります。

- Adobe Creative Cloud にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Adobe Creative Cloud にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Adobe Creative Cloud と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adobe Creative Cloud でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Adobe Creative Cloud を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>ギャラリーから Adobe Creative Cloud を追加する
Azure AD への Adobe Creative Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Creative Cloud を追加する必要があります。

**ギャラリーから Adobe Creative Cloud を追加するには、次の手順に従います。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Adobe Creative Cloud**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_000.png)

5. 結果ウィンドウで **[Adobe Creative Cloud]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Adobe Creative Cloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Adobe Creative Cloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Adobe Creative Cloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Adobe Creative Cloud の **[Username]** の値として割り当てることで確立されます。

Adobe Creative Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Adobe Creative Cloud のテスト ユーザーの作成](#creating-an-adobe-creative-cloud-test-user)** - Adobe Creative Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Adobe Creative Cloud アプリケーションにシングル サインオンを構成します。

**Adobe Creative Cloud との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Microsoft Azure 管理ポータルの **Adobe Creative Cloud** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure Single Sign-On][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_01.png)

3. **[Adobe Creative Cloud のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url1.png)

    a. **[識別子]** ボックスに、値として「`https://www.okta.com/saml2/service-provider/<token>`」と入力します。

    b. **[応答 URL]** ボックスに、`https://<company name>.okta.com/auth/saml20/accauthlinktest` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 これらの値は、実際の識別子と応答 URL で更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 ユーザーを手動で作成する必要がある場合は、Adobe Creative Cloud のサポート チームにお問い合わせください。

4. **[Adobe Creative Cloud のドメインと URL]** セクションで、**SP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url2.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[サインオン URL]** テキストボックスに、「`https://adobe.com`」と入力します。

5. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_05.png) 

6. **[Adobe Creative Cloud Configuration (Adobe Creative Cloud 構成)]** セクションで、**[Configure Adobe Creative Cloud (Adobe Creative Cloud の構成)]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 [クイック リファレンス] セクションから **SAML エンティティ ID** と **SAML SSO サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_06.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として Adobe Creative Cloud テナントにサインオンします。

8.  左のナビゲーション ウィンドウで **[ID]** に移動して、ドメインをクリックします。 その後、**[Single Sign On Configuration Required (シングル サインオンの構成が必要です)]** セクションで、次の手順を実行します。

    ![設定](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Settings")

9. **[Browse (参照)]** をクリックして、Azure AD からダウンロードした証明書を **[IDP Certificate (IDP 証明書)]** にアップロードします。

10. **[IDP issuer (IDP 発行者)]** ボックスで、Azure ポータルの **[サインオンの構成]** セクションからコピーした **SAML エンティティ ID** の値を入力します。

11. **[IDP Login URL (IDP ログイン URL)]** ボックスで、Azure ポータルの **[サインオンの構成]** セクションからコピーした **SAML SSO サービスの URL** の値を入力します。

12. **[IDP Binding (IDP バインディング)]** として **[HTTP - Redirect (HTTP - リダイレクト)]** を選択します。

13. **[User Login Setting (ユーザー ログイン設定)]** として **[Email Address (電子メール アドレス])** を選択します。
 
14. **[保存]** ボタンをクリックします。

15. ダッシュボードに、XML の **"メタデータのダウンロード"** ファイルが表示されます。 これには、アドビの EntityDescriptor URL と AssertionConsumerService URL が含まれています。 ファイルを開き、Azure AD アプリケーションでこれらを構成してください。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. **[アプリケーション設定の構成]** ダイアログの**識別子**として、アドビによって提供された EntityDescriptor 値を使用します。

    b. **[アプリケーション設定の構成]** ダイアログの **[応答 URL]** として、アドビによって提供された AssertionConsumerService 値を使用します。
 
### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 

### <a name="creating-an-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud テスト ユーザーを作成する

Azure AD ユーザーが Adobe Creative Cloud にログインできるようにするには、そのユーザーを Adobe Creative Cloud にプロビジョニングする必要があります。  
Adobe Creative Cloud の場合、プロビジョニングは手動で実行します。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Adobe Creative Cloud 企業サイトに管理者としてログインします。

2. **[ユーザー]**をクリックします。

    ![ユーザー](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_001.png "People")

3. **[ユーザーの招待]**をクリックします。

    ![ユーザーの招待](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_002.png "ユーザーの招待")

4. **[Invite People (ユーザーの招待)]** ダイアログ ページで、次の手順を実行します。

    ![ユーザーの招待](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_003.png "ユーザーの招待")

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。
    
    b. **[招待]**をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adobe Creative Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Adobe Creative Cloud に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Adobe Creative Cloud]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adobe Creative Cloud] タイルをクリックすると、自動的に Adobe Creative Cloud アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png
