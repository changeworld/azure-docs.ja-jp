---
title: 'チュートリアル: Azure Active Directory と Lifesize Cloud の統合 | Microsoft Docs'
description: Azure Active Directory と Lifesize Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: c03456dcda2b3ee44686b070cdebb5fc81c3968c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449181"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>チュートリアル: Azure Active Directory と Lifesize Cloud の統合

このチュートリアルでは、Lifesize Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

Lifesize Cloud と Azure AD の統合には、次の利点があります。

- Lifesize Cloud にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Lifesize Cloud にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Lifesize Cloud と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Lifesize Cloud でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Lifesize Cloud の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-lifesize-cloud-from-the-gallery"></a>ギャラリーからの Lifesize Cloud の追加
Azure AD への Lifesize Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lifesize Cloud を追加する必要があります。

**ギャラリーから Lifesize Cloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Lifesize Cloud**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

1. 結果ウィンドウで **Lifesize Cloud** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Lifesize Cloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Lifesize Cloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Lifesize Cloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

Lifesize Cloud で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Lifesize Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Lifesize Cloud のテスト ユーザーの作成](#creating-a-lifesize-cloud-test-user)** - Lifesize Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Lifesize Cloud アプリケーションでシングル サインオンを構成します。

**Lifesize Cloud との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Lifesize Cloud** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

1. **[Lifesize Cloud のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. **[サインオン URL]** ボックスに、`https://login.lifesizecloud.com/ls/?acs` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://login.lifesizecloud.com/<companyname>` の形式で URL を入力します。

     
1. **[詳細な URL 設定の表示]** をオンにして、次の手順を実行します。    
   
    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    **[リレー状態]** ボックスに、`https://webapp.lifesizecloud.com/?ent=<identifier>` のパターンで URL を入力します。
   
   > [!NOTE] 
   >これは実際の値ではないので注意してください。 実際のサインオン URL、リレー状態、識別子で値を更新する必要があります。 サインオン URL と識別子の値は、[Lifesize Cloud クライアント サポート チーム](https://www.lifesize.com/support)に問い合わせます。リレー状態の値は、後で説明する SSO 構成から取得できます。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_general_400.png)

1. **[Lifesize Cloud 構成]** セクションで、**[Lifesize Cloud の構成]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

1. アプリケーション用に構成された SSO を取得するには、管理者権限で Lifesize Cloud アプリケーションにログインします。

1. ページの右上にある自分の名前をクリックし、**[詳細設定]** をクリックします。
   
    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

1. [詳細設定] で **[SSO 構成]** のリンクをクリックします。 インスタンスの [SSO 構成] ページが開きます。
   
    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

1. SSO 構成 UI で、次の値を構成します。    
   
    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. **[ID プロバイダーの発行者]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    b.  **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X.509 証明書]** ボックスに貼り付けます。
  
    d. [名] テキスト ボックスの SAML 属性マッピングに、値を「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**」と入力します。
    
    e. **[姓]** テキスト ボックスの SAML 属性マッピングに、値を「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**」と入力します。
    
    f. **[電子メール]** テキスト ボックスの SAML 属性マッピングに、値を「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。

1. 構成を確認するには、**[テスト]** ボタンをクリックします。
   
    >[!NOTE]
    >テストを成功させるには、Azure AD の構成ウィザードを完了し、テストを実行するユーザーやグループにもアクセスを提供する必要があります。

1. **[SSO を有効にする]** ボタンをクリックして、SSO を有効にします。

1. **[更新]** ボタンをクリックして、すべての設定を保存します。 これにより、RelayState 値が生成されます。 テキスト ボックスに生成された RelayState の値をコピーし、**[Lifesize Cloud のドメインと URL]** セクションの **[リレー状態]** ボックスに貼り付けます。 

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/lifesize-cloud-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/lifesize-cloud-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/lifesize-cloud-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Lifesize Cloud のテスト ユーザーの作成

このセクションでは、Lifesize Cloud で Britta Simon というユーザーを作成します。 Lifesize Cloud では、ユーザーの自動プロビジョニングがサポートされています。 Azure AD での認証に成功すると、ユーザーはアプリケーションで自動的にプロビジョニングされます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Lifesize Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Lifesize Cloud に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Lifesize Cloud]** を選択します。

    ![Configure single sign-on](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Lifesize Cloud のタイルをクリックすると、Lifesize Cloud アプリケーションのログイン ページが表示されます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/lifesize-cloud-tutorial/tutorial_general_203.png

