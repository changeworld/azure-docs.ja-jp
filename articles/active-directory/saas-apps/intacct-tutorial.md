---
title: 'チュートリアル: Azure Active Directory と Intacct の統合 | Microsoft Docs'
description: Azure Active Directory と Intacct の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: d834ca75085878350e257cc1c50e60fc1bf28484
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447341"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>チュートリアル: Azure Active Directory と Intacct の統合

このチュートリアルでは、Intacct と Azure Active Directory (Azure AD) を統合する方法について説明します。

Intacct と Azure AD の統合には、次の利点があります。

- Intacct にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Intacct にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Intacct と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Intacct でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Intacct の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-intacct-from-the-gallery"></a>ギャラリーからの Intacct の追加
Azure AD への Intacct の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Intacct を追加する必要があります。

**ギャラリーから Intacct を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Intacct**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/intacct-tutorial/tutorial_intacct_search.png)

1. 結果ウィンドウで **[Intacct]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Intacct で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Intacct ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Intacct の関連ユーザーの間で、リンク関係が確立されている必要があります。

Intacct で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Intacct で Azure AD のシングル サインオンを構成してテストするには、次の要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Intacct テスト ユーザーの作成](#creating-an-intacct-test-user)** - Intacct で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Intacct アプリケーションでシングル サインオンを構成します。

**Intacct で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Intacct** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/intacct-tutorial/tutorial_intacct_samlbase.png)

1. **[Intacct のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/intacct-tutorial/tutorial_intacct_url.png)

    **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > これは実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Intacct サポート チーム](https://us.intacct.com/support)にお問い合わせください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/intacct-tutorial/tutorial_intacct_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/intacct-tutorial/tutorial_general_400.png)

1. **[Intacct 構成]** セクションで、**[Intacct の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/intacct-tutorial/tutorial_intacct_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Intacct 企業サイトに管理者としてサインインします。

1. **[会社]** タブをクリックし、**[会社情報]** をクリックします。

    ![Company](./media/intacct-tutorial/ic790037.png "Company")

1. **[セキュリティ]** タブをクリックし、**[編集]** をクリックします。

    ![Security (セキュリティ)](./media/intacct-tutorial/ic790038.png "Security")

1. **[シングル サインオン (SSO)]** セクションで、次の手順を実行します。

    ![シングル サインオン](./media/intacct-tutorial/ic790039.png "シングル サインオン")

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[ID プロバイダーの種類]** として **[SAML 2.0]** を選択します。

    c. **[発行者のURL]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
   
    d. **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. **base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[証明書]** ボックスに貼り付けます。
   
    f. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/intacct-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/intacct-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/intacct-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/intacct-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-intacct-test-user"></a>Intacct テスト ユーザーの作成

Azure AD ユーザーが Intacct にサインインできるように設定するには、そのユーザーを Intacct にプロビジョニングする必要があります。 Intacct の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順を実行します。**

1. **Intacct** テナントにサインインします。

1. **[会社]** タブをクリックし、**[ユーザー]** をクリックします。

    ![ユーザー](./media/intacct-tutorial/ic790041.png "Users")
1. **[Add (追加)]** タブをクリックします。

    ![Add](./media/intacct-tutorial/ic790042.png "Add")
1. **[ユーザー情報]** セクションで、次の手順を実行します。

    ![User Information](./media/intacct-tutorial/ic790043.png "User Information")

    a. プロビジョニングする Azure AD アカウントの**ユーザー ID**、**姓**、**名**、**電子メール アドレス**、**役職**、**電話番号**を、**[User Information (ユーザー情報)]** セクションに入力します。

    b. プロビジョニングする Azure AD アカウントの**管理者特権**を選択します。
   
    c. **[Save]** をクリックします。 Azure AD アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

>[!NOTE]
>Azure AD ユーザー アカウントをプロビジョニングするには、Intacct から提供されているその他の Intacct ユーザー アカウント作成ツールまたは API を使用できます。
        
### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Intacct へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Intacct に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Intacct]** を選択します。

    ![Configure single sign-on](./media/intacct-tutorial/tutorial_intacct_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Intacct] タイルをクリックすると、自動的に Intacct アプリケーションにサインインします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/intacct-tutorial/tutorial_general_01.png
[2]: ./media/intacct-tutorial/tutorial_general_02.png
[3]: ./media/intacct-tutorial/tutorial_general_03.png
[4]: ./media/intacct-tutorial/tutorial_general_04.png

[100]: ./media/intacct-tutorial/tutorial_general_100.png

[200]: ./media/intacct-tutorial/tutorial_general_200.png
[201]: ./media/intacct-tutorial/tutorial_general_201.png
[202]: ./media/intacct-tutorial/tutorial_general_202.png
[203]: ./media/intacct-tutorial/tutorial_general_203.png

