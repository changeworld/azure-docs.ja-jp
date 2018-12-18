---
title: 'チュートリアル: Azure Active Directory と GitHub の統合 | Microsoft Docs'
description: Azure Active Directory と GitHub の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: dbd4634c575fd4f1886d3e7714ef9ddabbde0f8a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341159"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>チュートリアル: Azure Active Directory と GitHub の統合

このチュートリアルでは、GitHub と Azure Active Directory (Azure AD) を統合する方法について説明します。

GitHub と Azure AD の統合には、次の利点があります。

- GitHub にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に GitHub にサインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

GitHub と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- GitHub シングル サインオンが 有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの GitHub の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-github-from-the-gallery"></a>ギャラリーからの GitHub の追加
Azure AD への GitHub の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に GitHub を追加する必要があります。

**ギャラリーから GitHub を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/github-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/github-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/github-tutorial/a_new_app.png)

4. 検索ボックスに「**GitHub**」と入力し、結果パネルで **[GitHub]** を選択します。次に **[追加]** ボタン をクリックして、アプリケーションを追加します。

     ![image](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、GitHub で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する GitHub ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと GitHub の関連ユーザーの間で、リンク関係が確立されている必要があります。

GitHub で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[GitHub のテスト ユーザーの作成](#create-a-github-test-user)** - GitHub で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simonにリンク させます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal でAzure AD シングル サインオンを有効にし、 GitHub アプリケーションで、シングル サインオンを構成します。

**GitHub で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. **GitHub** アプリケーション統合ページ上の [Azure portal](https://portal.azure.com/) で、**[シングル サインオン]** を選択します。

    ![image](./media/github-tutorial/b1_b2_select_sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![image](./media/github-tutorial/b1_b2_saml_sso.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![image](./media/github-tutorial/tutorial_github_url.png) 

    a. **[サインオン URL]** ボックスに、`https://github.com/orgs/<entity-id>/sso` のパターンを使用して URL を入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。`https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子で、これらの値を更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、GitHub 管理者セクションに移動します。

5. GitHub アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[編集]** ボタンをクリックして、**[ユーザー属性]** ダイアログを開きます。

    ![image](./media/github-tutorial/i3-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。
    
    a. **[編集]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/github-tutorial/i2-attribute.png)

    ![image](./media/github-tutorial/i4-attribute.png)

    b. **[ソース属性]** の一覧から、属性値を選択します。

    c. **[Save]** をクリックします。
 
7. **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[Download]\(ダウンロード\)** をクリックして**証明書 (Base64)** をダウンロードして、コンピューターに保存します。

    ![image](./media/github-tutorial/tutorial_github_certficate.png)

8. **[Set up GitHub]\(GitHub の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![image](./media/github-tutorial/d1_samlsonfigure.png) 

9. 別の Web ブラウザー ウィンドウで、GitHub 組織サイトに管理者としてログインします。

10. **[設定]** に移動し、**[セキュリティ]** をクリックします。

    ![設定](./media/github-tutorial/tutorial_github_config_github_03.png)

11. **[Enable SAML authentication (SAML 認証を有効にする)]** チェック ボックスをオンにして、シングル サインオンの構成フィールドを表示します。 次に、シングル サインオン URL 値を使用して、Azure AD 構成でシングル サインオン URL を更新します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_13.png)

12. 次のフィールドを構成します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. **[シングル サインオン URL]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[発行者]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書 をメモ帳で開き、その内容を **[Public Certificate]\(公開証明書\)** ボックスに貼り付けます。

    d. **[Edit]\(編集\)** アイコンをクリックし、**[Signature Method]\(署名方法\)** と **[Digest Method]\(ダイジェスト方法\)** を編集して、**RSA-SHA1** および **SHA1** から **RSA-SHA256** および **SHA256** に変更します (下図参照)。

    ![image](./media/github-tutorial/tutorial_github_sha.png) 
    
13. **[Test SAML configuration (SAML 構成のテスト)]** をクリックして、SSO の際に検証の失敗やエラーがないことを確認します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_06.png)

14. **[保存]**

> [!NOTE]
> GitHub でのシングル サインオンは GitHub で特定の組織を認証するものです。GitHub そのものの認証に取って代わることはできません。 つまり、ユーザーの github.com セッションの有効期限が切れた場合は、シングル サインオン プロセス中に GitHub の ID とパスワードで認証するように求められることがあります。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/github-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/github-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順のようにします。

    ![image](./media/github-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、**brittasimon@yourcompanydomain.extension** と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-github-test-user"></a>GitHub テスト ユーザーの作成

このセクションの目的は、GitHub で Britta Simon というユーザーを作成することです。 GitHub では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](github-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. GitHub 企業サイトに管理者としてログインします。

2. **[ユーザー]** をクリックします。

    ![ユーザー](./media/github-tutorial/tutorial_github_config_github_08.png "People")

3. **[メンバーの招待]** をクリックします。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_09.png "ユーザーの招待")

4. **[メンバーの招待]** ダイアログ ページで、次の手順を実行します。

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_10.png "ユーザーの招待")

    b. **[Send Invitation (招待状の送信)]** をクリックします。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_11.png "ユーザーの招待")

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に GitHub へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/github-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **GitHub** を選択します。

    ![image](./media/github-tutorial/tutorial_github_app.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/github-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/github-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで GitHub のタイルをクリックすると、GitHub アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


