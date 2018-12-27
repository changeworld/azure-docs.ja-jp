---
title: 'チュートリアル: Azure Active Directory と Silverback の統合 | Microsoft Docs'
description: Azure Active Directory と Silverback の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.openlocfilehash: e7cb3049f680f81026e09388066001413922600a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123855"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>チュートリアル: Azure Active Directory と Silverback の統合

このチュートリアルでは、Silverback と Azure Active Directory (Azure AD) を統合する方法について説明します。

Silverback と Azure AD の統合には、次の利点があります。

- Silverback にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Silverback に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Silverback と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- アクティブな Silverback サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Silverback を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-silverback-from-the-gallery"></a>ギャラリーから Silverback を追加する
Silverback の Azure AD への統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Silverback を追加する必要があります。

**ギャラリーから Silverback を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Silverback**」と入力し、結果パネルで **[Silverback]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Silverback](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Silverback で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Silverback ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Silverback の関連ユーザーの間で、リンク関係が確立されている必要があります。

Silverback で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Silverback のテスト ユーザーの作成](#create-a-silverback-test-user)** - Silverback で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Silverback アプリケーションでシングル サインオンを構成します。

**Silverback で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Silverback** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. **[Silverback のドメインと URL]** セクションで、次の手順に従います。

    ![[Silverback のドメインと URL] のシングル サインオン情報](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. **[サインオン URL]** ボックスに、`https://<YOURSILVERBACKURL>.com/ssp` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`<YOURSILVERBACKURL>.com` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<YOURSILVERBACKURL>.com/sts/authorize/login` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Silverback クライアント サポート チーム](mailto:helpdesk@matrix42.com)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/silverback-tutorial/tutorial_general_400.png)

6.  Administrator として Silverback Server にログオンし、次の手順を行います。

    a.  **[Admin]\(管理者\)** > **[Authentication Provider]\(認証プロバイダー\)** に移動します。

    b. **[Authentication Provider Settings]\(認証プロバイダーの設定\)** ページで、次の手順を行います。

    ![管理者 ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  **[Import from URL]\(URL からインポートする\)** をクリックします。
    
    d.  コピーしたメタデータ URL を貼り付け、**[OK]** をクリックします。
    
    e.  **[OK]** をクリックして確定すると、値が自動的に入力されます。
    
    f.  **[Show on Login Page]\(ログイン ページに表示する\)** をオンにします。
    
    g.  (省略可能) Azure AD で承認されたユーザーを自動的に追加するには、**[Dynamic User Creation]\(動的なユーザー作成\)** をオンにします。
    
    h.  [Self Service Portal]\(セルフサービス ポータル\) でボタンの **[Title]\(タイトル\)** を作成します。

    i.  **[Choose File]\(ファイルの選択\)** をクリックして **[Icon]\(アイコン\)** をアップロードします。
    
    j.  ボタンの背景**色**を選択します。
    
    k.  **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/silverback-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/silverback-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/silverback-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/silverback-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-silverback-test-user"></a>Silverback テスト ユーザーを作成する

Azure AD ユーザーが Silverback にログインできるようにするには、そのユーザーを Silverback にプロビジョニングする必要があります。 Silverback では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Silverback Server に Administrator としてログインします。

2. **[Users]\(ユーザー\)** に移動し、**新しいデバイス ユーザーを追加**します。

3. **[Basic]\(基本\)** ページで、次の手順を行います。

    ![ユーザー ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーの名前を入力します (例: **Britta**)。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    d. **[E-mail Address]\(電子メール アドレス\)** ボックスに、ユーザーの電子メール アドレスを入力します (例: **Brittasimon@contoso.com**)。

    e. **[Password]\(パスワード\)** ボックスにパスワードを入力します。
    
    f. **[Confirm Password]\(パスワードの確認入力\)** ボックスにパスワードをもう一度入力して確認します。

    g. **[Save]** をクリックします。

>[!NOTE]
>手動で各ユーザーを作成しない場合は、**[Admin]\(管理者\)** > **[Authentication Provider]\(認証プロバイダー\)** の **[Dynamic User Creation]\(動的ユーザーの作成\)** チェックボックスをオンにします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Silverback へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Silverback に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Silverback]** を選択します。

    ![アプリケーションの一覧の [Silverback] リンク](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Silverback] タイルをクリックすると、自動的に Silverback アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

