---
title: 'チュートリアル: Azure Active Directory と Menlo Security の統合 | Microsoft Docs'
description: Azure Active Directory と Menlo Security の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9e63fe6b-0ad0-405d-9e41-6a1a40a41df8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: jeedes
ms.openlocfilehash: a1f7458d52ffdee4cb48e4be0f553e3d57413249
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428847"
---
# <a name="tutorial-azure-active-directory-integration-with-menlo-security"></a>チュートリアル: Azure Active Directory と Menlo Security の統合

このチュートリアルでは、Menlo Security と Azure Active Directory (Azure AD) を統合する方法について説明します。

Menlo Security と Azure AD の統合には、次の利点があります。

- Menlo Security にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Menlo Security にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、 「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Menlo Security と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Menlo Security でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Menlo Security の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-menlo-security-from-the-gallery"></a>ギャラリーからの Menlo Security の追加
Azure AD への Menlo Security の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Menlo Security を追加する必要があります。

**ギャラリーから Menlo Security を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. **検索ボックス**に、「Menlo Security」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/menlosecurity-tutorial/tutorial_menlosecurity_search.png)

1. 結果ウィンドウで **[Menlo Security]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/menlosecurity-tutorial/tutorial_menlosecurity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Menlo Security で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Menlo Security ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Menlo Security の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Menlo Security の **[Username (ユーザー名)]** の値として割り当てます。

Menlo Security で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[ テスト ユーザーの作成](#creating-a-menlo-security-test-user)** - Menlo Security で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、Menlo Security アプリケーションでシングル サインオンを構成します。

**Menlo Security で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **Slack** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/menlosecurity-tutorial/tutorial_menlosecurity_samlbase.png)

1. **[Menlo Security のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/menlosecurity-tutorial/tutorial_menlosecurity_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.menlosecurity.com/account/login` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.menlosecurity.com/safeview-auth-server/saml/metadata` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Menlo Security クライアント サポート チーム](https://www.menlosecurity.com/menlo-contact)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/menlosecurity-tutorial/tutorial_menlosecurity_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/menlosecurity-tutorial/tutorial_general_400.png)

1. **[Menlo Security 構成]** セクションで、**[Menlo Security の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID** と **SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/menlosecurity-tutorial/tutorial_menlosecurity_configure.png) 

1. **Menlo Security 側**でシングル サインオンを構成するために、**Menlo Security**の Web サイトに管理者としてログインします。

1. **[Settings (設定)]** の **[Authentication (認証)]** に移動し、次の操作を行います。
    
    ![Configure single sign-on](./media/menlosecurity-tutorial/menlo_user_setup.png)

    a. **[Enable user authentication using SAML (SAML を使用してユーザー認証を有効にする)]** チェックボックスをオンにします。

    b. **[Allow External Access (外部アクセスを許可する)]** で **[Yes (はい)]** を選択します。

    c. **[SAML Provider (SAML プロバイダー)]** で、**[Azure Active Directory]** を選択します。

    d. **[SAML 2.0 Endpoint (SAML 2.0 エンドポイント)]**: Azure ポータルからコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    e. **[Service Identifier (Issuer) (サービス識別子 (発行者))]**: Azure ポータルからコピーした **SAML エンティティ ID** を貼り付けます。

    f. **[X.509 Certificate (X.509 証明書)]**: Azure Portal からダウンロードした**証明書 (Bas64)** をメモ帳で開いてこのボックスにコピーします。

    g. **[保存]** をクリックして設定を保存します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/menlosecurity-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/menlosecurity-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/menlosecurity-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/menlosecurity-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-menlo-security-test-user"></a>Menlo Security テスト ユーザーの作成
 
このセクションでは、Menlo Security で Britta Simon というユーザーを作成します。 [Menlo Security クライアント サポート チーム](https://www.menlosecurity.com/menlo-contact)と連携して、Menlo Security プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Menlo Security へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Menlo Security に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Menlo Security]** を選択します。

    ![Configure single sign-on](./media/menlosecurity-tutorial/tutorial_menlosecurity_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、Azure AD のシングル サインオン構成をテストします。

新しい認証をトリガーするために、"InPrivate" または "シークレット" モードでブラウザー ウィンドウを開きます。  Internet Explorer では、Ctrl キーと Shift キーを押しながら P キーを押します。  Chrome では、Ctrl キーと Shift キーを押しながら N キーを押します。  プライベート ブラウジング ウィンドウで、保護されているリソースを参照し、Azure AD ログインを実行します。  ログインが成功すると、要求されたサイトが分離されたセッションで開きます。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/menlosecurity-tutorial/tutorial_general_01.png
[2]: ./media/menlosecurity-tutorial/tutorial_general_02.png
[3]: ./media/menlosecurity-tutorial/tutorial_general_03.png
[4]: ./media/menlosecurity-tutorial/tutorial_general_04.png

[100]: ./media/menlosecurity-tutorial/tutorial_general_100.png

[200]: ./media/menlosecurity-tutorial/tutorial_general_200.png
[201]: ./media/menlosecurity-tutorial/tutorial_general_201.png
[202]: ./media/menlosecurity-tutorial/tutorial_general_202.png
[203]: ./media/menlosecurity-tutorial/tutorial_general_203.png

