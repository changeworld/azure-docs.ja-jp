---
title: 'チュートリアル: Azure Active Directory と 8x8 Virtual Office の統合 | Microsoft Docs'
description: Azure Active Directory と 8x8 Virtual Office の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 53db637bf7ad47896747b491fcbe31123fdb104e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741812"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>チュートリアル: Azure Active Directory と 8x8 Virtual Office の統合

このチュートリアルでは、8x8 Virtual Office と Azure Active Directory (Azure AD) を統合する方法について説明します。

8x8 Virtual Office と Azure AD の統合には、次の利点があります。

- 8x8 Virtual Office にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に 8x8 Virtual Office にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と 8x8 Virtual Office の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 8x8 Virtual Office でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの 8x8 Virtual Office の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>ギャラリーからの 8x8 Virtual Office の追加

Azure AD への 8x8 Virtual Office の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に 8x8 Virtual Office を追加する必要があります。

**ギャラリーから 8x8 Virtual Office を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**8x8 Virtual Office**」と入力し、結果ウィンドウで **[8x8 Virtual Office]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの 8x8 Virtual Office](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、8x8 Virtual Office で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する 8x8 Virtual Office のユーザーを認識している必要があります。 言い換えると、Azure AD ユーザーと 8x8 Virtual Office の関連ユーザーの間で、リンク関係が確立されている必要があります。

8x8 Virtual Office で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[8x8 Virtual Office テスト ユーザーの作成](#creating-a-8x8-virtual-office-test-user)** - Azure AD でのユーザーにリンクされた、8x8 Virtual Office での Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、8x8 Virtual Office アプリケーションでシングル サインオンを構成します。

**8x8 Virtual Office で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **[8x8 Virtual Office]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[8x8 Virtual Office のドメインと URL] のシングル サインオン情報](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://sso.8x8.com/saml2`

    b. **[応答 URL]** ボックスに、次の URL を入力します。`https://sso.8x8.com/saml2`

5. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Raw)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

6. **[8x8 Virtual Office の設定]** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![8x8 Virtual Office の構成](common/configuresection.png)

7. 8x8 Virtual Office テナントに管理者としてサインオンします。

8. アプリケーション パネルで **[Virtual Office Account Mgr (Virtual Office アカウント マネージャー)]** を選択します。

    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. 管理する **[Business (ビジネス)]** アカウントを選択し、**[Sign In (サインイン)]** をクリックします。

    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. メニュー リストの **[ACCOUNTS]\(アカウント\)** タブをクリックします。

    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. アカウントの一覧で **[Single Sign On (シングル サインオン)]** をクリックします。
  
    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. [Authentication methods]\(認証方法\) の **[Single Sign On]\(シングル サインオン\)** を選択し、**[SAML]** をクリックします。

    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. **[SAML Single Sign-On]\(SAML シングル サインオン\)** セクションで、次の手順に従います。

    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. **[Sign In URL]\(サインイン URL\)** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[Sign Out URL]\(サインアウト URL\)** テキストボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    c. **[Issuer URL]\(発行者 URL\)** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    d. **[Browse]\(参照\)** ボタンをクリックして、Azure portal からダウンロードした証明書をアップロードします。

    e. **[保存]** ボタンをクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
  
### <a name="creating-a-8x8-virtual-office-test-user"></a>8x8 Virtual Office のテスト ユーザーの作成

このセクションの目的は、8x8 Virtual Office で Britta Simon というユーザーを作成することです。 8x8 Virtual Office では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 8x8 Virtual Office にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[8x8 Virtual Office サポート チーム](https://www.8x8.com/about-us/contact-us)に問い合わせる必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、8x8 Virtual Office へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[8x8 Virtual Office]** を選択します。

    ![Configure single sign-on](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [8x8 Virtual Office] タイルをクリックすると、自動的に 8x8 Virtual Office アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
