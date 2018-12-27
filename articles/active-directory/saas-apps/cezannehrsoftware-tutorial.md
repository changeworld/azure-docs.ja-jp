---
title: 'チュートリアル: Azure Active Directory と Cezanne HR Software の統合 | Microsoft Docs'
description: Azure Active Directory と Cezanne HR Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: d617b7a1195f322ad33a47ae2fd99b7eb336b7b2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429908"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>チュートリアル: Azure Active Directory と Cezanne HR Software の統合

このチュートリアルでは、Cezanne HR Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

Cezanne HR Software と Azure AD の統合には、次の利点があります。

- Cezanne HR Software にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に Cezanne HR Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Cezanne HR Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Cezanne HR Software でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Cezanne HR Software の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>ギャラリーからの Cezanne HR Software の追加
Azure AD への Cezanne HR Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cezanne HR Software を追加する必要があります。

**ギャラリーから Cezanne HR Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Cezanne HR Software**」と入力し、結果ウィンドウで **Cezanne HR Software** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Cezanne HR Software](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Cezanne HR Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Cezanne HR Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Cezanne HR Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

Cezanne HR Software で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Cezanne HR Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Cezanne HR Software のテスト ユーザーの作成](#create-a-cezannehrsoftware-test-user)** - Cezanne HR Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Cezanne HR Software アプリケーションでシングル サインオンを構成します。

**Cezanne HR Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Cezanne HR Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

1. **[Cezanne HR Software のドメインと URL]** セクションで、次の手順を実行します。

    ![[Cezanne HR Software のドメインと URL] のシングル サインオン情報](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. **[サインオン URL]** ボックスに、URL として「`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`」を入力します。

    b. **[識別子]** ボックスに次の URL を入力します。`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. **[応答 URL]** ボックスに、URL として「`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`」と入力します。
    
    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 これらの値を入手するには、[Cezanne HR Software クライアント サポート チーム](https://cezannehr.com/services/support/)にお問い合わせください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

1. **[Cezanne HR Software 構成]** セクションで **[Cezanne HR Software の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![Cezanne HR Software 構成](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

1. **[クイック リファレンス]** セクションまで下にスクロールします。 **[クイック リファレンス]** セクションから、**SAML シングル サインオン サービス URL と SAML エンティティ ID** をコピーします。

    ![Cezanne HR Software 構成](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

1. 別の Web ブラウザーのウィンドウで、管理者として Cezanne HR Software テナントにサインオンします。

1. 左側のナビゲーション ウィンドウで、 **[System Setup (システム設定)]** をクリックします。 **[Security Settings (セキュリティの設定)]** に移動します。 次に、 **[Single Sign-On Configuration (シングル サインオンの構成)]** に移動します。

    ![アプリ側でのシングル サインオンの構成](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

1. **[Allow users to log in using the following Single Sign-On (SSO) Service (ユーザーに次のシングル サインオン (SSO) サービスを使用したログインを許可する)]** パネルで **[SAML 2.0]** チェック ボックスをオンにして、**[Advanced Configuration (詳細設定)]** オプションを選択します。

    ![アプリ側でのシングル サインオンの構成](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

1. **[Add New (新規追加)]** ボタンをクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

1. **[SAML 2.0 IDENTITY PROVIDERS (SAML 2.0 ID プロバイダー)]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. **[Display Name (表示名)]** に ID プロバイダー名を入力します。

    b. **[エンティティ ID]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 

    c. **[SAML Binding (SAML バインディング)]** を "POST" に変更します。

    d. **[セキュリティ トークン サービス エンドポイント]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. [ユーザー ID の属性名] ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。
    
    f. **アップロード** アイコンをクリックして、Azure Portal からダウンロードした証明書をアップロードします。
    
    g. **[OK]** ボタンをクリックします。 

1. **[保存]** ボタンをクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Cezanne HR Software のテスト ユーザーの作成

Azure AD ユーザーが Cezanne HR Software にログインできるようにするには、ユーザーを Cezanne HR Software にプロビジョニングする必要があります。 Cezanne HR Software の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1.  Cezanne HR Software 企業サイトに管理者としてログインします。

1.  左側のナビゲーション ウィンドウで、 **[System Setup (システム設定)]** をクリックします。 **[ユーザーの管理]** に移動します。 **[Add New User (新しいユーザーの追加)]** に移動します。

    ![New User](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "New User")

1.  **[Person Details (個人の詳細)]** セクションで、次の手順を実行します。

    ![New User](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "New User")
    
    a. **[Internal User (内部ユーザー)]** をオフに設定します。
    
    b. **[名]** ボックスに、ユーザーの名を入力します (この例では **Britta**)。  
 
    c. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。
    
    d. **[電子メール]** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

1.  **[Account Information (アカウント情報)]** セクションで、次の手順を実行します。

    ![New User](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "New User")
    
    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。
    
    b. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。
    
    c. **[Security Role (セキュリティ ロール)]** で **[HR Professional (人事担当者)]** を選択します。
    
    d. Click **OK**.

1. **[Single Sign-On (シングル サインオン)]** タブに移動し、**[SAML 2.0 Identifiers (SAML 2.0 識別子)]** 領域で **[Add New (新規追加)]** を選択します。

    ![ユーザー](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "User")

1. **[Identity Provider (ID プロバイダー)]** でお使いの ID プロバイダーを選択し、**[User Identifier (ユーザー識別子)]** ボックスに Britta Simon アカウントの電子メール アドレスを入力します。

    ![ユーザー](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "User")
    
1. **[保存]** ボタンをクリックします。

    ![ユーザー](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "User")

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Cezanne HR Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Cezanne HR Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Cezanne HR Software]** を選択します。

    ![アプリケーションの一覧の Cezanne HR Software のリンク](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cezanne HR Software] タイルをクリックすると、自動的に Cezanne HR Software アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

