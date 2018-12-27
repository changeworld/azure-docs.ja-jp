---
title: 'チュートリアル: Azure Active Directory と SumoLogic の統合 | Microsoft Docs'
description: Azure Active Directory と SumoLogic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: dbebf2605fb214a167a276ec8dc344ff450ae5c0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420072"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>チュートリアル: Azure Active Directory と SumoLogic の統合

このチュートリアルでは、SumoLogic と Azure Active Directory (Azure AD) を統合する方法について説明します。

SumoLogic と Azure AD の統合には、次の利点があります。

- SumoLogic にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に SumoLogic にサインオン (シングル サインオン) できるように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SumoLogic と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SumoLogic でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SumoLogic の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sumologic-from-the-gallery"></a>ギャラリーからの SumoLogic の追加
Azure AD への SumoLogic の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SumoLogic を追加する必要があります。

**ギャラリーから SumoLogic を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**SumoLogic**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/sumologic-tutorial/tutorial_sumologic_search.png)

1. 結果ウィンドウで **SumoLogic** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SumoLogic で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SumoLogic ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SumoLogic の関連ユーザーの間で、リンク関係が確立されている必要があります。

SumoLogic で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

SumoLogic で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[SumoLogic テスト ユーザーの作成](#creating-a-sumologic-test-user)** - SumoLogic で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SumoLogic アプリケーションでシングル サインオンを構成します。

**SumoLogic で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SumoLogic** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/sumologic-tutorial/tutorial_sumologic_samlbase.png)

1. **[SumoLogic のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/sumologic-tutorial/tutorial_sumologic_url.png)

    a. **[サインオン URL]** ボックスに、`https://<tenantname>.SumoLogic.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[SumoLogic クライアント サポート チーム](https://www.sumologic.com/contact-us/)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/sumologic-tutorial/tutorial_sumologic_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/sumologic-tutorial/tutorial_general_400.png)

1. **[SumoLogic 構成]** セクションで、**[SumoLogic の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/sumologic-tutorial/tutorial_sumologic_configure.png) 

1. 別の Web ブラウザーのウィンドウで、SumoLogic 企業サイトに管理者としてログインします。

1. **[Manage]\>[Security]** の順に選択します。
   
    ![Manage](./media/sumologic-tutorial/ic778556.png "Manage")

1. **[SAML]** をクリックします。
   
    ![グローバル セキュリティ設定](./media/sumologic-tutorial/ic778557.png "グローバル セキュリティ設定")

1. **[Select a configuration or create a new one]** ボックスの一覧から **[Azure AD]** を選択し、**[Configure]** をクリックします。
   
    ![Configure SAML 2.0](./media/sumologic-tutorial/ic778558.png "Configure SAML 2.0")

1. **[Configure SAML 2.0]** ダイアログで、次の手順に従います。
   
    ![Configure SAML 2.0](./media/sumologic-tutorial/ic778559.png "Configure SAML 2.0")
   
    a. **[Configuration Name]** テキスト ボックスに、「**Azure AD**」と入力します。 

    b. **[Debug Mode]** を選択します。

    c. **[発行者]** テキストボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 

    d. **[Authn Request URL]\(認証要求 URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。

    f. **[Email Attribute]** として、**[Use SAML subject]** を選択します。  

    g. **[SP initiated Login Configuration]** を選択します。

    h. **[Login Path]** テキスト ボックスに、「**Azure**」と入力し、**[保存]**.をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/sumologic-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/sumologic-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/sumologic-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/sumologic-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-sumologic-test-user"></a>SumoLogic テスト ユーザーの作成

Azure AD ユーザーが SumoLogic にログインできるようにするには、そのユーザーを SumoLogic にプロビジョニングする必要があります。  

* SumoLogic の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **SumoLogic** テナントにログインします。

1. **[管理]\>[ユーザー]** の順に移動します。
   
    ![ユーザー](./media/sumologic-tutorial/ic778561.png "Users")

1. **[追加]** をクリックします。
   
    ![ユーザー](./media/sumologic-tutorial/ic778562.png "Users")

1. **[New User]** ダイアログ ページで、次の手順に従います。
   
    ![New User](./media/sumologic-tutorial/ic778563.png "New User") 
 
    a. プロビジョニングする Azure AD アカウントに関連する情報を、**[First Name]\(名\)**、**[Last Name]\(姓\)**、および **[Email]\(電子メール\)** ボックスに入力します。
  
    b. ロールを選択します。
  
    c. **[Status]** として、**[Active]** を選択します。
  
    d. **[Save]** をクリックします。

>[!NOTE]
>SumoLogic から提供されている他の SumoLogic ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SumoLogic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**SumoLogic に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SumoLogic]** を選択します。

    ![Configure single sign-on](./media/sumologic-tutorial/tutorial_sumologic_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで SumoLogic のタイルをクリックすると、自動的に SumoLogic アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sumologic-tutorial/tutorial_general_01.png
[2]: ./media/sumologic-tutorial/tutorial_general_02.png
[3]: ./media/sumologic-tutorial/tutorial_general_03.png
[4]: ./media/sumologic-tutorial/tutorial_general_04.png

[100]: ./media/sumologic-tutorial/tutorial_general_100.png

[200]: ./media/sumologic-tutorial/tutorial_general_200.png
[201]: ./media/sumologic-tutorial/tutorial_general_201.png
[202]: ./media/sumologic-tutorial/tutorial_general_202.png
[203]: ./media/sumologic-tutorial/tutorial_general_203.png

