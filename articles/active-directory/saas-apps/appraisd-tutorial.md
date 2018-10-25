---
title: 'チュートリアル: Azure Active Directory と Appraisd の統合 | Microsoft Docs'
description: Azure Active Directory と Appraisd の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118337"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>チュートリアル: Azure Active Directory と Appraisd の統合

このチュートリアルでは、Appraisd と Azure Active Directory (Azure AD) を統合する方法について説明します。

Appraisd と Azure AD の統合には、次の利点があります。

- Appraisd にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Appraisd にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Appraisd と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Appraisd でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Appraisd を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-appraisd-from-the-gallery"></a>ギャラリーから Appraisd を追加する
Azure AD への Appraisd の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Appraisd を追加する必要があります。

**ギャラリーから Appraisd を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/appraisd-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/appraisd-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/appraisd-tutorial/a_new_app.png)

4. 検索ボックスに「**Appraisd**」と入力し、結果ウィンドウで **[Appraisd]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![image](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Appraisd で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Appraisd ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Appraisd の関連ユーザーの間で、リンク関係が確立されている必要があります。

Appraisd で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Appraisd のテスト ユーザーの作成](#create-an-appraisd-test-user)** - Appraisd で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Appraisd アプリケーションでシングル サインオンを構成します。

**Appraisd で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. [Azure portal](https://portal.azure.com/) の **[Appraisd]** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードを選択して、シングル サインオンを有効にします。

    ![image](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a. **[追加の URL を設定します]** をクリックします。 

    b. **[リレー状態]** ボックスに、URL `<TENANTCODE>` を入力します。

    c. **[SP]** 開始モードでアプリケーションを構成する場合は、**[サインオン URL]** テキストボックスに `https://app.appraisd.com/saml/<TENANTCODE>` のパターンで URL を入力します。

    > [!NOTE]
    > このチュートリアルで後述する Appraisd SSO の [Configuration]\(構成\) ページで、実際の [Sign-on URL]\(サインオン URL\) と [Relay State]\(リレー状態\) の値を取得します。
    
5. Appraisd アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[編集]** ボタンをクリックして、**[ユーザー属性]** ダイアログを開きます。

    ![image](./media/appraisd-tutorial/i3-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。
    
    a. **[編集]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/appraisd-tutorial/i2-attribute.png)

    ![image](./media/appraisd-tutorial/i4-attribute.png)

    b. **[ソース属性]** の一覧から、属性値を選択します。

    c. **[Save]** をクリックします。 

7. **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードして、コンピューターに保存します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. **[Set up Appraisd]\(Appraisd の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![image](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Appraisd にサインインします。

10. ページの右上にある **[Settings]\(設定\)** アイコンをクリックし、**[Configuration]\(構成\)** に移動します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. メニューの左側から **[SAML single sign-on]\(SAML シングル サインオン\)** をクリックします。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. **[SAML 2.0 Single Sign-On configuration]\(SAML 2.0 のシングル サインオンの構成\)** ページで、次の手順を実行します。
    
    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. **[Default Relay State]\(既定のリレー状態\)** 値をコピーし、Azure portal の **[基本的な SAML 構成]** の **[リレー状態]** ボックスに貼り付けます。

    b. **[Service-initiated login URL]\(サービス開始ログイン URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** の **[サインオン URL]** ボックスに貼り付けます。

13. 同じページの **[Identifying users]\(ユーザーの識別\)** まで下にスクロールし、次の手順を実行します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. **[Identity Provider Single Sign-On URL]\(ID プロバイダー シングル サインオン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付け、**[Save]\(保存\)** をクリックします。

    b. **[Identity Provider Issuer URL]\(ID プロバイダーの発行者 URL\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付け、**[Save]\(保存\)** をクリックします。

    c. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付け、**[Save]\(保存\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/appraisd-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/appraisd-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![image](./media/appraisd-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-an-appraisd-test-user"></a>Appraisd テスト ユーザーを作成する

Azure AD ユーザーが Appraisd にサインインできるようにするには、ユーザーを Appraisd にプロビジョニングする必要があります。 Appraisd では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Appraisd にサインインします。

2. ページの右上にある **[Settings]\(設定\)** アイコンをクリックし、**[Administration centre]\(管理センター\)** に移動します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. ページの上部にあるツール バーで、**[People]\(人\)** をクリックし、**[Add a new user]\(新しいユーザーの追加\)** に移動します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. **[Add a new user]\(新しいユーザーの追加\)** ページで、次の手順を実行します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a. **[First name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    b. **[Last name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    c. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: **Brittasimon@contoso.com**)。

    d. **[ユーザーの追加]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Appraisd へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/appraisd-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[Appraisd]** を選択します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/appraisd-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/appraisd-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Appraisd のタイルをクリックすると、Appraisd アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
