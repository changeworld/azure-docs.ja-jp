---
title: 'チュートリアル: Azure Active Directory と DocuSign の統合 | Microsoft Docs'
description: Azure Active Directory と DocuSign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 5d8aef1edf4d7a02686db48d3e788e4f9493c398
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448487"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>チュートリアル: Azure Active Directory と DocuSign の統合

このチュートリアルでは、DocuSign と Azure Active Directory (Azure AD) を統合する方法について説明します。

DocuSign と Azure AD の統合には、次の利点があります。

- DocuSign にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に DocuSign にサインオン (シングル サインオン) できるように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と DocuSign の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- DocuSign でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの DocuSign の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-docusign-from-the-gallery"></a>ギャラリーからの DocuSign の追加
Azure AD への DocuSign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に DocuSign を追加する必要があります。

**ギャラリーから DocuSign を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **DocuSign**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/docusign-tutorial/tutorial_docusign_search.png)

1. 結果ウィンドウで **DocuSign** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、DocuSign で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する DocuSign ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと DocuSign の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を DocuSign の **[Username]\(ユーザー名\)** の値として割り当てます。

DocuSign で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[DocuSign テスト ユーザーの作成](#creating-a-docusign-test-user)** - DocuSign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、DocuSign アプリケーションでシングル サインオンを構成します。

**DocuSign で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **DocuSign** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/docusign-tutorial/tutorial_docusign_samlbase.png)

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

1. Azure Portal の **[DocuSign 構成]** セクションで、**[DocuSign の構成]** をクリックして、[サインオンの構成] ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。
    
    ![Configure single sign-on](./media/docusign-tutorial/tutorial_docusign_configure.png)

1. 別の Web ブラウザー ウィンドウで、**DocuSign 管理者ポータル**に管理者としてログインします。

1. 左側のナビゲーション メニューの **[Domains (ドメイン)]** をクリックします。
   
    ![シングル サインオンの構成][51]

1. 右側のウィンドウで、 **[Claim Domain (ドメインを要求する)]** をクリックします。
   
    ![シングル サインオンの構成][52]

1. **[Claim a domain (ドメインの要求)]** ダイアログの **[Domain Name (ドメイン名)]** ボックスに、所属する会社のドメインを入力してから、**[Claim (要求)]** をクリックします。 ドメインを確認し、状態がアクティブであることを確かめてください。
   
    ![シングル サインオンの構成][53]

1. 左側のメニューで、 **[Identity Providers (ID プロバイダー)]**  
   
    ![シングル サインオンの構成][54]
1. 右側のウィンドウで、 **[Add Identity Provider (ID プロバイダーの追加)]** をクリックします。 
   
    ![シングル サインオンの構成][55]

1. **[Identity Provider Settings (ID プロバイダーの設定)]** ページで、次の手順を実行します。
   
    ![シングル サインオンの構成][56]

    a. **[Name (名前)]** ボックスに、構成の一意の名前を入力します。 スペースは使用しないでください。

    b. **SAML エンティティ ID** を **[ID プロバイダーの発行者]** ボックスに貼り付けます。

    c. **SAML シングル サインオン サービス URL** を **[ID プロバイダーのログイン URL]** ボックスに貼り付けます。

    d. **サインアウト URL** を **[ID プロバイダーのログアウト URL]** ボックスに貼り付けます。

    e. **[Sign AuthN Request (認証要求に署名する)]** を選択します。

    f. **[Send AuthN request by (認証要求の送信方法)]** として、**[POST]** を選択します。

    g. **[Send logout request by]\(ログアウト要求の送信方法\)** として、**[GET]** を選択します。

1. **[Custom Attribute Mapping (カスタム属性マッピング)]** セクションで、Azure AD の要求とマッピングするフィールドを選択します。 この例では、**emailaddress** 要求は **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** の値とマップされます。 これは、Azure AD の電子メール要求の既定の要求名です。 
   
    > [!NOTE]
    > Azure AD のユーザーを DocuSign のユーザー マッピングにマッピングする際は、適切な**ユーザー識別子**を使用してください。 適切なフィールドを選択し、組織の設定に基づく適切な値を入力してください。
          
    ![シングル サインオンの構成][57]

1. **[Identity Provider Certificate]\(ID プロバイダー証明書\)** セクションで **[Add Certificate]\(証明書の追加\)** をクリックし、Azure AD ポータルからダウンロードした証明書をアップロードします。   
   
    ![シングル サインオンの構成][58]

1. **[Save]** をクリックします。

1. **[Identity Providers (ID プロバイダー)]** セクションで、**[Actions (アクション)]** をクリックし、**[Endpoints (エンドポイント)]** をクリックします。   
   
    ![シングル サインオンの構成][59]
 
1. **DocuSign 管理者ポータル**の **[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)** セクションで、次の手順を実行します。
   
    ![シングル サインオンの構成][60]
   
    a. **[Service Provider Issuer URL]\(サービス プロバイダー発行者 URL\)** をコピーし、Azure Portal の **[DocuSign のドメインと URL]** セクションの **[識別子]** ボックスに、`https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>` のパターンで貼り付けます。
   
    b. **[Service Provider Login URL]\(サービス プロバイダー ログイン URL\)** をコピーし、Azure Portal の **[DocuSign のドメインと URL]** セクションの **[サインオン URL]** ボックスに、`https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/` のパターンで貼り付けます。

    ![Configure single sign-on](./media/docusign-tutorial/tutorial_docusign_url.png)
      
    c.  **[閉じる]** をクリックします。
    
1. Azure Portal で、**[保存]** をクリックします。
    
    ![Configure single sign-on](./media/docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/docusign-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/docusign-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/docusign-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/docusign-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-docusign-test-user"></a>DocuSign テスト ユーザーの作成

アプリケーションでは、**ジャストインタイムのユーザー プロビジョニング**がサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に DocuSign へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**DocuSign に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[DocuSign]** を選択します。

    ![Configure single sign-on](./media/docusign-tutorial/tutorial_docusign_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [DocuSign] タイルをクリックすると、自動的に DocuSign アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/docusign-tutorial/tutorial_general_01.png
[2]: ./media/docusign-tutorial/tutorial_general_02.png
[3]: ./media/docusign-tutorial/tutorial_general_03.png
[4]: ./media/docusign-tutorial/tutorial_general_04.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/docusign-tutorial/tutorial_general_100.png

[200]: ./media/docusign-tutorial/tutorial_general_200.png
[201]: ./media/docusign-tutorial/tutorial_general_201.png
[202]: ./media/docusign-tutorial/tutorial_general_202.png
[203]: ./media/docusign-tutorial/tutorial_general_203.png

