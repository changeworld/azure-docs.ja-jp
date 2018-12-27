---
title: 'チュートリアル: Azure Active Directory と Teamphoria の統合 | Microsoft Docs'
description: Azure Active Directory と Teamphoria の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 554930b18a271a677aeb5e82c3e62a94965a8e7f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439611"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>チュートリアル: Azure Active Directory と Teamphoria の統合

このチュートリアルでは、Teamphoria と Azure Active Directory (Azure AD) を統合する方法について説明します。

Teamphoria と Azure AD の統合には、次の利点があります。

- Teamphoria にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Teamphoria にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Teamphoria と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Teamphoria でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Teamphoria の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-teamphoria-from-the-gallery"></a>ギャラリーからの Teamphoria の追加
Azure AD への Teamphoria の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Teamphoria を追加する必要があります。

**ギャラリーから Teamphoria を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Teamphoria**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

1. 結果ウィンドウで **[Teamphoria]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Teamphoria で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Teamphoria ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Teamphoria の関連ユーザーの間で、リンク関係が確立されている必要があります。

Teamphoria で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Teamphoria のテスト ユーザーの作成](#creating-a-teamphoria-test-user)** - Teamphoria で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Teamphoria アプリケーションにシングル サインオンを構成します。

**Teamphoria で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Teamphoria** アプリケーション統合ページで、**[シングルサインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

1. **[Teamphoria のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    **[サインオン URL]** ボックスに、`https://<sub-domain>.teamphoria.com/login` という形式で URL を入力します。   

    > [!NOTE] 
    > サインオン URL は実際の値ではありません。 この値は実際のサインオン URL で更新する必要があります。 シングル サインオン URL を取得するには、[Teamphoria クライアント サポート チーム](https://www.teamphoria.com/)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書を保存します。

    ![Configure single sign-on](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/teamphoria-tutorial/tutorial_general_400.png)

1. **[Teamphoria Configuration (Teamphoria 構成)]** セクションで、**[Configure Teamphoria (Teamphoria を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

1. **Teamphoria** 側でシングル サインオンを構成するには、Teamphoria アプリケーションに管理者としてログインします。

1. 左のツールバーの [Configure] タブの **[ADMIN SETTINGS]** オプションに移動し、**[SINGLE SIGN-ON]** をクリックして SSO 構成ウィンドウを開きます。

    ![Configure single sign-on](./media/teamphoria-tutorial/admin_sso_configure.png)

1. 右上隅の **[ADD NEW IDENTITY PROVIDER (新しい ID プロバイダーの追加)]** をクリックして、SSO の設定を追加するためのフォームを開きます。

    ![Configure single sign-on](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. 以下の説明に従って、フィールドに詳細を入力します。

    ![Configure single sign-on](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **表示名**: 管理者ページでのプラグインの表示名を入力します。

    b. **ボタン名**: SSO を使用してログインするためのログイン ページに表示されるタブの名前。

    c. **証明書**: Azure portal から先ほどダウンロードした証明書をメモ帳で開き、内容をコピーして、こちらのボックス内に貼り付けます。

    d. **エントリ ポイント**: Azure portal から先ほどコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    e. オプションを **[ON (オン)]** に切り替え、**[保存]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/teamphoria-tutorial/create_aaduser_01.png) 

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/teamphoria-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/teamphoria-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-teamphoria-test-user"></a>Teamphoria テスト ユーザーの作成

Azure AD ユーザーが Teamphoria にログインできるようにするには、そのユーザーを Teamphoria にプロビジョニングする必要があります。 Teamphoria の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Teamphoria 企業サイトに管理者としてログインします。

1. 左のツールバーの **[管理]** タブで**管理者**設定をクリックし、**[ユーザー]** をクリックして、ユーザーの管理者ページを開きます。

    ![従業員の追加](./media/teamphoria-tutorial/admin_manage_users.png)

1. **[MANUAL INVITE (手動招待)]** オプションをクリックします。

    ![[ユーザーの招待]](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. このページで、次の操作を実行します。
    
    ![[ユーザーの招待]](./media/teamphoria-tutorial/manual_user_invite.png)

    a. **[EMAIL ADDRESS (電子メール アドレス)]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    b. **[名]** ボックスに「**Britta**」と入力します。

    c. **[姓]** ボックスに「**Simon**」と入力します。

    d. **[INVITE 1 USER (1 ユーザーを招待)]** をクリックします。 ユーザーをシステムに作成するには、そのユーザーが招待を受け入れる必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Teamphoria へのアクセスを　許可することにより、Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200]

**Britta Simon を Teamphoria に割り当てるには、次の手順を実行します。**

1. Azure portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[Teamphoria]** を選択します。

    ![Configure single sign-on](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
