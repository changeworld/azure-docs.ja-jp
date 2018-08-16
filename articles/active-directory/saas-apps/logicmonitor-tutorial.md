---
title: 'チュートリアル: Azure Active Directory と LogicMonitor の統合 | Microsoft Docs'
description: Azure Active Directory と LogicMonitor の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2018
ms.author: jeedes
ms.openlocfilehash: a6bc220d15e720662eaa9605421e21ccb99892ab
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502347"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>チュートリアル: Azure Active Directory と LogicMonitor の統合

このチュートリアルでは、LogicMonitor と Azure Active Directory (Azure AD) を統合する方法について説明します。

LogicMonitor と Azure AD の統合には、次の利点があります。

- LogicMonitor にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に LogicMonitor にサインオン (シングル サインオン) できるように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

LogicMonitor と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LogicMonitor でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LogicMonitor の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-logicmonitor-from-the-gallery"></a>ギャラリーからの LogicMonitor の追加
Azure AD への LogicMonitor の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LogicMonitor を追加する必要があります。

**ギャラリーから LogicMonitor を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「**LogicMonitor**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/logicmonitor-tutorial/tutorial_logicmonitor_search.png)

1. 結果ウィンドウで **LogicMonitor** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LogicMonitor で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LogicMonitor ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LogicMonitor の関連ユーザーの間で、リンク関係が確立されている必要があります。

LogicMonitor で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

LogicMonitor で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[LogicMonitor テスト ユーザーの作成](#creating-a-logicmonitor-test-user)** - LogicMonitor で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、LogicMonitor アプリケーションでシングル サインオンを構成します。

**LogicMonitor で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **LogicMonitor** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

1. **[LogicMonitor のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.logicmonitor.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.logicmonitor.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[LogicMonitor クライアント サポート チーム](https://www.logicmonitor.com/contact/)に問い合わせてください。 
 


1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/logicmonitor-tutorial/tutorial_general_400.png)

1. **LogicMonitor** の企業サイトに管理者としてログインします。

1. 上部のメニューで **[Settings]** をクリックします。
   
    ![設定](./media/logicmonitor-tutorial/ic790052.png "Settings")

1. 左側にあるナビゲーション バーで、 **[シングル サインオン]**
   
    ![シングル サインオン](./media/logicmonitor-tutorial/ic790053.png "Single Sign-On")

1. **[シングル サインオンの設定]** セクションで、次の手順に従います。
   
    ![Single Sign-On Settings](./media/logicmonitor-tutorial/ic790054.png "Single Sign-On Settings")
   
    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[Default Role Assignment (既定のロールの割り当て)]** で、**[読み取り専用]** を選択します。
   
    c. ダウンロードしたメタデータ ファイルをメモ帳で開き、ファイルの内容を **[Identity Provider Metadata (ID プロバイダーのメタデータ)]** ボックスに貼り付けます。
   
    d. **[変更を保存]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/logicmonitor-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/logicmonitor-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/logicmonitor-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/logicmonitor-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-logicmonitor-test-user"></a>LogicMonitor テスト ユーザーの作成

Azure AD ユーザーがサインインできるように、Azure Active Directory ユーザー名を使用して、LogicMonitor アプリケーションにユーザーをプロビジョニングする必要があります。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. LogicMonitor の企業サイトに管理者としてログインします。

1. 上部のメニューで、**[設定]**、**[Roles and Users (ロールとユーザー)]** の順にクリックします。
   
    ![Roles and Users](./media/logicmonitor-tutorial/ic790056.png "Roles and Users")

1. **[追加]** をクリックします。

1. **[アカウントの追加]** セクションで、次の手順に従います。
   
    ![Add an account](./media/logicmonitor-tutorial/ic790057.png "Add an account")
   
    a. 関連するテキスト ボックスにプロビジョニングする Azure Active Directory ユーザーの**ユーザー名**、**メール**、**パスワード**、**パスワードの再入力**の値を入力します。
   
    b. **[ロール]**、**[アクセス許可の表示]**、**[状態]** の順に選択します。
   
    c. **[送信]** をクリックします。

>[!NOTE]
>LogicMonitor から提供されている他の LogicMonitor ユーザー アカウント作成ツールや API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LogicMonitor へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**LogicMonitor に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[LogicMonitor]** を選択します。

    ![Configure single sign-on](./media/logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。
 
アクセス パネルで LogicMonitor のタイルをクリックすると、自動的に LogicMonitor アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/logicmonitor-tutorial/tutorial_general_203.png

