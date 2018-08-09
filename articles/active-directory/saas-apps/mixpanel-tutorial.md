---
title: 'チュートリアル: Azure Active Directory と Mixpanel の統合 | Microsoft Docs'
description: Azure Active Directory と Mixpanel の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 9ec0b27defdc4c859415e78e1cb6e43f5ed0b208
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435079"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>チュートリアル: Azure Active Directory と Mixpanel の統合

このチュートリアルでは、Mixpanel と Azure Active Directory (Azure AD) を統合する方法について説明します。

Mixpanel と Azure AD の統合には、次の利点があります。

- Mixpanel にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Mixpanel にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Mixpanel と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Mixpanel でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Mixpanel を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-mixpanel-from-the-gallery"></a>ギャラリーから Mixpanel を追加する
Azure AD への Mixpanel の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Mixpanel を追加する必要があります。

**ギャラリーから Mixpanel を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Mixpanel**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/mixpanel-tutorial/tutorial_mixpanel_search.png)

1. 結果ウィンドウで **[Mixpanel]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/mixpanel-tutorial/tutorial_mixpanel_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Mixpanel で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Mixpanel ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Mixpanel の関連ユーザーの間で、リンク関係が確立されている必要があります。

Mixpanel で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Mixpanel で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Mixpanel テスト ユーザーの作成](#creating-a-mixpanel-test-user)** - Mixpanel で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure AD のシングル サインオンを Azure Portal で有効にし、Mixpanel アプリケーションでシングル サインオンを構成します。

**Mixpanel で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Mixpanel** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/mixpanel-tutorial/tutorial_mixpanel_samlbase.png)

1. **[Mixpanel Domain and URLs]\(Mixpanel のドメインと URL\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/mixpanel-tutorial/tutorial_mixpanel_url.png)

     **[サインオン URL]** ボックスに、「`https://mixpanel.com/login/`」と入力します。

    > [!NOTE] 
    > [https://mixpanel.com/register/](https://mixpanel.com/register/) で登録してログイン資格情報を設定し、[Mixpanel サポート チーム](mailto:support@mixpanel.com)に連絡してテナントの SSO 設定を有効にしてください。 必要であれば、Mixpanel サポート チームはサインオン URL 値も提供します。 
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/mixpanel-tutorial/tutorial_mixpanel_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/mixpanel-tutorial/tutorial_general_400.png)

1. **[Mixpanel Configuration]\(Mixpanel 構成\)** セクションで、**[Configure Mixpanel]\(Mixpanel を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/mixpanel-tutorial/tutorial_mixpanel_configure.png) 

1. 別のブラウザー ウィンドウで、管理者として Mixpanel アプリケーションにサインオンします。

1. ページの下部の左隅にある小さな **歯車** アイコンをクリックします。 
   
    ![Mixpanel シングル サインオン](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

1. **[Access security]** タブをクリックし、**[Change settings]** をクリックします。
   
    ![Mixpanel 設定](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

1. **[Change your certificate]\(証明書の変更\)** ダイアログ ページで、**[ファイルの選択]** をクリックしてダウンロードした証明書をアップロードし、**[次へ]** をクリックします。
   
    ![Mixpanel 設定](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

1.  **[Change your authentication URL]\(認証 URL の変更\)** ダイアログ ページの [authentication URL]\(認証 URL\) ボックスで、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けて **[次へ]** をクリックします。
   
   ![Mixpanel 設定](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

1. **[Done]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/mixpanel-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/mixpanel-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/mixpanel-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/mixpanel-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-mixpanel-test-user"></a>Mixpanel テスト ユーザーの作成

このセクションの目的は、Mixpanel で Britta Simon というユーザーを作成することです。 

1. Mixpanel 企業サイトに管理者としてサインオンします。

1. ページの下部の左隅にある小さな歯車ボタンをクリックし、 **[設定]** ウィンドウを開きます。

1. **[チーム]** タブをクリックします。

1. **[Invite a team member by email]** ボックスに、Azure での Britta の 電子メール アドレスを入力します。
   
    ![Mixpanel 設定](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

1. **[招待]** をクリックします。 

> [!Note]
> プロファイルを設定するための電子メールがユーザーに届きます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Mixpanel へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Mixpanel に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Mixpanel]** を選択します。

    ![Configure single sign-on](./media/mixpanel-tutorial/tutorial_mixpanel_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Mixpanel のタイルをクリックすると、自動的に Mixpanel アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/mixpanel-tutorial/tutorial_general_04.png

[100]: ./media/mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/mixpanel-tutorial/tutorial_general_201.png
[202]: ./media/mixpanel-tutorial/tutorial_general_202.png
[203]: ./media/mixpanel-tutorial/tutorial_general_203.png

