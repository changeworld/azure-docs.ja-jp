---
title: 'チュートリアル: Azure Active Directory と BorrowBox の統合 | Microsoft Docs'
description: Azure Active Directory と BorrowBox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd8e4178-9a63-492a-bd48-782e94e404af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: jeedes
ms.openlocfilehash: 16252f690bfd34e596b2b8db26ab3a2ede0dfdee
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2018
ms.locfileid: "48833462"
---
# <a name="tutorial-azure-active-directory-integration-with-borrowbox"></a>チュートリアル: Azure Active Directory と BorrowBox の統合

このチュートリアルでは、BorrowBox と Azure Active Directory (Azure AD) を統合する方法について説明します。

BorrowBox と Azure AD の統合には、次の利点があります。

- BorrowBox にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーに対して自分の Azure AD アカウントで自動的に BorrowBox にサインオン (シングル サインオン) することを許可する設定が行えます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

BorrowBox と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- BorrowBox のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの BorrowBox の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-borrowbox-from-the-gallery"></a>ギャラリーからの BorrowBox の追加
Azure AD への BorrowBox の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BorrowBox を追加する必要があります。

**ギャラリーから BorrowBox を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/borrowbox-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/borrowbox-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/borrowbox-tutorial/a_new_app.png)

4. 検索ボックスに「**BorrowBox**」と入力し、結果パネルで **[BorrowBox]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![image](./media/borrowbox-tutorial/tutorial_borrowbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、BorrowBox で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する BorrowBox ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BorrowBox の関連ユーザーの間で、リンク関係が確立されている必要があります。

BorrowBox で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[BorrowBox のテスト ユーザーの作成](#create-a-borrowbox-test-user)** - BorrowBox で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、BorrowBox アプリケーションでシングル サインオンを構成します。

**BorrowBox で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. [Azure portal](https://portal.azure.com/) の **[BorrowBox]** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/borrowbox-tutorial/B1_B2_Select_SSO.png)

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

      ![image](./media/borrowbox-tutorial/b1_b2_saml_ssso.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードを選択して、シングル サインオンを有効にします。

    ![image](./media/borrowbox-tutorial/b1_b2_saml_sso.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/borrowbox-tutorial/b1-domains_and_urlsedit.png)

5. アプリは Azure と事前に統合済みであるため、**[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url.png)

    a. **[追加の URL を設定します]** をクリックします。

    b. **[サインオン URL]** ボックスに、`https://fe.bolindadigital.com/wldcs_bol_fo/b2i/mainPage.html?b2bSite=<ID>` という形式で URL を入力します。

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url1.png)

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 値を取得するには、[BorrowBox クライアント サポート チーム](mailto:borrowbox@bolinda.com)にお問い合わせください。 

6. **[Set up Single Sign-On with SAML]\(SAML でのシングル サインオンの設定)** ページの **[SAML Signing Certificate]\(SAML 署名証明書)** セクションで、**[ダウンロード]** をクリックして要件のとおりに適切な証明書をダウンロードして、コンピューターに保存します。

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_certificate.png) 

7. **BorrowBox** サイドでシングル サインオンを構成するには、Azure portal からダウンロードした証明書/メタデータを [BorrowBox サポート チーム](mailto:borrowbox@bolinda.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/borrowbox-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/borrowbox-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順のようにします。

    ![image](./media/borrowbox-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、**brittasimon@yourcompanydomain.extension** と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-borrowbox-test-user"></a>BorrowBox テスト ユーザーの作成

このセクションの目的は、BorrowBox で Britta Simon というユーザーを作成することです。 BorrowBox では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない BorrowBox ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、[BorrowBox サポート チーム](mailto:borrowbox@bolinda.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に BorrowBox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/borrowbox-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[BorrowBox]** を選択します。

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_app.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/borrowbox-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/borrowbox-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [BorrowBox] タイルをクリックすると、BorrowBox アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
