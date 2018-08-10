---
title: 'チュートリアル: Azure Active Directory と QuickHelp の統合 | Microsoft Docs'
description: Azure Active Directory と QuickHelp の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: jeedes
ms.openlocfilehash: c99be60301085dddfd5c658ee1eed81b88238e54
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421594"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>チュートリアル: Azure Active Directory と QuickHelp の統合

このチュートリアルでは、QuickHelp と Azure Active Directory (Azure AD) を統合する方法について説明します。

QuickHelp と Azure AD の統合には、次の利点があります。

- QuickHelp にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に QuickHelp にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

QuickHelp と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- QuickHelp でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの QuickHelp の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-quickhelp-from-the-gallery"></a>ギャラリーからの QuickHelp の追加
Azure AD への QuickHelp の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に QuickHelp を追加する必要があります。

**ギャラリーから QuickHelp を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **QuickHelp**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/quickhelp-tutorial/tutorial_quickhelp_search.png)

1. 結果ウィンドウで **[QuickHelp]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/quickhelp-tutorial/tutorial_quickhelp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、QuickHelp で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する QuickHelp ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと QuickHelp の関連ユーザーの間で、リンク関係が確立されている必要があります。

QuickHelp で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

QuickHelp で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[QuickHelp テスト ユーザーの作成](#creating-a-quickhelp-test-user)** - QuickHelp で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、QuickHelp アプリケーションでシングル サインオンを構成します。

**QuickHelp で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **QuickHelp** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/quickhelp-tutorial/tutorial_quickhelp_samlbase.png)

1. **[QuickHelp のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/quickhelp-tutorial/tutorial_quickhelp_url.png)

    a. **[サインオン URL]** ボックスに、`https://quickhelp.com/<ROUTEURL>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに次の URL を入力します。`https://auth.quickhelp.com`

    > [!NOTE] 
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 組織の QuickHelp 管理者や BrainStorm Client Success マネージャーに値を問い合わせてください。
 
1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/quickhelp-tutorial/tutorial_quickhelp_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/quickhelp-tutorial/tutorial_general_400.png) 

1. QuickHelp 企業サイトに管理者としてサインオンします。

1. 上部のメニューで **[Admin]** をクリックします。
   
    ![Configure single sign-on][21]

1. **[QuickHelp の管理者]** メニューの **[設定]** をクリックします。
   
    ![Configure single sign-on][22]

1. **[認証設定]** をクリックします。

1. **[認証設定]** ページで、次の手順を実行します。
   
    ![Configure single sign-on][23]
   
    a. **SSO 型**として **WSFederation** を選びます。
   
    b. ダウンロードした Azure メタデータ ファイルをアップロードするには、**[参照]** をクリックし、目的のファイルに移動し、**[メタデータのアップロード]** をクリックします。
   
    c. **[電子メール]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。
   
    d. **[名]** ボックスに「`type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。
   
    e. **[姓]** ボックスに「`type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。
   
    f. **[操作バー]** で、**[保存]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/quickhelp-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/quickhelp-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/quickhelp-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/quickhelp-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-quickhelp-test-user"></a>QuickHelp テスト ユーザーの作成

このセクションの目的は、QuickHelp で Britta Simon というユーザーを作成することです。
シングル サインオンを機能させるには、Azure AD ユーザーに対応する QuickHelp ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと QuickHelp の関連ユーザーの間で、リンク関係が確立されている必要があります。

QuickHelp では、ジャストインタイム プロビジョニングがサポートされています。 つまり、必要に応じて、ユーザー アカウントは QuickHelp で自動的に作成され、Azure AD アカウントにリンクされます。

このセクションでは、ユーザー側で必要な操作はありません。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に QuickHelp へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**QuickHelp に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[QuickHelp]** を選択します。

    ![Configure single sign-on](./media/quickhelp-tutorial/tutorial_quickhelp_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  

アクセス パネルで [QuickHelp] タイルをクリックすると、自動的に QuickHelp アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/quickhelp-tutorial/tutorial_general_04.png

[100]: ./media/quickhelp-tutorial/tutorial_general_100.png

[200]: ./media/quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/quickhelp-tutorial/tutorial_general_202.png
[203]: ./media/quickhelp-tutorial/tutorial_general_203.png
[21]: ./media/quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/quickhelp-tutorial/tutorial_quickhelp_07.png
