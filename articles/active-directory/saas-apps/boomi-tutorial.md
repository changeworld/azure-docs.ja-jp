---
title: 'チュートリアル: Azure Active Directory と Boomi の統合 | Microsoft Docs'
description: Azure Active Directory と Boomi の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: cf925e0e0e7b6b4c10b6b21d17214f91473a9026
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432951"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>チュートリアル: Azure Active Directory と Boomi の統合

このチュートリアルでは、Boomi と Azure Active Directory (Azure AD) を統合する方法について説明します。

Boomi と Azure AD の統合には、次の利点があります。

- Boomi にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Boomi にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Boomi と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Boomi でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Boomi の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-boomi-from-the-gallery"></a>ギャラリーからの Boomi の追加
Azure AD への Boomi の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Boomi を追加する必要があります。

**ギャラリーから Boomi を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Boomi**」と入力し、結果パネルで **[Boomi]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Boomi](./media/boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Boomi で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Boomi ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Boomi の関連ユーザーの間で、リンク関係が確立されている必要があります。

Boomi で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Boomi で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Boomi のテスト ユーザーの作成](#create-a-boomi-test-user)** - Boomi で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Boomi アプリケーションにシングル サインオンを構成します。

**Boomi で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Boomi** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/boomi-tutorial/tutorial_boomi_samlbase.png)

1. **[Boomi のドメインと URL]** セクションで、次の手順を実行します。

    ![[Boomi のドメインと URL] のシングル サインオン情報](./media/boomi-tutorial/tutorial_boomi_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://platform.boomi.com/`

    b. **[応答 URL]** ボックスに、`https://platform.boomi.com/sso/<boomi-tenant>/saml` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 値を取得するには、[Boomi サポート チーム](https://boomi.com/company/contact/)に問い合わせてください。
 
1. Boomi アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure single sign-on](./media/boomi-tutorial/tutorial_attribute.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、以下の表に示す行ごとに、次の手順を実行します。

    | 属性名 | 属性値 |
    | -------------- | --------------- |
    | FEDERATION_ID | User.mail |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。
    
    ![Configure single sign-on](./media/boomi-tutorial/tutorial_officespace_04.png)
    
    ![Configure single sign-on](./media/boomi-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/boomi-tutorial/tutorial_boomi_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/boomi-tutorial/tutorial_general_400.png)

1. **[Boomi 構成]** セクションで、**[Boomi の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Boomi の構成](./media/boomi-tutorial/tutorial_boomi_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Boomi 企業サイトに管理者としてログインします。 

1. **[会社名]**、**[セットアップ]** の順に進みます。

1. **[SSO オプション]** タブをクリックして以下の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. **[Enable SAML Single Sign-On]\(SAML シングル サインオンの有効化)** のチェックボックスをオンにします。

    b. **[インポート]** をクリックして、Azure AD からダウンロードした証明書を **ID プロバイダー証明書** にアップロードします。
    
    c. **[ID プロバイダーのログイン URL]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-On Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    d. **[Federation Id Location]** で、**[Federation Id is in FEDERATION_ID Attribute element]** オプションを選択します。 

    e. **[保存]** ボタンをクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/boomi-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/boomi-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/boomi-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/boomi-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-boomi-test-user"></a>Boomi のテスト ユーザーの作成

Azure AD ユーザーが Boomi にログインできるようにするには、ユーザーを Boomi にプロビジョニングする必要があります。 Boomi の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. Boomi 企業サイトに管理者としてログインします。

1. **[ユーザー管理]**、**[ユーザー]** の順に進みます。

    ![ユーザー](./media/boomi-tutorial/tutorial_boomi_001.png "Users")

1. **+** アイコンをクリックすると **[Add/Maintain User Roles]\(ユーザーの役割の追加と管理)** ダイアログが開きます。

    ![ユーザー](./media/boomi-tutorial/tutorial_boomi_002.png "Users")

    ![ユーザー](./media/boomi-tutorial/tutorial_boomi_003.png "Users")

    a. **[ユーザーのメール アドレス]** ボックスに、ユーザーのメール アドレスを入力します (この例では BrittaSimon@contoso.com)。
    
    b. **[名]** ボックスに、ユーザーの名を入力します (この例では Britta)。

    c. **[姓]** ボックスに、ユーザーの姓を入力します (この例では Simon)。
    
    d. ユーザーの**フェデレーション ID** を入力します。 各ユーザーには、アカウント内のユーザーを一意に識別するフェデレーション ID が必要です。
    
    e. **標準ユーザー**の役割をユーザーに割り当てます。 シングル サインオンのアクセスのほかに、Boomi AtomSphere の通常のアクセスも付与することになるため、管理者の役割は割り当てないでください。
    
    f. Click **OK**.
    
    > [!NOTE]
    > ユーザー パスワードは ID プロバイダーを通じて管理されるため、AtomSphere アカウントのログインに使用するパスワードを含む、「ようこそ」の通知メールはユーザーに送信されません。 Boomi から提供されている他の Boomi ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Boomi へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Boomi に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Boomi]** を選択します。

    ![アプリケーションの一覧の Boomi のリンク](./media/boomi-tutorial/tutorial_boomi_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Boomi] タイルをクリックすると、自動的に Boomi アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/boomi-tutorial/tutorial_general_01.png
[2]: ./media/boomi-tutorial/tutorial_general_02.png
[3]: ./media/boomi-tutorial/tutorial_general_03.png
[4]: ./media/boomi-tutorial/tutorial_general_04.png

[100]: ./media/boomi-tutorial/tutorial_general_100.png

[200]: ./media/boomi-tutorial/tutorial_general_200.png
[201]: ./media/boomi-tutorial/tutorial_general_201.png
[202]: ./media/boomi-tutorial/tutorial_general_202.png
[203]: ./media/boomi-tutorial/tutorial_general_203.png

