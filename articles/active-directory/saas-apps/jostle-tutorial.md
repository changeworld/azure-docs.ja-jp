---
title: 'チュートリアル: Azure Active Directory と Jostle の統合 | Microsoft Docs'
description: Azure Active Directory と Jostle の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9ca4ca1f-8f68-4225-81a6-1666b486d6a8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: b988d908b995b1144837c8642a8864a87e2fc61a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437528"
---
# <a name="tutorial-azure-active-directory-integration-with-jostle"></a>チュートリアル: Azure Active Directory と Jostle の統合

このチュートリアルでは、Jostle と Azure Active Directory (Azure AD) を統合する方法について説明します。

Jostle と Azure AD の統合には、次の利点があります。

- Jostle にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Jostle にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Jostle の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Jostle でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Jostle の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-jostle-from-the-gallery"></a>ギャラリーからの Jostle の追加
Azure AD への Jostle の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Jostle を追加する必要があります。

**ギャラリーから Jostle を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]

1. ウィンドウ上部の **[追加]** をクリックします。

    ![add_01](./media/jostle-tutorial/add_01.png)

1. **[アプリケーションの追加]** の下にある検索ボックスに「**Jostle**」と入力します。

    ![add_02](./media/jostle-tutorial/add_02.png)

1. 結果ウィンドウで **[Jostle]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/jostle-tutorial/tutorial_jostle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Jostle で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Jostle ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Jostle の関連ユーザーの間で、リンク関係が確立されている必要があります。

Jostle で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Jostle で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Jostle テスト ユーザーの作成](#creating-a-jostle-test-user)** - Jostle で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Jostle アプリケーションでシングル サインオンを構成します。

**Jostle で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Jostle** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/jostle-tutorial/tutorial_jostle_samlbase.png)

1. **[Jostle Domain and URLs]\(Jostle のドメインと URL\)** セクションで、次の手順を実行します。

    ![url_01](./media/jostle-tutorial/url_01.png)

    a. **[サインオン URL]** ボックスに、「`https://login-prod.jostle.us`」と入力します。

    b. **[識別子]** ボックスに、「`https://jostle.us`」と入力します。

    c. **[詳細な URL 設定の表示]** チェック ボックスをオンにします。

    d. **[応答 URL]** ボックスに、「`https://login-prod.jostle.us/saml/SSO/alias/newjostle.us`」と入力します。

1. **[ユーザー属性]** セクションで、**[ユーザー識別子]** フィールドに「`user.userprincipalname`」と入力します。

    ![url_02](./media/jostle-tutorial/url_02.png)

1. ウィンドウの上部にある **[保存]** をクリックします。

1. **[SAML 署名証明書]** に移動し、**[アクティブ]** に設定されていることを確認します。 **[メタデータ XML]** をクリックし、メタデータ ファイルをダウンロードします。

    ![url_03](./media/jostle-tutorial/url_03.png)

1. Jostle 側にシングル サインオンを構成するには、ダウンロードしたメタデータ XML を [Jostle サポート チーム](mailto:support@jostle.me)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
>

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/jostle-tutorial/create_aaduser_01.png)

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/jostle-tutorial/create_aaduser_02.png)

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/jostle-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/jostle-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-jostle-test-user"></a>Jostle のテスト ユーザーの作成

このセクションでは、Jostle で Britta Simon というユーザーを作成します。 Jostle に Britta Simon を追加する方法がわからない場合は、[Jostle サポート チーム](mailto:support@jostle.me)に連絡し、テスト ユーザーを追加して SSO を有効にしてください。

> [!NOTE]
> Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Jostle へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200]

**Jostle に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[Jostle]** を選択します。

    ![Configure single sign-on](./media/jostle-tutorial/tutorial_jostle_app.png)

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Jostle のタイルをクリックすると、Jostle アプリケーションのページに自動的にログインします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/jostle-tutorial/tutorial_general_01.png
[2]: ./media/jostle-tutorial/tutorial_general_02.png
[3]: ./media/jostle-tutorial/tutorial_general_03.png
[4]: ./media/jostle-tutorial/tutorial_general_04.png

[100]: ./media/jostle-tutorial/tutorial_general_100.png

[200]: ./media/jostle-tutorial/tutorial_general_200.png
[201]: ./media/jostle-tutorial/tutorial_general_201.png
[202]: ./media/jostle-tutorial/tutorial_general_202.png
[203]: ./media/jostle-tutorial/tutorial_general_203.png
