---
title: 'チュートリアル: Azure Active Directory と ScreenSteps の統合 | Microsoft Docs'
description: Azure Active Directory と ScreenSteps の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 105ec895635a882d562de48203222702a2c6bfed
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421748"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>チュートリアル: Azure Active Directory と ScreenSteps の統合

このチュートリアルでは、ScreenSteps と Azure Active Directory (Azure AD) を統合する方法について説明します。

ScreenSteps と Azure AD の統合には、次の利点があります。

- ScreenSteps にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に ScreenSteps にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ScreenSteps と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ScreenSteps でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ScreenSteps の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-screensteps-from-the-gallery"></a>ギャラリーからの ScreenSteps の追加
Azure AD への ScreenSteps の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから ScreenSteps を追加する必要があります。

**ギャラリーから ScreenSteps を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**ScreenSteps**」と入力し、結果ウィンドウで **[ScreenSteps]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの ScreenSteps](./media/screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ScreenSteps で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ScreenSteps ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ScreenSteps の関連ユーザーの間で、リンク関係が確立されている必要があります。

ScreenSteps で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

ScreenSteps で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[ScreenSteps テスト ユーザーの作成](#create-a-screensteps-test-user)** - ScreenSteps で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、ScreenSteps アプリケーションでシングル サインオンを構成します。

**ScreenSteps で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ScreenSteps** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/screensteps-tutorial/tutorial_screensteps_samlbase.png)

1. **[ScreenSteps のドメインと URL]** セクションで、次の手順を実行します。

    ![[ScreenSteps のドメインと URL] のシングル サインオン情報](./media/screensteps-tutorial/tutorial_screensteps_url.png)

    **[サインオン URL]** ボックスに、`https://<tenantname>.ScreenSteps.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL に値を置き換えます。実際の値については後で説明します。 

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/screensteps-tutorial/tutorial_screensteps_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/screensteps-tutorial/tutorial_general_400.png)

1. **[ScreenSteps Configuration]\(ScreenSteps 構成\)** セクションで、**[Configure ScreenSteps]\(ScreenSteps を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![ScreenSteps の構成](./media/screensteps-tutorial/tutorial_screensteps_configure.png) 

1. 別の Web ブラウザー ウィンドウで、ScreenSteps 企業サイトに管理者としてログインします。

1. **[Account Settings]\(アカウント設定\)** をクリックします。

    ![Account management](./media/screensteps-tutorial/ic778523.png "Account management")

1. **[Single Sign-on]** をクリックします。

    ![Remote authentication](./media/screensteps-tutorial/ic778524.png "Remote authentication")

1. **[シングル サインオン エンドポイントの作成]** をクリックします。

    ![Remote authentication](./media/screensteps-tutorial/ic778525.png "Remote authentication")

1. **[シングル サインオン エンドポイントの作成]** セクションで、次の手順に従います。

    ![Create an authentication endpoint](./media/screensteps-tutorial/ic778526.png "Create an authentication endpoint")
    
    a. **[Title]** テキスト ボックスに、タイトルを入力します。
    
    b. **[Mode]** 一覧から **[SAML]** を選択します。
    
    c. **Create** をクリックしてください。

1. 新しいエンドポイントを**編集**します。

    ![エンドポイントの編集](./media/screensteps-tutorial/ic778528.png "Edit endpoint")

1. **[シングル サインオン エンドポイントの編集]** セクションで、次の手順に従います。

    ![Remote authentication endpoint](./media/screensteps-tutorial/ic778527.png "Remote authentication endpoint")

    a. **[Upload new SAML Certificate file]\(新しい SAML 証明書のアップロード\)** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。
    
    b. Azure Portal からコピーした **SAML シングル サインオン サービスの URL** を **[Remote Login URL]\(Remote ログイン URL\)** ボックスに貼り付けます。
    
    c. Azure Portal からコピーした **サインアウト URL** を **[Logout URL]\(ログアウト URL\)** ボックスに貼り付けます。
    
    d. **[グループ]** を選択し、プロビジョニングされたときにユーザーを割り当てます。
    
    e. **[Update]** をクリックします。

    f. **SAML コンシューマー URL** をクリップボードにコピーし、**[ScreenSteps Domain and URLs]\(ScreenSteps ドメインおよび URL\)** セクションの **[Sign-on URL]\(サインオン URL\)** ボックスに貼り付けます。
    
    g. **[シングル サインオン エンドポイントの編集]** に戻ります。
    
    h. ScreenSteps にログインするすべてのユーザーについて、このエンドポイントを使用するには、**[アカウントの既定値にする]** ボタンをクリックします。 または、**[サイトに追加]** ボタンをクリックして、**ScreenSteps** 内の特定のサイトにこのエンドポイントを使用することもできます。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/screensteps-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/screensteps-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/screensteps-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/screensteps-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-screensteps-test-user"></a>ScreenSteps テスト ユーザーの作成

このセクションでは、ScreenSteps で Britta Simon というユーザーを作成します。 [ScreenSteps クライアント サポート チーム](https://www.screensteps.com/contact)と協力して、ScreenSteps プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ScreenSteps へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**ScreenSteps に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[ScreenSteps]** を選択します。

    ![アプリケーションの一覧の ScreenSteps のリンク](./media/screensteps-tutorial/tutorial_screensteps_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで ScreenSteps のタイルをクリックすると、自動的に ScreenSteps アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/screensteps-tutorial/tutorial_general_01.png
[2]: ./media/screensteps-tutorial/tutorial_general_02.png
[3]: ./media/screensteps-tutorial/tutorial_general_03.png
[4]: ./media/screensteps-tutorial/tutorial_general_04.png

[100]: ./media/screensteps-tutorial/tutorial_general_100.png

[200]: ./media/screensteps-tutorial/tutorial_general_200.png
[201]: ./media/screensteps-tutorial/tutorial_general_201.png
[202]: ./media/screensteps-tutorial/tutorial_general_202.png
[203]: ./media/screensteps-tutorial/tutorial_general_203.png

