---
title: 'チュートリアル: Azure Active Directory と Lucidchart の統合 | Microsoft Docs'
description: Azure Active Directory と Lucidchart の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 45dbf350bc874d48b077ba8f7d67819eff741df2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448042"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>チュートリアル: Azure Active Directory と Lucidchart の統合

このチュートリアルでは、Lucidchart と Azure Active Directory (Azure AD) を統合する方法について説明します。

Lucidchart と Azure AD の統合には、次の利点があります。

- Lucidchart にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Lucidchart にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Lucidchart と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Lucidchart でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Lucidchart の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-lucidchart-from-the-gallery"></a>ギャラリーからの Lucidchart の追加
Azure AD への Lucidchart の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lucidchart を追加する必要があります。

**ギャラリーから Lucidchart を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Lucidchart**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/lucidchart-tutorial/tutorial_lucidchart_search.png)

1. 結果ウィンドウで **Lucidchart** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/lucidchart-tutorial/tutorial_lucidchart_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Lucidchart で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Lucidchart ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Lucidchart の関連ユーザーの間で、リンク関係が確立されている必要があります。

Lucidchart で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Lucidchart で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Lucidchart テスト ユーザーの作成](#creating-a-lucidchart-test-user)** - Lucidchart で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Lucidchart アプリケーションでシングル サインオンを構成します。

**Lucidchart で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Lucidchart** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_lucidchart_samlbase.png)

1. **[Lucidchart のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_lucidchart_url.png)

    **[サインオン URL]** ボックスに、「`https://chart2.office.lucidchart.com/saml/sso/azure`」と入力します。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_lucidchart_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザー ウィンドウで、Lucidchart の企業サイトに管理者としてログインします。

1. 上部のメニューで **[チーム]** をクリックします。
   
    ![Team](./media/lucidchart-tutorial/ic791190.png "Team")

1. **[アプリケーション] \> [SAML の管理]** をクリックします。
   
    ![Manage SAML](./media/lucidchart-tutorial/ic791191.png "Manage SAML")

1. **[SAML Authentication Settings]** ダイアログ ページで、次の手順に従います。
   
    a. **[SAML 認証を有効にする]** を選んで、**[オプション]** をクリックします。

    ![SAML Authentication Settings](./media/lucidchart-tutorial/ic791192.png "SAML Authentication Settings")
 
    b. **[ドメイン]** ボックスで、使用するドメインを入力して、**[証明書の変更]** をクリックします。

    ![Change Certificate](./media/lucidchart-tutorial/ic791193.png "Change Certificate")
 
    c. ダウンロードしたメタデータ ファイルを開いて内容をコピーし、 **[Upload Metadata (メタデータのアップロード)]** ボックスに貼り付けます。

    ![Upload Metadata](./media/lucidchart-tutorial/ic791194.png "Upload Metadata")
 
    d. **[Automatically Add new users to the team]\(新しいユーザーを自動的にチームに追加する\)** を選んで、**[Save changes]\(変更を保存\)** をクリックします。

    ![変更を保存](./media/lucidchart-tutorial/ic791195.png "Save Changes")

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/lucidchart-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/lucidchart-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/lucidchart-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/lucidchart-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-lucidchart-test-user"></a>Lucidchart テスト ユーザーの作成

Lucidchart へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  割り当て済みユーザーがアクセス パネルを使用して Lucidchart にログインしようとすると、そのユーザーが存在するかどうかが Lucidchart によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lucidchart により自動的に作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Lucidchart へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Lucidchart に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Lucidchart]** を選択します。

    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_lucidchart_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Lucidchart のタイルをクリックすると、自動的に Lucidchart アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lucidchart-tutorial/tutorial_general_01.png
[2]: ./media/lucidchart-tutorial/tutorial_general_02.png
[3]: ./media/lucidchart-tutorial/tutorial_general_03.png
[4]: ./media/lucidchart-tutorial/tutorial_general_04.png

[100]: ./media/lucidchart-tutorial/tutorial_general_100.png

[200]: ./media/lucidchart-tutorial/tutorial_general_200.png
[201]: ./media/lucidchart-tutorial/tutorial_general_201.png
[202]: ./media/lucidchart-tutorial/tutorial_general_202.png
[203]: ./media/lucidchart-tutorial/tutorial_general_203.png

