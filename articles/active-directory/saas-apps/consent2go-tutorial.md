---
title: 'チュートリアル: Azure Active Directory と Consent2Go の統合 | Microsoft Docs'
description: Azure Active Directory と Consent2Go の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ea93bc02-58ca-4468-84ff-359888fc6183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 89fc0b69a3a7ca3f795b4ae0e79b11a4bcd9c9fb
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49433517"
---
# <a name="tutorial-azure-active-directory-integration-with-consent2go"></a>チュートリアル: Azure Active Directory と Consent2Go の統合

このチュートリアルでは、Consent2Go と Azure Active Directory (Azure AD) を統合する方法について説明します。

Consent2Go と Azure AD の統合には、次の利点があります。

- Consent2Go にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Consent2Go に自動的にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Consent2Go と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Consent2Go でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Consent2Go の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-consent2go-from-the-gallery"></a>ギャラリーからの Consent2Go の追加
Azure AD への Consent2Go の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Consent2Go を追加する必要があります。

**ギャラリーから Consent2Go を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/consent2go-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/consent2go-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/consent2go-tutorial/a_new_app.png)

4. 検索ボックスに「**Consent2Go**」と入力し、結果パネルで **[Consent2Go]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

     ![image](./media/consent2go-tutorial/tutorial_consent2go_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Consent2Go で Azure AD シングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Consent2Go ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Consent2Go の関連ユーザーの間で、リンク関係が確立されている必要があります。

Consent2Go で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Consent2Go テスト ユーザーの作成](#create-a-consent2go-test-user)** - Consent2Go で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD シングル サインオンを有効にし、Consent2Go アプリケーションでシングル サインオンを構成します。

**Consent2Go で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. [Azure portal](https://portal.azure.com/) の **Consent2Go** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/consent2go-tutorial/b1_b2_select_sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![image](./media/consent2go-tutorial/b1_b2_saml_sso.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/consent2go-tutorial/b1-domains_and_urlsedit.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、「`   https://www.mcbschools.com/Login`」と入力します。

    ![image](./media/consent2go-tutorial/tutorial_consent2go_url.png)

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー アイコンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、コンピューターに保存します。

    ![image](./media/consent2go-tutorial/tutorial_consent2go_certificate.png) 

6. **Consent2Go** 側でシングル サインオンを構成するには、コピーした**アプリのフェデレーション メタデータ URL** を [Consent2Go サポート チーム](mailto:support@consent2go.com)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/consent2go-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/consent2go-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![image](./media/consent2go-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-consent2go-test-user"></a>Consent2Go テスト ユーザーの作成

このセクションでは、Consent2Go で Britta Simon というユーザーを作成します。 [Consent2Go サポート チーム](mailto:support@consent2go.com)と協力して、Consent2Go プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Consent2Go へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/consent2go-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[Consent2Go]** を選択します。

    ![image](./media/consent2go-tutorial/tutorial_consent2go_app.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/consent2go-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/consent2go-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Consent2Go] タイルをクリックすると、自動的に Consent2Go アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


