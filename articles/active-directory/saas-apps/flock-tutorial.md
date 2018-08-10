---
title: 'チュートリアル: Azure Active Directory と Flock の統合 | Microsoft Docs'
description: Azure Active Directory と Flock の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7b2c3ac5-17f1-49a0-8961-c541b258d4b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 6589bbe581ec5e4ca3a18363ede427bf08f8b7cc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428126"
---
# <a name="tutorial-azure-active-directory-integration-with-flock"></a>チュートリアル: Azure Active Directory と Flock の統合

このチュートリアルでは、Flock と Azure Active Directory (Azure AD) を統合する方法について説明します。

Flock と Azure AD の統合には、次の利点があります。

- Flock にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Flock にサインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Flock と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Flock でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Flock の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-flock-from-the-gallery"></a>ギャラリーからの Flock の追加
Azure AD への Flock の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Flock を追加する必要があります。

**ギャラリーから Flock を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Flock**」と入力し、結果パネルで **[Flock]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Flock](./media/flock-tutorial/tutorial_flock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Flock で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Flock ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Flock の関連ユーザーの間で、リンク関係が確立されている必要があります。

Flock で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Flock テスト ユーザーの作成](#create-a-flock-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Flock で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Flock アプリケーションでシングル サインオンを構成します。

**Flock で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Flock** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/flock-tutorial/tutorial_flock_samlbase.png)

1. **[Flock ドメインと URL]** セクションで、次の手順を実行します。

    ![[Flock ドメインと URL] のシングル サインオン情報](./media/flock-tutorial/tutorial_flock_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.flock.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.flock.com/` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 この値を取得するには、[Flock クライアント サポート チーム](mailto:support@flock.com)にお問い合わせください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/flock-tutorial/tutorial_flock_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/flock-tutorial/tutorial_general_400.png)

1. **[Flock 構成]** セクションで、**[Flock の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Flock 構成](./media/flock-tutorial/tutorial_flock_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Flock 企業サイトに管理者としてログインします。

1. 左側のナビゲーション パネルで **[Authentication]\(認証\)** タブを選択し、**[SAML Authentication]\(SAML 認証\)** を選択します。

    ![Flock 構成](./media/flock-tutorial/configure1.png)

1. **[SAML Authentication]** セクションで、次の手順に従います。

    ![Flock 構成](./media/flock-tutorial/configure2.png)

    a. **[SAML 2.0 Endpoint(HTTP)]\(SAML 2.0 エンドポイント (HTTP)\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    b. **[Identity Provider Issuer]\(ID プロバイダー発行者\)** ボックスに、Azure portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c. Azure portal からダウンロードした**証明書 (Base64)** をメモ帳で開き、その内容を **[Public Certificate]\(公開証明書\)** ボックスに貼り付けます。

    d. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/flock-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/flock-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/flock-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/flock-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-flock-test-user"></a>Flock テスト ユーザーの作成

Azure AD ユーザーが Flock にログインできるようにするには、ユーザーを Flock にプロビジョニングする必要があります。 Flock の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Flock 企業サイトに管理者としてログインします。

1. 左側のナビゲーション パネルで **[Manage Team]\(チームの管理\)** をクリックします。

    ![従業員の追加](./media/flock-tutorial/user1.png)

1. **[Add Member]\(メンバーの追加\)** タブをクリックし、**[Team Members]\(チーム メンバー\)** を選択します。

    ![従業員の追加](./media/flock-tutorial/user2.png)

1. **Brittasimon@contoso.com** のようなユーザーのメール アドレスを入力し、**[Add Users]\(ユーザーの追加\)** を選択します。

    ![従業員の追加](./media/flock-tutorial/user3.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Flock へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Flock に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[Flock]** を選択します。

    ![アプリケーションの一覧の Flock のリンク](./media/flock-tutorial/tutorial_flock_app.png)

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Flock のタイルをクリックすると、自動的に Flock アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/flock-tutorial/tutorial_general_01.png
[2]: ./media/flock-tutorial/tutorial_general_02.png
[3]: ./media/flock-tutorial/tutorial_general_03.png
[4]: ./media/flock-tutorial/tutorial_general_04.png

[100]: ./media/flock-tutorial/tutorial_general_100.png

[200]: ./media/flock-tutorial/tutorial_general_200.png
[201]: ./media/flock-tutorial/tutorial_general_201.png
[202]: ./media/flock-tutorial/tutorial_general_202.png
[203]: ./media/flock-tutorial/tutorial_general_203.png
