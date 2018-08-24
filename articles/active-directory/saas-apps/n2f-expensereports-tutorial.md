---
title: 'チュートリアル: Azure Active Directory と N2F - Expense reports の統合 | Microsoft Docs'
description: Azure Active Directory と N2F - Expense reports の間のシングル サインオンの設定方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 27fb299bc3bbbbf75bdf40ae02eac627763ce6d4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007596"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>チュートリアル: Azure Active Directory と N2F - Expense reports の統合

このチュートリアルでは、N2F - Expense reports と Azure Active Directory (Azure AD) を統合する方法について説明します。

N2F - Expense reports と Azure AD の統合には、次の利点があります。

- N2F - Expense reports にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に N2F - Expense reports にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

N2F - Expense reports と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- N2F - Expense reports のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの N2F - Expense reports の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>ギャラリーからの N2F - Expense reports の追加

Azure AD への N2F - Expense reports の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから N2F - Expense reports を追加する必要があります。

**ギャラリーから N2F - Expense reports を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**N2F - Expense reports**」と入力し、結果ウィンドウで **N2F - Expense reports** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、N2F - Expense reports で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する N2F - Expense reports ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと N2F - Expense reports の関連ユーザーの間で、リンク関係が確立されている必要があります。

N2F - Expense reports で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[N2F - Expense reports のテスト ユーザーの作成](#create-a-n2f---expense-reports-test-use)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを N2F - Expense reports で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、N2F - Expense reports アプリケーションでシングル サインオンを構成します。

**N2F - Expense reports で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **N2F - Expense reports** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. **IDP 開始モード**でアプリケーションを構成する場合は、Azure と既に統合されているため、**[N2F - Expense reports のドメインと URL]** セクションで実行する必要がある手順はありません。

    ![[N2F - Expense reports のドメインと URL] のシングル サインオン情報](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[N2F - Expense reports のドメインと URL] のシングル サインオン情報](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    **[サインオン URL]** ボックスに、URL として「`https://www.n2f.com/app/`」を入力します。

5. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. **[N2F - Expense reports 構成]** セクションで、**[N2F - Expense reports の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **[SAML エンティティ ID]** をコピーします。

    ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. 別の Web ブラウザー ウィンドウで、N2F - Expense reports 企業サイトに管理者としてサインインします。

9. **[Settings]\(設定\)** をクリックし、ドロップダウンから **[Advance Settings]\(詳細設定\)** を選択します。

    ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/configure1.png)

10. **[Account & Settings]\(アカウントと設定\)** を選択します。

    ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/configure2.png)

11. **[Authentication]\(認証\)** を選択し、**[+ Add an authentication method]\(認証方法の追加\)** タブを選択します。

    ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/configure3.png)

12. 認証方法として **[SAML Microsoft Office 365]** を選択します。

    ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/configure4.png)

13. **[Authentication method]\(認証方法\)** セクションで、次の手順に従います。

    ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/configure5.png)

    a. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure Portal からコピーした **[SAML エンティティ ID]** の値を貼り付けます。

    b. **[Metadata URL]\(メタデータ URL\)** ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    c. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-n2f---expense-reports-test-user"></a>N2F - Expense reports テストユーザーの作成

Azure AD ユーザーが N2F - Expense reports にログインできるようにするには、そのユーザーを N2F - Expense reports にプロビジョニングする必要があります。 N2F - Expense reports の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. N2F - Expense reports 企業サイトに管理者としてログインします。

2. **[Settings]\(設定\)** をクリックし、ドロップダウンから **[Advance Settings]\(詳細設定\)** を選択します。

   ![N2F - Expense でのユーザーの追加](./media/n2f-expensereports-tutorial/configure1.png)

3. ナビゲーション パネルで **[Users]\(ユーザー\)** タブをクリックします。

    ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/user1.png)

4. **[+ New user]\(+ 新規ユーザー\)** タブを選択します。

   ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/user2.png)

5. **[User]\(ユーザー\)** セクションで、次の手順を実行します。

    ![N2F - Expense reports の構成](./media/n2f-expensereports-tutorial/user3.png)

    a. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (**brittasimon@contoso.com** など) を入力します。

    b. **[First name]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    c. **[Name]\(名前\)** ボックスに、ユーザーの名前 (**BrittaSimon** など) を入力します。

    d. 組織の要件に応じて、**[Role]\(ロール\)、[Direct manager (N+1)]\(直属の上司\)** および **[Division]\(部門\)** を選択します。

    e. **[Validate and send invitation]\(検証して招待を送信\)** をクリックします。

    > [!NOTE]
    > ユーザーを追加するときに問題が発生した場合は、[N2F - Expense reports のサポート チーム](mailto:support@n2f.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に N2F - Expense reports へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**N2F - Expense reports に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[N2F - Expense reports]** を選択します。

    ![アプリケーションの一覧の N2F - Expense reports リンク](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで N2F - Expense reports のタイルをクリックすると、自動的に N2F - Expense reports アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

