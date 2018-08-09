---
title: 'チュートリアル: Azure Active Directory と New Relic の統合 | Microsoft Docs'
description: Azure Active Directory と New Relic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: 80bd77504f1b2ab5b6e5c781eadb7c2cd4c99220
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440747"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>チュートリアル: Azure Active Directory と New Relic の統合

このチュートリアルでは、New Relic と Azure Active Directory (Azure AD) を統合する方法について説明します。

New Relic と Azure AD の統合には、次の利点があります。

- New Relic にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に New Relic にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

New Relic と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- New Relic でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの New Relic の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-new-relic-from-the-gallery"></a>ギャラリーからの New Relic の追加
Azure AD への New Relic の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に New Relic を追加する必要があります。

**ギャラリーから New Relic を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**New Relic**」と入力し、結果ウィンドウで **[New Relic]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の New Relic](./media/new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、New Relic で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する New Relic ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと New Relic の関連ユーザーの間で、リンク関係が確立されている必要があります。

New Relic で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

New Relic で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[New Relic テスト ユーザーの作成](#create-a-new-relic-test-user)** - New Relic で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、New Relic アプリケーションでシングル サインオンを構成します。

**New Relic で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **New Relic** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/new-relic-tutorial/tutorial_new-relic_samlbase.png)

1. **[New Relic Domain and URLs]\(New Relic のドメインと URL\)** セクションで、次の手順を実行します。

    ![[New Relic のドメインと URL] のシングル サインオン情報](./media/new-relic-tutorial/tutorial_new-relic_url.png)

    a. **[サインオン URL]** テキスト ボックスに、`https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` のパターンを使用して URL を入力します。必ずご自身の新しい Relic アカウント ID に置き換えてください。

    b. **[識別子]** テキストボックスに、値として「`rpm.newrelic.com`」を入力します。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/new-relic-tutorial/tutorial_new-relic_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/new-relic-tutorial/tutorial_general_400.png)

1. **[New Relic Configuration]\(New Relic 構成\)** セクションで、**[Configure New Relic]\(New Relic を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **クイック リファレンス セクション**から、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![New Relic の構成](./media/new-relic-tutorial/tutorial_new-relic_configure.png) 

1. 別の Web ブラウザーのウィンドウで、管理者として **New Relic** 企業サイトにサインオンします。

1. 上部のメニューで **[アカウント設定]** をクリックします。
   
    ![Account Settings](./media/new-relic-tutorial/ic797036.png "Account Settings")

1. **[セキュリティと認証]** タブをクリックし、**[シングル サインオン]** タブをクリックします。
   
    ![シングル サインオン](./media/new-relic-tutorial/ic797037.png "Single Sign-On")

1. SAML ダイアログ ページで、次の手順に従います。
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
   a. **[ファイルの選択]** をクリックして、ダウンロードした Azure Active Directory 証明書をアップロードします。

   b. **[リモート ログイン URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
   c. **[Logout landing URL]\(ログアウト ランディング URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

   d. **[変更を保存する]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/new-relic-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/new-relic-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/new-relic-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/new-relic-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-new-relic-test-user"></a>New Relic テスト ユーザーを作成する

Azure Active Directory ユーザーが New Relic にログインできるようにするには、そのユーザーを New Relic にプロビジョニングする必要があります。 New Relic の場合、プロビジョニングは手動で行います。

**ユーザー アカウントを New Relic にプロビジョニングするには、次の手順に従います。**

1. **New Relic** 企業サイトに管理者としてログインします。

1. 上部のメニューで **[アカウント設定]** をクリックします。
   
    ![Account Settings](./media/new-relic-tutorial/ic797040.png "Account Settings")

1. **[アカウント]** ペインの左側にある **[概要]** をクリックし、次に **[ユーザーの追加]** をクリックします。
   
    ![Account Settings](./media/new-relic-tutorial/ic797041.png "Account Settings")

1. **[アクティブ ユーザー]** ダイアログで、次の手順に従います。
   
    ![アクティブ ユーザー](./media/new-relic-tutorial/ic797042.png "アクティブ ユーザー")
   
    a. **[電子メール]** テキストボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの電子メール アドレスを入力します。

    b. **[ロール]** として **[ユーザー]** を選びます。

    c. **[このユーザーを追加]** をクリックします。

>[!NOTE]
>他の New Relic ユーザー アカウントの作成ツールまたは New Relic から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に New Relic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**New Relic に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[New Relic]** を選択します。

    ![アプリケーションの一覧の New Relic のリンク](./media/new-relic-tutorial/tutorial_new-relic_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [New Relic] タイルをクリックすると、New Relic アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/new-relic-tutorial/tutorial_general_01.png
[2]: ./media/new-relic-tutorial/tutorial_general_02.png
[3]: ./media/new-relic-tutorial/tutorial_general_03.png
[4]: ./media/new-relic-tutorial/tutorial_general_04.png

[100]: ./media/new-relic-tutorial/tutorial_general_100.png

[200]: ./media/new-relic-tutorial/tutorial_general_200.png
[201]: ./media/new-relic-tutorial/tutorial_general_201.png
[202]: ./media/new-relic-tutorial/tutorial_general_202.png
[203]: ./media/new-relic-tutorial/tutorial_general_203.png

