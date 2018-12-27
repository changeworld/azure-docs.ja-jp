---
title: 'チュートリアル: Azure Active Directory と ON24 Virtual Environment SAML Connection の統合 | Microsoft Docs'
description: Azure Active Directory と ON24 Virtual Environment SAML Connection の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 1ec18f0013a7fa640395a8b8bedd9df8b0924c3a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071064"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>チュートリアル: Azure Active Directory と ON24 Virtual Environment SAML Connection の統合

このチュートリアルでは、ON24 Virtual Environment SAML Connection と Azure Active Directory (Azure AD) を統合する方法について説明します。

ON24 Virtual Environment SAML Connection と Azure AD の統合には、次の利点があります。

- ON24 Virtual Environment SAML Connection へのアクセス権を持つユーザーを Azure AD 内で制御できます。
- ユーザーが自分の Azure AD アカウントを使用して自動的に ON24 Virtual Environment SAML Connection にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と ON24 Virtual Environment SAML Connection の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ON24 Virtual Environment SAML Connection シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ON24 Virtual Environment SAML Connection の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>ギャラリーからの ON24 Virtual Environment SAML Connection の追加
Azure AD への ON24 Virtual Environment SAML Connection の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に ON24 Virtual Environment SAML Connection を追加する必要があります。

**ギャラリーから ON24 Virtual Environment SAML Connection を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/on24-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/on24-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/on24-tutorial/a_new_app.png)

4. 検索ボックスに「**ON24 Virtual Environment SAML Connection**」と入力して、結果パネルから **[ON24 Virtual Environment SAML Connection]\(ON24 Virtual Environment SAML Connection\)** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

     ![image](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、ON24 Virtual Environment SAML Connection との Azure AD シングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する ON24 Virtual Environment SAML Connection のユーザーを認識している必要があります。 言い換えると、Azure AD ユーザーと ON24 Virtual Environment SAML Connection の関連ユーザーの間で、リンク関係が確立されている必要があります。

ON24 Virtual Environment SAML Connection との Azure AD シングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ON24 Virtual Environment SAML Connection テスト ユーザーの作成](#create-an-on24-virtual-environment-saml-connection-test-user)** - ON24 Virtual Environment SAML Connection 上で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にし、ON24 Virtual Environment SAML Connection アプリケーション上でシングル サインオンを構成します。

**ON24 Virtual Environment SAML Connection との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. [Azure portal](https://portal.azure.com/) の **[ON24 Virtual Environment SAML Connection]\(ON24 Virtual Environment SAML Connection\)** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

      ![image](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードを選択して、シングル サインオンを有効にします。

    ![image](./media/on24-tutorial/b1_b2_saml_sso.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![image](./media/on24-tutorial/tutorial_on24_url.png)

    a. **[識別子]** テキスト ボックスに次の URL を入力します。

     **運用環境 URL**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **QA 環境 URL**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. **[応答 URL]** テキスト ボックスに、次の URL を入力します。
    
     **運用環境 URL**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **QA 環境 URL**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. **[追加の URL を設定します]** をクリックします。 

    d. **[リレー状態]** テキスト ボックスに、URL `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>` を入力します

    e. **[SP]** 開始モードでアプリケーションを構成する場合は、**[サインオン URL]** テキスト ボックスに次の URL を入力します: `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. **[Set up Single Sign-On with SAML]\(SAML でのシングル サインオンの設定\)** ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[ダウンロード]** をクリックして要件のとおりに適切な証明書をダウンロードして、コンピューターに保存します。

    ![image](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. **ON24 Virtual Environment SAML Connection** 側でシングル サインオンを構成するには、Azure portal からダウンロードした証明書/メタデータを [ON24 Virtual Environment SAML Connection サポート チーム](https://www.on24.com/about-us/support/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/on24-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/on24-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![image](./media/on24-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、**brittasimon@yourcompanydomain.extension** と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>ON24 Virtual Environment SAML Connection テスト ユーザーの作成

このセクションでは、ON24 Virtual Environment SAML Connection に Britta Simon というユーザーを作成します。 [ON24 Virtual Environment SAML Connection サポート チーム](https://www.on24.com/about-us/support/)と協力して、ON24 Virtual Environment SAML Connection プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ON24 Virtual Environment SAML Connection へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/on24-tutorial/d_all_applications.png)

2. アプリケーションの一覧で、**[ON24 Virtual Environment SAML Connection]\(ON24 Virtual Environment SAML Connection\)** を選択します。

    ![image](./media/on24-tutorial/tutorial_on24_app.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/on24-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/on24-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [ON24 Virtual Environment SAML Connection]\(ON24 Virtual Environment SAML Connection\) タイルをクリックすると、ON24 Virtual Environment SAML Connection アプリケーションに自動的にサインインします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

