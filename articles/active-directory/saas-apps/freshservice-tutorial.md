---
title: 'チュートリアル: Azure Active Directory と Freshservice の統合 | Microsoft Docs'
description: Azure Active Directory と Freshservice の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.openlocfilehash: eb848ede258d8d25d4734664bd500235f34359e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440662"
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>チュートリアル: Azure Active Directory と Freshservice の統合

このチュートリアルでは、Freshservice と Azure Active Directory (Azure AD) を統合する方法について説明します。

Freshservice と Azure AD の統合には、次の利点があります。

- Freshservice にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Freshservice にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Freshservice の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Freshservice でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Freshservice の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-freshservice-from-the-gallery"></a>ギャラリーからの Freshservice の追加
Azure AD への Freshservice の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Freshservice を追加する必要があります。

**ギャラリーから Freshservice を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Freshservice**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/freshservice-tutorial/tutorial_freshservice_search.png)

1. 結果ウィンドウで **Freshservice** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Freshservice で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Freshservice ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Freshservice の関連ユーザーの間で、リンク関係が確立されている必要があります。

Freshservice で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Freshservice で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Freshservice テスト ユーザーの作成](#creating-a-freshservice-test-user)** - Freshservice で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Freshservice アプリケーションでシングル サインオンを構成します。

**Freshservice で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Freshservice** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/freshservice-tutorial/tutorial_freshservice_samlbase.png)

1. **[Freshservice のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/freshservice-tutorial/tutorial_freshservice_url.png)

    a. **[サインオン URL]** ボックスに、`https://<democompany>.freshservice.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<democompany>.freshservice.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Freshservice クライアント サポート チーム](https://support.freshservice.com/)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、証明書の **THUMBPRINT** の値をコピーします。

    ![Configure single sign-on](./media/freshservice-tutorial/tutorial_freshservice_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/freshservice-tutorial/tutorial_general_400.png)

1. **[Freshservice 構成]** セクションで、**[Freshservice の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **クイック リファレンス セクション**から、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/freshservice-tutorial/tutorial_freshservice_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Freshservice 企業サイトに管理者としてログインします。

1. 上部のメニューで **[Admin]** をクリックします。
   
    ![管理](./media/freshservice-tutorial/ic790814.png "Admin")

1. **[カスタマー ポータル]** で **[セキュリティ]** をクリックします。
   
    ![Security (セキュリティ)](./media/freshservice-tutorial/ic790815.png "Security")

1. **[セキュリティ]** セクションで、次の手順を実行します。
   
    ![Single Sign On](./media/freshservice-tutorial/ic790816.png "Single Sign On")
   
    a. **[シングル サインオン]** を切り替えます。

    b. **[SAML SSO]** を選択します。

    c. **[SAML Login URL]\(SAML ログイン URL\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    d. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした**サインアウト URL** の値を貼り付けます。

    e. **[Security Certificate Fingerprint]\(セキュリティ証明書フィンガープリント\)** ボックスに、Azure portal からコピーした証明書の**拇印**の値を貼り付けます。

    f. **[保存]**

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/freshservice-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/freshservice-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/freshservice-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/freshservice-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-freshservice-test-user"></a>Freshservice テスト ユーザーの作成

Azure AD ユーザーが Freshservice にログインできるようにするには、そのユーザーを Freshservice にプロビジョニングする必要があります。 FreshService の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **FreshService** 企業サイトに管理者としてログインします。

1. 上部のメニューで **[Admin]** をクリックします。
   
    ![管理](./media/freshservice-tutorial/ic790814.png "Admin")

1. **[ユーザー管理]** セクションで、**[要求者]** をクリックします。
   
    ![Requesters](./media/freshservice-tutorial/ic790818.png "Requesters")

1. **[新しい要求者]** をクリックします。
   
    ![New Requesters](./media/freshservice-tutorial/ic790819.png "New Requesters")

1. **[新しい要求者]** セクションで、次の手順を実行します。
   
    ![New Requester](./media/freshservice-tutorial/ic790820.png "New Requester")   

    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**名**および**メール**の属性を入力します。

    b. **[Save]** をクリックします。
   
    >[!NOTE]
    >Azure Active Directory のアカウント所有者は、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールを受け取ります。
    >  

>[!NOTE]
>FreshService から提供されている他の FreshService ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

![ユーザーの割り当て][200] 

**Britta Simon を Freshservice に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Freshservice]** を選択します。

    ![Configure single sign-on](./media/freshservice-tutorial/tutorial_freshservice_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Freshservice] タイルをクリックすると、Freshservice アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshservice-tutorial/tutorial_general_01.png
[2]: ./media/freshservice-tutorial/tutorial_general_02.png
[3]: ./media/freshservice-tutorial/tutorial_general_03.png
[4]: ./media/freshservice-tutorial/tutorial_general_04.png

[100]: ./media/freshservice-tutorial/tutorial_general_100.png

[200]: ./media/freshservice-tutorial/tutorial_general_200.png
[201]: ./media/freshservice-tutorial/tutorial_general_201.png
[202]: ./media/freshservice-tutorial/tutorial_general_202.png
[203]: ./media/freshservice-tutorial/tutorial_general_203.png

