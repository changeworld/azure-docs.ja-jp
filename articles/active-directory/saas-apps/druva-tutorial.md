---
title: 'チュートリアル: Azure Active Directory と Druva の統合 | Microsoft Docs'
description: Azure Active Directory と Druva の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 985304244acdfafa4fa99dbbe876f35b3e6c58b2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439363"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>チュートリアル: Azure Active Directory と Druva の統合

このチュートリアルでは、Druva と Azure Active Directory (Azure AD) を統合する方法について説明します。

Druva と Azure AD の統合には、次の利点があります。

- Druva にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Druva にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Druva と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Druva でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Druva の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-druva-from-the-gallery"></a>ギャラリーからの Druva の追加
Azure AD への Druva の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Druva を追加する必要があります。

**ギャラリーから Druva を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Druva**」と入力し、結果ウィンドウで **Druva** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Druva](./media/druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Druva で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Druva ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Druva の関連ユーザーの間で、リンク関係が確立されている必要があります。

Druva で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Druva で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Druva のテスト ユーザーの作成](#create-a-druva-test-user)** - Druva で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Druva アプリケーションでシングル サインオンを構成します。

**Druva で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Druva** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/druva-tutorial/tutorial_druva_samlbase.png)

1. **[Druva のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![Configure single sign-on](./media/druva-tutorial/tutorial_druva_url.png)

    **[識別子]** ボックスに、文字列値 `druva-cloud`を入力します。
    
1. **[詳細な URL 設定の表示]** をクリックします。 **SP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![Configure single sign-on](./media/druva-tutorial/tutorial_druva_url1.png)
    
    **[サインオン URL]** テキストボックスに、URL として「`https://cloud.druva.com/home`」と入力します。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/druva-tutorial/tutorial_druva_certificate.png) 

1. Druva アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性**の構成に追加する必要があります。 

    ![Configure single sign-on](./media/druva-tutorial/tutorial_druva_attribute.png)

1. **[Single sign-on]\(シングル サインオン\)** ダイアログの **[User Attributes]\(ユーザー属性\)** セクションで、上記の図に示すように SAML トークン属性を構成し、次の手順を実行します。

    | 属性名      | 属性値      |
    | ------------------- | -------------------- |
    | insync\_auth\_token |トークン生成値を入力します |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。
    
    ![Configure single sign-on](./media/druva-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/druva-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。 トークン生成値については後で説明します。
    
    d. **[OK]** をクリックします。    

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/druva-tutorial/tutorial_general_400.png)

1. **[Druva 構成]** セクションで、**[Druva の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/druva-tutorial/tutorial_druva_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Druva 企業サイトに管理者としてログインします。

1. **[管理]\>[設定]** の順にクリックします。

    ![設定](./media/druva-tutorial/ic795091.png "Settings")

1. [シングル サインオンの設定] ダイアログで、次の手順を実行します。

    ![Single Sign-On Settings](./media/druva-tutorial/ic795092.png "Single Sign-On Settings")
    
    a. **[ID プロバイダーのログイン URL]** ボックスに、Azure Portal からコピーした**シングル サインオン サービス URL** の値を貼り付けます。
        
    b. **[ID Provider Logout URL]\(ID プロバイダー ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。
        
    c. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[ID プロバイダー証明書]** ボックスに貼り付けます。
     
    d. **[設定]** ページを開くには、**[保存]** をクリックします。

1. **[設定]** ページで、**[SSO トークンの生成]** をクリックします。

    ![設定](./media/druva-tutorial/ic795093.png "Settings")

1. **[シングル サインオン認証トークン]** ダイアログで、次の手順を実行します。

    ![SSO Token](./media/druva-tutorial/ic795094.png "SSO Token")
    
    a. **[Copy]\(コピー\)** をクリックし、Azure Portal の **[Add Attribute]\(属性の追加\)** セクションの **[Value]\(値\)** ボックスにコピーした値を貼り付けます。
    
    b. **[閉じる]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/druva-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/druva-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/druva-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/druva-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-druva-test-user"></a>Druva テスト ユーザーの作成

Azure AD ユーザーが Druva にログインできるようにするには、そのユーザーを Druva にプロビジョニングする必要があります。 Druva の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Druva** 企業サイトに管理者としてログインします。

1. **[管理]\>[ユーザー]** の順に移動します。
   
   ![Manage Users](./media/druva-tutorial/ic795097.png "Manage Users")

1. **[Create New]** をクリックします。
   
   ![Manage Users](./media/druva-tutorial/ic795098.png "Manage Users")

1. [新しいユーザーの作成] ダイアログで、次の手順を実行します。
   
   ![Create NewUser](./media/druva-tutorial/ic795099.png "Create NewUser")
   
   a. **[Email address]\(電子メール アドレス\)** ボックスに、ユーザーの電子メール アドレスを入力します (この例では **brittasimon@contoso.com**)。
   
   b. **[Name]\(名前\)** ボックスに、ユーザーの名前 (**BrittaSimon** など) を入力します。
   
   c. **[Create User]** をクリックします。

>[!NOTE]
>Druva から提供されている他の Druva ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Druva へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Druva に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Druva]** を選択します。

    ![アプリケーションの一覧の Druva のリンク](./media/druva-tutorial/tutorial_druva_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Druva のタイルをクリックすると、自動的に Druva アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/druva-tutorial/tutorial_general_01.png
[2]: ./media/druva-tutorial/tutorial_general_02.png
[3]: ./media/druva-tutorial/tutorial_general_03.png
[4]: ./media/druva-tutorial/tutorial_general_04.png

[100]: ./media/druva-tutorial/tutorial_general_100.png

[200]: ./media/druva-tutorial/tutorial_general_200.png
[201]: ./media/druva-tutorial/tutorial_general_201.png
[202]: ./media/druva-tutorial/tutorial_general_202.png
[203]: ./media/druva-tutorial/tutorial_general_203.png

