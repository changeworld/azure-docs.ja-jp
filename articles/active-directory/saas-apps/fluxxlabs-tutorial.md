---
title: 'チュートリアル: Azure Active Directory と Fluxx Labs の統合 | Microsoft Docs'
description: Azure Active Directory と Fluxx Labs の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 367310527619d4bdb5f84a80c567a9d83698846e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433774"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>チュートリアル: Azure Active Directory と Fluxx Labs の統合

このチュートリアルでは、Fluxx Labs と Azure Active Directory (Azure AD) を統合する方法について説明します。

Fluxx Labs と Azure AD の統合には、次の利点があります。

- Fluxx Labs にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Fluxx Labs にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Fluxx Labs と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Fluxx Labs のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Fluxx Labs の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-fluxx-labs-from-the-gallery"></a>ギャラリーからの Fluxx Labs の追加
Azure AD への Fluxx Labs の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Fluxx Labs を追加する必要があります。

**ギャラリーから Fluxx Labs を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Fluxx Labs**」と入力し、結果ウィンドウで **[Fluxx Labs]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Fluxx Labs](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Fluxx Labs で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Fluxx Labs ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Fluxx Labs の関連ユーザーの間で、リンク関係が確立されている必要があります。

Fluxx Labs で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Fluxx Labs で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Fluxx Labs テスト ユーザーの作成](#create-a-fluxx-labs-test-user)** - Fluxx Labs で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Fluxx Labs アプリケーションでシングル サインオンを構成します。

**Fluxx Labs で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Fluxx Labs** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

1. **[Fluxx Labs のドメインと URL]** セクションで、次の手順に従います。

    ![[Fluxx Labs のドメインと URL] のシングル サインオン情報](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    | 環境 | URL パターン|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | 運用前 | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | 環境 | URL パターン|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | 運用前 | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Fluxx Labs サポート チーム](mailto:travis@fluxxlabs.com)に連絡してください。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/fluxxlabs-tutorial/tutorial_general_400.png)

1. **[Fluxx Labs 構成]** セクションで、**[Fluxx Labs の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

1. 別の Web ブラウザーのウィンドウで、Fluxx Labs 企業サイトに管理者としてサインオンします。

1. **[Settings]\(設定\)** セクションで **[Admin]\(管理者\)** を選択します。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config1.png)

1. 管理パネルで、**[Plug-ins]\(プラグイン\)** > **[Integrations]\(インテグレーション\)** の順に選択し、**[SAML SSO-(Disabled)]\(SAML SSO (無効)\)** を選択します。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config2.png)

1. [Attribute]\(属性\) セクションで、次の手順に従います。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config3.png)

    a. **[SAML SSO]** チェックボックスをオンにします。

    b. **[Request Path]\(要求パス\)** ボックスに、「**/auth/saml**」と入力します。

    c. **[Callback Path]\(コールバック パス\)** ボックスに、「**/auth/saml/callback**」と入力します。

    d. **[Assertion Consumer Service Url(Single Sign-On URL)]\(Assertion Consumer Service URL (シングル サインオン URL)\)** テキストボックスに、Azure portal で入力した **[応答 URL]** の値を入力します。

    e. **[Audience(SP Entity ID)]\(対象 (SP エンティティ ID)\)** テキストボックスに、Azure portal で入力した **[ID]** の値を入力します。

    f. **[Identity Provider SSO Target URL]\(ID プロバイダーの SSO ターゲット URL\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービスの URL** の値を貼り付けます。

    g. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    h. **[Name identifier Format]\(名前識別子の形式\)** ボックスに、次の値を入力します。`urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`

    i. **[Save]** をクリックします。

    > [!NOTE]
    > 内容が保存されると、フィールドはセキュリティのために空白で表示されますが、値は構成内に保存されています。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/fluxxlabs-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/fluxxlabs-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/fluxxlabs-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/fluxxlabs-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-fluxx-labs-test-user"></a>Fluxx Labs テスト ユーザーを作成する

Azure AD ユーザーが Fluxx Labs にログインできるようにするには、そのユーザーを Fluxx Labs にプロビジョニングする必要があります。 Fluxx Labs の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Fluxx Labs 企業サイトに管理者としてログインします。

1. 次の図に示されている**アイコン**をクリックします。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config6.png)

1. ダッシュボードで、次の図に示されているアイコンをクリックして、**[New PEOPLE]\(新規ユーザー\)** カードを開きます。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config4.png)

1. **[NEW PEOPLE]\(新規ユーザー\)** セクションで、次の手順を実行します。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs では、SSO ログインの一意識別子として電子メールを使用します。 **[SSO UID]** フィールドにユーザーの電子メール アドレスを入力します。これは、SSO でのログインとして使用される電子メールアドレスと一致する電子メール アドレスです。

    b. **[Save]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Fluxx Labs へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Fluxx Labs に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Fluxx Labs]** を選択します。

    ![アプリケーションの一覧の Fluxx Labs のリンク](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Fluxx Labs] タイルをクリックすると、自動的に Fluxx Labs アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/fluxxlabs-tutorial/tutorial_general_203.png
