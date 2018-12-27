---
title: 'チュートリアル: Azure Active Directory と Promapp の統合 | Microsoft Docs'
description: Azure Active Directory と Promapp の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: 6bcd1add3985112fe60aab22f1799e40ad8889b2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421864"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>チュートリアル: Azure Active Directory と Promapp の統合

このチュートリアルでは、Promapp と Azure Active Directory (Azure AD) を統合する方法について説明します。

Promapp と Azure AD の統合には、次の利点があります。

- Promapp にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Promapp にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Promapp と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Promapp でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Promapp を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-promapp-from-the-gallery"></a>ギャラリーから Promapp を追加する
Azure AD への Promapp の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Promapp を追加する必要があります。

**ギャラリーから Promapp を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「 **Promapp**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/promapp-tutorial/tutorial_promapp_search.png)

1. 結果ウィンドウで **[Promapp]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Promapp で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Promapp ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Promapp の関連ユーザーの間で、リンク関係が確立されている必要があります。

Promapp で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Promapp で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Promapp のテスト ユーザーの作成](#creating-a-promapp-test-user)** - Promapp で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Promapp アプリケーションでシングル サインオンを構成します。

**Promapp で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Promapp** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/promapp-tutorial/tutorial_promapp_samlbase.png)

1. **[Promapp のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![Configure single sign-on](./media/promapp-tutorial/tutorial_promapp_url.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。
    | |
    |--|
    | `https://go.promapp.com/TENANTNAME/`|
    | `https://au.promapp.com/TENANTNAME/`|
    | `https://us.promapp.com/TENANTNAME/`|
    | `https://eu.promapp.com/TENANTNAME/`|
    | `https://ca.promapp.com/TENANTNAME/`|
    
    > [!NOTE] 
    > 現在、Promapp との Azure AD の統合は、サービスで開始された認証についてのみ構成されています。たとえば、Promapp URL にアクセスすると、認証プロセスが開始します。 ただし、応答 URL は必須フィールドです。
    
    b. **[応答 URL]** ボックスに、`https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![Configure single sign-on](./media/promapp-tutorial/tutorial_promapp_url1.png)

    **[サインオン URL]** ボックスに、`https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Promapp クライアント サポート チーム](https://www.promapp.com/about-us/contact-us/)に連絡してください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/promapp-tutorial/tutorial_promapp_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/promapp-tutorial/tutorial_general_400.png)

1. **[Promapp Configuration] (Promapp 構成)** セクションで、**[Configure Promapp] (Promapp を構成する)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/promapp-tutorial/tutorial_promapp_configure.png) 

1. Promapp 企業サイトに管理者としてサインオンします。 

1. 上部のメニューで **[Admin]** をクリックします。 
   
    ![Azure AD Single Sign-On][12]

1. **[構成]** をクリックします。 
   
    ![Azure AD Single Sign-On][13]

1. **[Security]** ダイアログで、次の手順を実行します。
   
    ![Azure AD Single Sign-On][14]
    
    a. Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[SSO-Login URL]** ボックスに貼り付けます。
    
    b. **[SSO - Single Sign-on Mode]** として **[Optional]** を選択し、**[Save]** をクリックします。

    > [!NOTE]
    > **Optional** モードはテスト目的専用です。 構成に問題がなければ、**Required** モードを選択し、Azure AD を使用してすべてのユーザーに認証を強制します。

    c. ダウンロードした証明書をメモ帳で開き、最初の行 (----**BEGIN CERTIFICATE**-----) と最後の行 (-----**END CERTIFICATE**-----) を除く証明書の内容をコピーして、**[SSO-x.509 Certificate]** に貼り付け、**[Save]** をクリックします。
        
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/promapp-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/promapp-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/promapp-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/promapp-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-promapp-test-user"></a>Promapp のテスト ユーザーの作成

Promapp アプリケーションでは、ジャストインタイム プロビジョニングがサポートされています。 そのため、アクセス パネルを使用してアプリケーションにアクセスを試みると、必要に応じてユーザー アカウントが自動的に作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Promapp へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Promapp に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Promapp]** を選択します。

    ![Configure single sign-on](./media/promapp-tutorial/tutorial_promapp_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

**SP** 開始モードでアプリケーションをテストするには、Promapp サイトから認証を開始する必要があります。 これは、**[Optional]\(オプション\)** モードを有効にした状態で、ログイン ページの [Login with Single Sign-on]\(シングル サインオンでログイン\) ボタンをクリックすることにより、行うことができます。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promapp-tutorial/tutorial_general_01.png
[2]: ./media/promapp-tutorial/tutorial_general_02.png
[3]: ./media/promapp-tutorial/tutorial_general_03.png
[4]: ./media/promapp-tutorial/tutorial_general_04.png
[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/promapp-tutorial/tutorial_general_100.png

[200]: ./media/promapp-tutorial/tutorial_general_200.png
[201]: ./media/promapp-tutorial/tutorial_general_201.png
[202]: ./media/promapp-tutorial/tutorial_general_202.png
[203]: ./media/promapp-tutorial/tutorial_general_203.png

