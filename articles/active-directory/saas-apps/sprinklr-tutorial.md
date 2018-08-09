---
title: 'チュートリアル: Azure Active Directory と Sprinklr の統合 | Microsoft Docs'
description: Azure Active Directory と Sprinklr の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: e2dc9b7e7cf5964c36b21418a0162c1c2ef92dc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430183"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>チュートリアル: Azure Active Directory と Sprinklr の統合

このチュートリアルでは、Sprinklr と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sprinklr と Azure AD の統合には、次の利点があります。

- Sprinklr にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Sprinklr にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Sprinklr の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Sprinklr でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Sprinklr の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sprinklr-from-the-gallery"></a>ギャラリーからの Sprinklr の追加
Azure AD への Sprinklr の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sprinklr を追加する必要があります。

**ギャラリーから Sprinklr を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Sprinklr**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/sprinklr-tutorial/tutorial_sprinklr_search.png)

1. 結果ウィンドウで **[Sprinklr]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Sprinklr で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Sprinklr ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Sprinklr の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sprinklr で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Sprinklr で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Sprinklr テスト ユーザーの作成](#creating-a-sprinklr-test-user)** - Sprinklr で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Sprinklr アプリケーションでシングル サインオンを構成します。

**Sprinklr で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Sprinklr** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

1. **[Sprinklr のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.sprinklr.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.sprinklr.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子で値を更新してください。 これらの値を取得するには、[Sprinklr クライアント サポート チーム](https://www.sprinklr.com/contact-us/)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/sprinklr-tutorial/tutorial_general_400.png)

1. **[Sprinklr 構成]** セクションで、**[Sprinklr の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

1. 別の Web ブラウザー ウィンドウで、Sprinklr 企業サイトに管理者としてログインします。

1. **[管理] \> [設定]** の順に選択します。
   
    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. 左側のパネルで、**[Manage Partner] \> [Single Sign on]** の順に選択します。
   
    ![Manage Partner](./media/sprinklr-tutorial/ic782908.png "Manage Partner")

1. **[+Add Single Sign Ons]** をクリックします。
   
    ![Single Sign-Ons](./media/sprinklr-tutorial/ic782909.png "Single Sign-Ons")

1. **[Single Sign on]** ページで、次の手順に従います。
   
    ![Single Sign-Ons](./media/sprinklr-tutorial/ic782910.png "Single Sign-Ons")

    a. **[Name]\(名前\)** ボックスに、構成の名前を入力します (例: *WAADSSOTest*)。

    b. **[Enabled]** を選択します。

    c. **[Use new SSO Certificate]** を選択します。
             
    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    f. Azure Portal からコピーした **SAML エンティティ ID** 値を **[Entity ID]\(エンティティ ID\)** ボックスに貼り付けます。

    g. Azure Portal からコピーした **SAML シングル サインオン サービスの URL** を **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** ボックスに貼り付けます。

    h. Azure Portal からコピーした **サインアウト URL** を **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** ボックスに貼り付けます。
     
    i. **[SAML User ID Type]** として **[Assertion contains User”s sprinklr.com username]** を選択します。

    j. **[SAML User ID Location]** として **[User ID is in the Name Identifier element of the Subject statement]** を選択します。

    k. **[Save]** をクリックします。
       
    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/sprinklr-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/sprinklr-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/sprinklr-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/sprinklr-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-sprinklr-test-user"></a>Sprinklr テスト ユーザーの作成

1. Sprinklr 企業サイトに管理者としてログインします。

1. **[管理] \> [設定]** の順に選択します。
   
    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. 左側のパネルで、**[クライアントの管理] \> [ユーザー]** の順に選択します。
   
    ![設定](./media/sprinklr-tutorial/ic782914.png "Settings")

1. **[ユーザーの追加]** をクリックします。
   
    ![設定](./media/sprinklr-tutorial/ic782915.png "Settings")

1. **[Edit user]** ダイアログで、次の手順に従います。
   
    ![Edit user](./media/sprinklr-tutorial/ic782916.png "Edit user") 

    a. **[Email]**、**[First Name]**、および **[Last Name]** テキスト ボックスに、プロビジョニングする Azure AD のユーザー アカウントの情報を入力します。

    b. **[Password Disabled]** を選択します。

    c. **[Language]\(言語\)** を選択します。

    d. **[User Type]\(ユーザー タイプ\)** を選択します。

    e. **[Update]** をクリックします。
   
     >[!IMPORTANT]
     >**[[Password Disabled]]** を選択する必要があります。 
     
1. **[Role]** に移動して、次の手順に従います。
   
    ![Partner Roles](./media/sprinklr-tutorial/ic782917.png "Partner Roles")

    a. **[Global]** ボックスの一覧から、**[ALL\_Permissions]** を選択します。  

    b. **[Update]** をクリックします。

>[!NOTE]
>Sprinklr から提供されている他の Sprinklr ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sprinklr へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Sprinklr に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Sprinklr]** を選択します。

    ![Configure single sign-on](./media/sprinklr-tutorial/tutorial_sprinklr_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Sprinklr] タイルをクリックすると、自動的に Sprinklr アプリケーションにサインオンします。アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/sprinklr-tutorial/tutorial_general_203.png

