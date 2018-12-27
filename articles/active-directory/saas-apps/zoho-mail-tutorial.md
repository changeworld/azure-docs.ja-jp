---
title: 'チュートリアル: Azure Active Directory と Zoho の統合 | Microsoft Docs'
description: Azure Active Directory と Zoho の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 03950d983f6ed119ae6cf7a7391418804bb20c76
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446278"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>チュートリアル: Azure Active Directory と Zoho の統合

このチュートリアルでは、Zoho と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zoho と Azure AD の統合には、次の利点があります。

- Zoho にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Zoho に自動サインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zoho と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Zoho でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zoho の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zoho-from-the-gallery"></a>ギャラリーからの Zoho の追加
Azure AD への Zoho の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zoho を追加する必要があります。

**ギャラリーから Zoho を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Zoho**」と入力し、結果パネルで **[Zoho]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Zoho](./media/zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zoho で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zoho ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zoho の関連ユーザーの間で、リンク関係が確立されている必要があります。

Zoho で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Zoho で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Zoho テスト ユーザーの作成](#create-a-zoho-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Zoho で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Zoho アプリケーションでシングル サインオンを構成します。

**Zoho で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Zoho** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/zoho-mail-tutorial/tutorial_zoho_samlbase.png)

1. **[Zoho のドメインと URL]** セクションで、次の手順を実行します。

    ![[Zoho のドメインと URL] のシングル サインオン情報](./media/zoho-mail-tutorial/tutorial_zoho_url.png)

    a. **[サインオン URL]** ボックスに、`https://<company name>.zohomail.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Zoho クライアント サポート チーム](https://www.zoho.com/mail/contact.html)にお問い合わせください。 
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/zoho-mail-tutorial/tutorial_zoho_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/zoho-mail-tutorial/tutorial_general_400.png)

1. **[Zoho 構成]** セクションで、**[Zoho の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL、パスワードの変更 URL、および SAML シングル サインオン サービス URL** をコピーします。

    ![Zoho 構成](./media/zoho-mail-tutorial/tutorial_zoho_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Zoho Mail 企業サイトに管理者としてログインします。

1. **コントロール パネル**に移動します。
   
    ![コントロール パネル](./media/zoho-mail-tutorial/ic789607.png "Control Panel")

1. **[SAML 認証]** タブをクリックします。
   
    ![SAML Authentication](./media/zoho-mail-tutorial/ic789608.png "SAML Authentication")

1. **[SAML 認証の詳細]** セクションで、次の手順に従います。
   
    ![SAML 認証の詳細](./media/zoho-mail-tutorial/ic789609.png "SAML Authentication Details")
   
    a. **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。
   
    b. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** を貼り付けます。
   
    c. **[Change Password URL]\(パスワードの変更 URL\)** ボックスに、Azure Portal からコピーした**パスワードの変更 URL** を貼り付けます。
       
    d. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[PublicKey]\(公開キー\)** ボックスに貼り付けます。
   
    e. **[アルゴリズム]** として **[RSA]** を選択します。
   
    f. Click **OK**.

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/zoho-mail-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/zoho-mail-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/zoho-mail-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/zoho-mail-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-zoho-test-user"></a>Zoho テスト ユーザーの作成

Azure AD ユーザーが Zoho Mail にログインできるようにするには、そのユーザーを Zoho Mail にプロビジョニングする必要があります。 Zoho Mail の場合、プロビジョニングは手動で行います。

> [!NOTE]
> Zoho Mail から提供されている他の Zoho Mail ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. **Zoho Mail** 企業サイトに管理者としてログインします。

1. **[コントロール パネル] \> [メールとドキュメント]** に移動します。

1. **[ユーザーの詳細] \> [ユーザーの追加]** の順に移動します。
   
    ![ユーザーの追加](./media/zoho-mail-tutorial/ic789611.png "Add User")

1. **[ユーザーの追加]** ダイアログで、次の手順を実行します。
   
    ![ユーザーの追加](./media/zoho-mail-tutorial/ic789612.png "Add User")
   
    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Email ID]\(メール ID\)** ボックスに、ユーザーのメール ID (この例では **brittasimon@contoso.com**) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。
   
    e. Click **OK**.  
      
    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zoho へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Zoho に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Zoho]** を選択します。

    ![アプリケーションの一覧の [Zoho] リンク](./media/zoho-mail-tutorial/tutorial_zoho_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Zoho] タイルをクリックすると、自動的に Zoho アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/zoho-mail-tutorial/tutorial_general_203.png

