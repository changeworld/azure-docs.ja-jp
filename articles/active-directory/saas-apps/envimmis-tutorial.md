---
title: 'チュートリアル: Azure Active Directory と Envi MMIS の統合 | Microsoft Docs'
description: Azure Active Directory と Envi MMIS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 96168dcb8400d2580d0b64257ceb861c1da3ff65
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447287"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>チュートリアル: Azure Active Directory と Envi MMIS の統合

このチュートリアルでは、Envi MMIS と Azure Active Directory (Azure AD) を統合する方法について説明します。

Envi MMIS と Azure AD の統合には、次の利点があります。

- Envi MMIS にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Envi MMIS にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Envi MMIS と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Envi MMIS でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Envi MMIS の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-envi-mmis-from-the-gallery"></a>ギャラリーからの Envi MMIS の追加
Azure AD への Envi MMIS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Envi MMIS を追加する必要があります。

**ギャラリーから Envi MMIS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Envi MMIS**」と入力し、結果ウィンドウで **Envi MMIS** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Envi MMIS](./media/envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Envi MMIS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Envi MMIS ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Envi MMIS の関連ユーザーの間で、リンク関係が確立されている必要があります。

Envi MMIS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Envi MMIS のテスト ユーザーの作成](#create-an-envi-mmis-test-user)** - Envi MMIS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Envi MMIS アプリケーションでシングル サインオンを構成します。

**Envi MMIS で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Envi MMIS** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/envimmis-tutorial/tutorial_envimmis_samlbase.png)

1. アプリケーションを **IDP** 開始モードで構成する場合は、**[Envi MMIS のドメインと URL]** セクションで次の手順を実行します。

    ![[Envi MMIS のドメインと URL] のシングル サインオン情報](./media/envimmis-tutorial/tutorial_envimmis_url.png)

    a. **[識別子]** ボックスに、`https://www.<CUSTOMER DOMAIN>.com/Account` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://www.<CUSTOMER DOMAIN>.com/Account/Acs` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Envi MMIS のドメインと URL] のシングル サインオン情報](./media/envimmis-tutorial/tutorial_envimmis_url1.png)

    **[サインオン URL]** ボックスに、`https://www.<CUSTOMER DOMAIN>.com/Account` のパターンを使用して URL を入力します。
     
    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Envi MMIS クライアント サポート チーム](mailto:support@ioscorp.com)に連絡してください。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/envimmis-tutorial/tutorial_envimmis_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/envimmis-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザー ウィンドウで、Envi MMIS サイトに管理者としてログインします。

1. **[マイ ドメイン]** タブをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/envimmis-tutorial/configure1.png)

1. **[編集]** をクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/envimmis-tutorial/configure2.png)

1. **[Use remote authentication]\(リモート認証を使用する\)** チェック ボックスをオンにして、**[Authentication Type]\(認証の種類\)** ドロップダウンから **[HTTP Redirect]\(HTTP リダイレクト\)** を選びます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/envimmis-tutorial/configure3.png)

1. **[Resources]\(リソース\)** タブを選び、**[Upload Metadata]\(メタデータのアップロード\)** をクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/envimmis-tutorial/configure4.png)

1. **[Upload Metadata]\(メタデータのアップロード\)** ポップアップで、次の手順を実行します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/envimmis-tutorial/configure5.png)

    a. **[Upload From]\(アップロード元\)** ドロップダウンから **[File]\(ファイル\)** オプションを選びます。

    b. **ファイル選択アイコン**を選ぶことにより、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。

    c. **[OK]** をクリックします。

1. ダウンロードしたメタデータ ファイルをアップロードした後、フィールドは自動的に設定されます。 **[Update]\(更新\)** をクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/envimmis-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/envimmis-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/envimmis-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/envimmis-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-envi-mmis-test-user"></a>Envi MMIS テスト ユーザーの作成

Azure AD ユーザーが Envi MMIS にログインできるようにするには、そのユーザーを Envi MMIS にプロビジョニングする必要があります。  
Envi MMIS の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Envi MMIS 企業サイトに管理者としてログインします。

1. **[User List]\(ユーザー一覧\)** タブをクリックします。

    ![従業員の追加](./media/envimmis-tutorial/user1.png)

1. **[Add User]\(ユーザーの追加\)** ボタンをクリックします。

    ![従業員の追加](./media/envimmis-tutorial/user2.png)

1. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![従業員の追加](./media/envimmis-tutorial/user3.png)

    a. **[User Name]\(ユーザー名)** テキストボックスに、Britta Simon のアカウントのユーザー名を入力します (例: **brittasimon@contoso.com**)。
    
    b. **[First Name]\(名\)** テキスト ボックスに、Britta Simon の名を入力します (**Britta**)。

    c. **[Last Name]\(姓\)** テキスト ボックスに、Britta Simon の名を入力します (**Simon**)。

    d. ユーザーの役職を **[Title]\(役職\)** テキスト ボックスに入力します。
    
    e. **[Email Address]\(電子メール アドレス\)** テキストボックスに、Britta Simon のアカウントのメール アドレスを入力します (例: **brittasimon@contoso.com**)。

    f. **[SSO User Name]\(SSO ユーザー名)** テキストボックスに、Britta Simon のアカウントのユーザー名を入力します (例: **brittasimon@contoso.com**)。

    g. **[Save]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Envi MMIS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Envi MMIS に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Envi MMIS]** を選択します。

    ![アプリケーションの一覧の Envi MMIS のリンク](./media/envimmis-tutorial/tutorial_envimmis_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Envi MMIS のタイルをクリックすると、自動的に Envi MMIS アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/envimmis-tutorial/tutorial_general_01.png
[2]: ./media/envimmis-tutorial/tutorial_general_02.png
[3]: ./media/envimmis-tutorial/tutorial_general_03.png
[4]: ./media/envimmis-tutorial/tutorial_general_04.png

[100]: ./media/envimmis-tutorial/tutorial_general_100.png

[200]: ./media/envimmis-tutorial/tutorial_general_200.png
[201]: ./media/envimmis-tutorial/tutorial_general_201.png
[202]: ./media/envimmis-tutorial/tutorial_general_202.png
[203]: ./media/envimmis-tutorial/tutorial_general_203.png

