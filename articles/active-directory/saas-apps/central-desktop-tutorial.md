---
title: 'チュートリアル: Azure Active Directory と Central Desktop の統合 | Microsoft Docs'
description: Azure Active Directory と Central Desktop の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 82a6911c85dd1438aa8f60cb36194a2916bc91e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429048"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>チュートリアル: Azure Active Directory と Central Desktop の統合

このチュートリアルでは、Central Desktop と Azure Active Directory (Azure AD) を統合する方法について説明します。

Central Desktop と Azure AD の統合には、次の利点があります。

- Central Desktop にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Central Desktop にサインオンできるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Central Desktop の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Central Desktop でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使わないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使わないでください。
- Azure AD の評価環境がまだない場合は、[1 か月の無料試用版を入手します](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Central Desktop の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="add-central-desktop-from-the-gallery"></a>ギャラリーから Central Desktop を追加する
Azure AD への Central Desktop の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Central Desktop を追加する必要があります。

**ギャラリーから Central Desktop を追加するには、次の手順を実行します。**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン][3]

1. **検索ボックス**に、「Central Desktop」と入力します。 結果パネルから **[Central Desktop]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧での Central Desktop](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Central Desktop で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Central Desktop ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Central Desktop の関連ユーザーの間で、リンクが確立されている必要があります。

Central Desktop で、**Username** に Azure AD の**ユーザー名**と同じ値を指定します。 これで、2 人のユーザー間にリンクが確立されました。

Central Desktop で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
1. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
1. [Central Desktop のテスト ユーザーの作成](#create-a-central-desktop-test-user) - Central Desktop で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Central Desktop アプリケーションでシングル サインオンを構成します。

**Central Desktop で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Central Desktop** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

1. シングル サインオンを有効にするには、**[シングル サインオン]** ダイアログ ボックスの **[モード]** ドロップダウン リストで **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

1. **[Central Desktop のドメインと URL]** セクションで、次の手順を実行します。

    ![[Central Desktop のドメインと URL] のシングル サインオン情報](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.centraldesktop.com` というパターンで URL を入力します。

    b. **[識別子]** ボックスに、次のパターンで URL を入力します。
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. **[応答 URL]** ボックスに、`https://<companyname>.centraldesktop.com/saml2-assertion.php` の形式で URL を入力します。    
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Central Desktop クライアント サポート チーム](https://imeetcentral.com/contact-us)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[証明書]** を選択します。 コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

1. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/central-desktop-tutorial/tutorial_general_400.png)
    
1. **[Central Desktop 構成]** セクションで、**[Central Desktop の構成]** を選び、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Central Desktop の構成](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

1. **Central Desktop** テナントにサインインします。

1. **[設定]** に移動します。 **[Advanced]\(詳細\)** を選び、**[Single Sign On]\(シングル サインオン\)** を選びます。

    ![Setup - Advanced](./media/central-desktop-tutorial/ic769563.png "Setup - Advanced")

1. **[Single Sign On Settings]\(シングル サインオンの設定\)** ページで、次の手順を実行します。

    ![シングル サインオンの設定](./media/central-desktop-tutorial/ic769564.png "シングル サインオンの設定")
    
    a. **[Enable SAML v2 Single Sign On]** チェック ボックスをオンにします。
    
    b. **[SSO URL]\(SSO URL\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
    
    c. **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
    
    d. **[SSO Logout URL]\(SSO ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

1. **[Message Signature Verification Method]\(メッセージ署名検証方法\)** セクションで、次の手順を実行します。

    ![メッセージ署名検証方法](./media/central-desktop-tutorial/ic769565.png "メッセージ署名検証方法") a. **[Certificate]** を選択します。
    
    b. **[SSO Certificate]\(SSO 証明書\)** ボックスの一覧で、**[RSH SHA256]** を選びます。
    
    c. ダウンロードした証明書をメモ帳で開きます。 証明書の内容をコピーして、**[SSO Certificate]\(SSO 証明書\)** フィールドに貼り付けます。
        
    d. **[Display a link to your SAMLv2 login page]** チェック ボックスをオンにします。
    
    e. **[Update]\(更新\)** を選択します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。 **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/central-desktop-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動します。 次に、**[すべてのユーザー]** を選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/central-desktop-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/central-desktop-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-central-desktop-test-user"></a>Central Desktop のテスト ユーザーを作成する

Azure AD ユーザーがサインインできるようにするには、ユーザーを Central Desktop アプリケーションにプロビジョニングする必要があります。 このセクションでは、Central Desktop で Azure AD ユーザー アカウントを作成する方法について説明します。

> [!NOTE]
> Azure AD ユーザー アカウントをプロビジョニングするには、Central Desktop から提供されている他の Central Desktop ユーザー アカウント作成ツールまたは API を使います。

**Central Desktop にユーザー アカウントをプロビジョニングするには:**

1. Central Desktop テナントにサインインします。

1. **[People]\(ユーザー\)** > **[Internal Members]\(内部メンバー\)** の順に移動します。

1. **[Add Internal Members]\(内部メンバーの追加\)** を選びます。

    ![ユーザー](./media/central-desktop-tutorial/ic781051.png "People")
    
1. **[Email Address of New Members]\(新しいメンバーの電子メール アドレス\)** ボックスにプロビジョニングする Azure AD アカウントを入力し、**[Next]\(次へ\)** を選びます。

    ![新しいメンバーの電子メール アドレス](./media/central-desktop-tutorial/ic781052.png "新しいメンバーの電子メール アドレス")

1. **[Add Internal member(s)]\(内部メンバーの追加\)** を選びます。

    ![内部メンバーの追加](./media/central-desktop-tutorial/ic781053.png "内部メンバーの追加")
   
   >[!NOTE]
   >追加したユーザーが、アカウント アクティブ化のための確認リンクを含むメールを受け取ります。
   
### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon というユーザーに Central Desktop へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Central Desktop に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルで、アプリケーション ビューを開きます。 ディレクトリ ビューに移動し、**[エンタープライズ アプリケーション]** に移動します。

1. **[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Central Desktop]** を選択します。

    ![アプリケーションの一覧の [Central Desktop] リンク](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンを選びます。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[ユーザー]** 一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

1. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Central Desktop] タイルを選ぶと、自動的に Central Desktop アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

