---
title: 'チュートリアル: Azure Active Directory と OrgChart Now の統合 | Microsoft Docs'
description: Azure Active Directory と OrgChart Now の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: e23d76074f4b428b672e0cd5aeeaba99d080a4cf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435936"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>チュートリアル: Azure Active Directory と OrgChart Now の統合

このチュートリアルでは、OrgChart Now と Azure Active Directory (Azure AD) を統合する方法について説明します。

OrgChart Now と Azure AD の統合には、次の利点があります。

- OrgChart Now にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に OrgChart Now にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

OrgChart Now と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- OrgChart Now でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから OrgChart Now を追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-orgchart-now-from-the-gallery"></a>ギャラリーから OrgChart Now を追加
Azure AD への OrgChart Now の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OrgChart Now を追加する必要があります。

**ギャラリーから OrgChart Now を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**OrgChart Now**」と入力し、結果パネルから **[OrgChart Now]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、OrgChart Now で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する OrgChart Now ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと OrgChart Now の関連ユーザーの間でリンク関係が確立されている必要があります。

OrgChart Now で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[OrgChart Now のテスト ユーザーの作成](#create-an-orgchart-now-test-user)** - OrgChart Now で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、OrgChart Now アプリケーションでシングル サインオンを構成します。

**OrgChart Now で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **OrgChart Now** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

1. **[OrgChart Now のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[OrgChart Now のドメインと URL] のシングル サインオン情報](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    **[識別子]** ボックスに次の URL を入力します。`https://sso2.orgchartnow.com`

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[OrgChart Now のドメインと URL] のシングル サインオン情報](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    **[サインオン URL]** ボックスに、`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com` のパターンを使用して URL を入力します。
     
    > [!NOTE]
    > `<YourEntityID>` は、このチュートリアルで後ほど説明する [クイック リファレンス] セクションからコピーした SAML エンティティ ID です。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
1. **[OrgChart Now Configuration]\(OrgChart Now 構成\)** セクションで、**[Configure OrgChart Now]\(OrgChart Now を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーし、これを使用して **[OrgChart Now Domain and URLs]\(OrgChart Now のドメインと URL\)** セクションの**サインオン URL** を完成させます。

    ![OrgChart Now の構成](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

1. **OrgChart Now** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [OrgChart Now サポート チーム](mailto:ocnsupport@officeworksoftware.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/orgchartnow-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/orgchartnow-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/orgchartnow-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-orgchart-now-test-user"></a>OrgChart Now のテスト ユーザーの作成

Azure AD ユーザーが OrgChart Now にログインできるようにするには、そのユーザーを OrgChart Now にプロビジョニングする必要があります。 

1. OrgChart Now では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 OrgChart Now にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。 Just-In-Time ユーザー プロビジョニング機能では、認識されている IDP から SSO 要求が届いたとき、SAML アサーションの電子メールがユーザーの一覧に見つからない場合にのみ**読み取り専用**ユーザーが作成されます。 この自動プロビジョニング機能のために、OrgChart Now で「**全般**」というタイトルのアクセス グループを作成する必要があります。 次の手順でアクセス グループを作成してください。

    a. UI の右上隅にある**歯車**をクリックし、**[グループの管理]** オプションに進みます。

    ![OrgChart Now のグループ](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. **[追加]** アイコンを選択し、グループに「**全般**」という名前を付け、**[OK]** をクリックします。 

    ![OrgChart Now の [追加]](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. 全般 (読み取り) ユーザーがアクセスできるフォルダーを選択します。

    ![OrgChart Now のフォルダー](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. 管理者ユーザーのみが変更できるようにフォルダーを**ロックします**。 **[OK]** をクリックします。

    ![OrgChart Now のロック機能](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

1. **管理者**ユーザーと**読み取り/書き込み**ユーザーを作成するには、SSO 経由で特権レベルへのアクセスを取得する目的でユーザーを手動作成する必要があります。 ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

    a. セキュリティ管理者として OrgChart Now にログインします。

    b.  右上隅にある **[設定]** をクリックし、**[ユーザーの管理]** に移動します。

    ![OrgChart Now の設定](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. **[追加]** をクリックし、次の手順を実行します。

    ![OrgChart Now の管理](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * **[ユーザー ID]** テキストボックスに **brittasimon@contoso.com** のようなユーザー ID を入力します。

    * **[電子メール アドレス]** ボックスに、ユーザーの電子メール アドレスを入力します (この例では **brittasimon@contoso.com**)。

    * **[追加]** をクリックします。
    
### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に OrgChart Now へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**OrgChart Now に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[OrgChart Now]** を選択します。

    ![アプリケーションの一覧の [OrgChart Now] リンク](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [OrgChart Nowt] タイルをクリックすると、自動的に OrgChart Now アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png

