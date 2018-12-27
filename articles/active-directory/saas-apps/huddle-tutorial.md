---
title: 'チュートリアル: Azure Active Directory と Huddle の統合 | Microsoft Docs'
description: Azure Active Directory と Huddle の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 050a5a210ab10a557b149513a0416b188f4618ba
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885905"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>チュートリアル: Azure Active Directory と Huddle の統合

このチュートリアルでは、Huddle と Azure Active Directory (Azure AD) を統合する方法について説明します。

Huddle と Azure AD の統合には、次の利点があります。

- Huddle にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Huddle にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Huddle と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Huddle でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Huddle の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-huddle-from-the-gallery"></a>ギャラリーからの Huddle の追加

Azure AD への Huddle の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Huddle を追加する必要があります。

**ギャラリーから Huddle を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. 検索ボックスに「**Huddle**」と入力します。 結果ウィンドウで **[Huddle]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Huddle で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Huddle ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Huddle の関連ユーザーの間で、リンク関係が確立されている必要があります。

Huddle で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Huddle テスト ユーザーの作成](#creating-a-huddle-test-user)** - Huddle で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Huddle アプリケーションでシングル サインオンを構成します。

**Huddle で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Huddle** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

    ![Configure single sign-on](./media/huddle-tutorial/tutorial_general_300.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/huddle-tutorial/tutorial_general_301.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/huddle-tutorial/tutorial_general_302.png)

5. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    > [!NOTE]
    > 下で入力するドメインから Huddle インスタンスが自動的に検出されます。

    ![[Huddle のドメインと URL] のシングル サインオン情報](./media/huddle-tutorial/tutorial_huddle_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. **[応答 URL]** ボックスに、URL を入力します。

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

    c. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Huddle のドメインと URL] のシングル サインオン情報](./media/huddle-tutorial/tutorial_huddle_url1.png)

    **[サインオン URL]** ボックスに、次のパターンを使用していずれかの URL を入力します。

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Huddle クライアント サポート チーム](https://huddle.zendesk.com)に問い合わせてください。

6. **[Set up Single Sign-On with SAML]\(SAML でのシングル サインオンの設定\)** ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[ダウンロード]** をクリックして要件のとおりに適切な証明書をダウンロードして、コンピューターに保存します。

    ![Configure single sign-on](./media/huddle-tutorial/tutorial_huddle_certificate.png)

7. **[Set up Huddle]\(Huddle の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    ![Configure single sign-on](./media/huddle-tutorial/tutorial_huddle_configure.png)

8. **Huddle** 側でシングル サインオンを構成するには、ダウンロードした証明書と、Azure portal の **[Set up** **Huddle]\(Huddle の設定\)** セクションからコピーした URL を、[Huddle クライアント サポート チーム](https://huddle.zendesk.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

    >[!NOTE]
    > Huddle サポート チームがシングル サインオンを有効にする必要があります。 構成が完了すると、通知が届きます。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/huddle-tutorial/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/huddle-tutorial/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-huddle-test-user"></a>Huddle テスト ユーザーの作成

Azure AD ユーザーが Huddle にログインできるようにするには、そのユーザーを Huddle にプロビジョニングする必要があります。 Huddle の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Huddle** 企業サイトに管理者としてログインします。

2. **[ワークスペース]** をクリックします。

3. **[ユーザー] \>[ユーザーの招待]** の順にクリックします。

    ![ユーザー](./media/huddle-tutorial/IC787838.png "People")

4. **[新しい招待の作成]** セクションで、次の手順を実行します。
  
    ![New Invitation](./media/huddle-tutorial/IC787839.png "New Invitation")
  
    a. **[ユーザーを招待して参加させるチームの選択]** の一覧で **[チーム]** を選択します。

    b. **[Enter email address for people you'd like to invite]\(招待したい人の電子メール アドレスの入力\)** ボックスに、プロビジョニングを行う有効な Azure AD アカウントの**電子メール アドレス**を入力します。

    c. **[招待]** をクリックします。

    >[!NOTE]
    > アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。

>[!NOTE]
>Huddle から提供されている他の Huddle ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Huddle へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Huddle]\(Huddle\)** を選択します。

    ![Configure single sign-on](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Huddle のタイルをクリックすると、Huddle アプリケーションのページに自動的にログインします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
