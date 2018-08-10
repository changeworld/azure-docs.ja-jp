---
title: 'チュートリアル: Azure Active Directory と TeamSeer の統合 | Microsoft Docs'
description: Azure Active Directory と TeamSeer の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: a5910689f34c511c6cf7d8a044ef4358d2e0570d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445582"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>チュートリアル: Azure Active Directory と TeamSeer の統合

このチュートリアルでは、TeamSeer と Azure Active Directory (Azure AD) を統合する方法について説明します。

TeamSeer と Azure AD の統合には、次の利点があります。

- TeamSeer にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に TeamSeer にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

TeamSeer と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TeamSeer でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの TeamSeer の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-teamseer-from-the-gallery"></a>ギャラリーからの TeamSeer の追加
Azure AD への TeamSeer の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TeamSeer を追加する必要があります。

**ギャラリーから TeamSeer を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**TeamSeer**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/teamseer-tutorial/tutorial_teamseer_search.png)

1. 結果ウィンドウで **[TeamSeer]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TeamSeer で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TeamSeer ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TeamSeer の関連ユーザーの間で、リンク関係が確立されている必要があります。

TeamSeer で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

TeamSeer で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[TeamSeer テスト ユーザーの作成](#creating-a-teamseer-test-user)** - TeamSeer で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、TeamSeer アプリケーションでシングル サインオンを構成します。

**TeamSeer で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **TeamSeer** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/teamseer-tutorial/tutorial_teamseer_samlbase.png)

1. **[TeamSeer のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/teamseer-tutorial/tutorial_teamseer_url.png)

     **[サインオン URL]** ボックスに、`https://www.teamseer.com/<companyid>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[TeamSeer クライアント サポート チーム](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/teamseer-tutorial/tutorial_teamseer_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/teamseer-tutorial/tutorial_general_400.png)

1. **[TeamSeer 構成]** セクションで、**[TeamSeer の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/teamseer-tutorial/tutorial_teamseer_configure.png)

1. 別の Web ブラウザーのウィンドウで、TeamSeer 企業サイトに管理者としてログインします。

1. **[HR Admin]** に移動します。
   
    ![HR Admin](./media/teamseer-tutorial/ic789634.png "HR Admin")

1. **[Setup]** をクリックします。
   
    ![Setup](./media/teamseer-tutorial/ic789635.png "Setup")

1. **[Set up SAML provider details]** をクリックします。
   
    ![SAML Settings](./media/teamseer-tutorial/ic789636.png "SAML Settings")

1. SAML プロバイダーの詳細セクションで、次の手順に従います。
   
    ![SAML Settings](./media/teamseer-tutorial/ic789637.png "SAML Settings")   

    a. **シングル サインオン サービス URL** 値を **[URL]** ボックスに貼り付けます。
          
    b. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[IdP Public Certificate]\(IdP パブリック証明書\)** ボックスに貼り付けます。

1. SAML プロバイダー構成を完了するには、次の手順に従います。
    
    ![SAML Settings](./media/teamseer-tutorial/ic789638.png "SAML Settings") 

    a. **[Test Email Addresses]** に、テスト ユーザーの電子メール アドレスを入力します。 
  
    b. **[Issuer]** テキスト ボックスに、サービス プロバイダーの発行元 URL を入力します。 
  
    c. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/teamseer-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/teamseer-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/teamseer-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/teamseer-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-teamseer-test-user"></a>TeamSeer のテスト ユーザーの作成

Azure AD ユーザーが TeamSeer にログインできるようにするには、ユーザーを ShiftPlanning にプロビジョニングする必要があります。 TeamSeer の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **TeamSeer** 企業サイトに管理者としてログインします。

1. 次の手順に従います。
   
    ![HR Admin](./media/teamseer-tutorial/ic789640.png "HR Admin")  
 
    a. **[HR Admin]\>[Users]** の順に選択します。
  
    b. **[Run the New User wizard]** をクリックします。

1. **[User Details]** セクションで、次の手順に従います。
   
    ![ユーザーの詳細](./media/teamseer-tutorial/ic789641.png "User Details")

    a. プロビジョニングする有効な AAD アカウントの**名**、**姓**、**ユーザー名 (電子メール アドレス)** を、対応するボックスに入力します。
  
    b. **[次へ]** をクリックします。

1. 画面の指示に従って新しいユーザーを追加し、**[完了]** をクリックします。

>[!NOTE]
>TeamSeer から提供されている他の TeamSeer ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TeamSeer へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**TeamSeer に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[TeamSeer]** を選択します。

    ![Configure single sign-on](./media/teamseer-tutorial/tutorial_teamseer_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/teamseer-tutorial/tutorial_general_01.png
[2]: ./media/teamseer-tutorial/tutorial_general_02.png
[3]: ./media/teamseer-tutorial/tutorial_general_03.png
[4]: ./media/teamseer-tutorial/tutorial_general_04.png

[100]: ./media/teamseer-tutorial/tutorial_general_100.png

[200]: ./media/teamseer-tutorial/tutorial_general_200.png
[201]: ./media/teamseer-tutorial/tutorial_general_201.png
[202]: ./media/teamseer-tutorial/tutorial_general_202.png
[203]: ./media/teamseer-tutorial/tutorial_general_203.png

