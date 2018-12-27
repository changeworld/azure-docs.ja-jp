---
title: 'チュートリアル: Azure Active Directory と GetThere の統合 | Microsoft Docs'
description: Azure Active Directory と GetThere の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: bcefa3966a6c854f02ce7b3a75306b3d1c888ecd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49433109"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>チュートリアル: Azure Active Directory と GetThere の統合

このチュートリアルでは、GetThere と Azure Active Directory (Azure AD) を統合する方法について説明します。

GetThere と Azure AD の統合には、次の利点があります。

- GetThere にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に GetThere にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

GetThere と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- GetThere でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの GetThere の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-getthere-from-the-gallery"></a>ギャラリーからの GetThere の追加
Azure AD への GetThere の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に GetThere を追加する必要があります。

**ギャラリーから GetThere を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/getthere-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/getthere-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/getthere-tutorial/a_new_app.png)

4. 検索ボックスに「**GetThere**」と入力し、結果パネルで **[GetThere]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

     ![image](./media/getthere-tutorial/tutorial_getthere_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、GetThere で Azure AD シングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する GetThere ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと GetThere の関連ユーザーの間で、リンク関係が確立されている必要があります。

GetThere で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[GetThere テスト ユーザーの作成](#create-a-getthere-test-user)** - GetThere で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD シングル サインオンを有効にし、GetThere アプリケーションでシングル サインオンを構成します。

**GetThere で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. [Azure portal](https://portal.azure.com/) の **GetThere** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/getthere-tutorial/B1_B2_Select_SSO.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードを選択して、シングル サインオンを有効にします。

    ![image](./media/getthere-tutorial/b1_b2_saml_sso.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/getthere-tutorial/b1-domains_and_urlsedit.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![image](./media/getthere-tutorial/tutorial_getthere_url.png)

    a. **[識別子]** ボックスに、次のいずれかの URL を入力します。
    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. **[応答 URL]** ボックスに、次のいずれかの URL を入力します。
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |
    
5. GetThere アプリケーションでは、ユーザー名要求に一意の**ユーザー名**値が必要です。 顧客は、ユーザー名要求の適切な値をマップできます。 次の例では、**[ユーザー名]** を **user.mail** にマップしていますが、組織の設定に応じてマッピングを変更できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](./media/getthere-tutorial/i4-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。
    
    | Name |  ソース属性 |  名前空間 |
    | ---------------| --------------- | --------------- |
    | サイト名 | "組織に応じて値を指定" | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | ユーザー名 |  User.mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |
    
    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/getthere-tutorial/i2-attribute.png)

    ![image](./media/getthere-tutorial/i3-attribute.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** ボックスに、その行に表示される属性名前空間を入力します。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[Save]** をクリックします。

7. **[Set up Single Sign-On with SAML]\(SAML でのシングル サインオンの設定\)** ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[Download]\(ダウンロード\)** をクリックして**証明書 (Base64)** をダウンロードし、コンピューターに保存します。

    ![image](./media/getthere-tutorial/tutorial_getthere_certficate.png) 

8. **[Set up GetThere]\(GetThere の設定)** セクションで、要件に従って適切な URL をコピーします。

    URL は次のように表示されている場合があることに注意してください。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![image](./media/getthere-tutorial/d1_samlsonfigure.png) 

9. **GetThere** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Base64)** と、コピーした**ログイン URL、Azure AD 識別子、ログアウト URL** を [GetThere クライアント サポート チーム](mailto:dataintegration@sabre.com)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/getthere-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/getthere-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![image](./media/getthere-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-getthere-test-user"></a>GetThere テスト ユーザーの作成

このセクションでは、GetThere で Britta Simon というユーザーを作成します。 [GetThere クライアント サポート チーム](mailto:dataintegration@sabre.com)と協力して、GetThere プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に GetThere へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/getthere-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[GetThere]** を選択します。

    ![image](./media/getthere-tutorial/tutorial_getthere_app.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/getthere-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/getthere-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [GetThere] タイルをクリックすると、自動的に GetThere アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
