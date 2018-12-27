---
title: 'チュートリアル: Azure Active Directory と Sciforma の統合 | Microsoft Docs'
description: Azure Active Directory と Sciforma の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: abbfb5ac-7687-4153-b263-8090102dae37
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 4dc7c9c9c702db7c0a10b89d33169a97541f3b30
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716442"
---
# <a name="tutorial-azure-active-directory-integration-with-sciforma"></a>チュートリアル: Azure Active Directory と Sciforma の統合

このチュートリアルでは、Sciforma と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sciforma と Azure AD の統合には、次の利点があります。

- Sciforma にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Sciforma にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Sciforma の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Sciforma でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Sciforma の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sciforma-from-the-gallery"></a>ギャラリーからの Sciforma の追加
Azure AD への Sciforma の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sciforma を追加する必要があります。

**ギャラリーから Sciforma を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Sciforma**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/sciforma-tutorial/tutorial_sciforma_search.png)

1. 結果ウィンドウで **[Sciforma]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/sciforma-tutorial/tutorial_sciforma_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Sciforma で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Sciforma ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Sciforma の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sciforma で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Sciforma で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Sciforma テスト ユーザーの作成](#creating-a-sciforma-test-user)** - Sciforma で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Sciforma アプリケーションでシングル サインオンを構成します。

**Sciforma で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Sciforma** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/sciforma-tutorial/tutorial_sciforma_samlbase.png)

1. **[Sciforma のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/sciforma-tutorial/tutorial_sciforma_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.sciforma.net/sciforma/main.html` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.sciforma.net/sciforma/saml` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Sciforma クライアント サポート チーム](https://www.sciforma.com/about/contact)に問い合わせてください。 
 


1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/sciforma-tutorial/tutorial_sciforma_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/sciforma-tutorial/tutorial_general_400.png)

1. **Sciforma** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [Sciforma サポート チーム](https://www.sciforma.com/about/contact)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/sciforma-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/sciforma-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/sciforma-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/sciforma-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-sciforma-test-user"></a>Sciforma テスト ユーザーの作成

Sciforma へのユーザー プロビジョニングの構成にあたって必要な操作はありません。 割り当て済みユーザーがアクセス パネルを使用して Sciforma にログインしようとすると、そのユーザーが存在するかどうかが Sciforma によって確認されます。  

* 使用可能なユーザー アカウントがない場合、ユーザー アカウントは Sciforma により自動的に作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sciforma へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Sciforma に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Sciforma]** を選択します。

    ![Configure single sign-on](./media/sciforma-tutorial/tutorial_sciforma_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sciforma-tutorial/tutorial_general_01.png
[2]: ./media/sciforma-tutorial/tutorial_general_02.png
[3]: ./media/sciforma-tutorial/tutorial_general_03.png
[4]: ./media/sciforma-tutorial/tutorial_general_04.png

[100]: ./media/sciforma-tutorial/tutorial_general_100.png

[200]: ./media/sciforma-tutorial/tutorial_general_200.png
[201]: ./media/sciforma-tutorial/tutorial_general_201.png
[202]: ./media/sciforma-tutorial/tutorial_general_202.png
[203]: ./media/sciforma-tutorial/tutorial_general_203.png

