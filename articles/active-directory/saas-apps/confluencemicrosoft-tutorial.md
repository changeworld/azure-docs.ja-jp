---
title: 'チュートリアル: Azure Active Directory と Confluence SAML SSO by Microsoft の統合 | Microsoft Docs'
description: Azure Active Directory と Confluence SAML SSO by Microsoft の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 856809d6eb480d0112eb7ed85c33560950be7d64
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146765"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>チュートリアル: Azure Active Directory と Confluence SAML SSO by Microsoft の統合

このチュートリアルでは、Confluence SAML SSO by Microsoft と Azure Active Directory (Azure AD) を統合する方法について説明します。

Confluence SAML SSO by Microsoft と Azure AD の統合には、次の利点があります。

- Confluence SAML SSO by Microsoft にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで Confluence SAML SSO by Microsoft に自動的にサインオン (シングル サインオン) するように設定できます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="description"></a>説明:

Microsoft Azure Active Directory アカウントと Atlassian Confluence サーバーを使用して、シングル サインオンを有効にします。 これにより、組織のすべてのユーザーが、Azure AD の資格情報を使用して Confluence アプリケーションにログインできます。 このプラグインは、フェデレーションに SAML 2.0 を使用します。

## <a name="prerequisites"></a>前提条件

Confluence SAML SSO by Microsoft と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 64 ビットの Windows サーバー (オンプレミスまたはクラウド IaaS インフラストラクチャ) にインストールされている Confluence サーバー アプリケーション
- Confluence サーバーの HTTPS が有効になっていること
- Confluence プラグインがサポートされているバージョンであること (下記のセクションをご覧ください)
- Confluence サーバーが認証のためにインターネット、特に Azure AD ログイン ページにアクセスでき、Azure AD からトークンを受け取れること
- 管理者の資格情報が Confluence に設定されていること
- WebSudo が Confluence で無効になっていること
- テスト ユーザーが Confluence サーバー アプリケーションで作成されていること

> [!NOTE]
> このチュートリアルの手順をテストする場合、Confluence の運用環境を使用しないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="supported-versions-of-confluence"></a>サポートされている Confluence のバージョン 

現時点では、次のバージョンの Confluence がサポートされています。

- Confluence: 5.0 ～ 5.10

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Confluence SAML SSO by Microsoft の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>ギャラリーからの Confluence SAML SSO by Microsoft の追加
Azure AD への Confluence SAML SSO by Microsoft の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Confluence SAML SSO by Microsoft を追加する必要があります。

**ギャラリーから Confluence SAML SSO by Microsoft を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「**Confluence SAML SSO by Microsoft**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

1. 結果ウィンドウで、**Confluence SAML SSO by Microsoft** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Confluence SAML SSO by Microsoft で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Confluence SAML SSO by Microsoft ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Confluence SAML SSO by Microsoft の関連ユーザーの間で、リンク関係が確立されている必要があります。

Confluence SAML SSO by Microsoft で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Confluence SAML SSO by Microsoft テスト ユーザーの作成](#creating-a-confluence-saml-sso-by-microsoft-test-user)** - Confluence SAML SSO by Microsoft で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Confluence SAML SSO by Microsoft アプリケーションでシングル サインオンを構成します。

**Confluence SAML SSO by Microsoft で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Confluence SAML SSO by Microsoft** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

1. **[Confluence SAML SSO by Microsoft のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. **[サインオン URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<domain:port>/` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 名前付き URL である場合は、ポートは省略できます。 これらの値は Confluence プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。
    
    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/tutorial_metadataurl.png)
     
1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザー ウィンドウで、Confluence インスタンスに管理者としてログインします。

1. 歯車をポイントし、**[Add-ons]\(アドオン\)** をクリックします。
    
    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon1.png)

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=56503)からプラグインをダウンロードします。 **[Upload add-on]\(アドオンのアップロード\)** メニューを使用して、Microsoft が提供しているプラグインを手動でアップロードします。 プラグインのダウンロードは、[Microsoft サービス規約](https://www.microsoft.com/servicesagreement/)の対象です。 
    
    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon12.png)

1. プラグインがインストールされると、**[アドオンの管理]** セクションの **[User Installed]\(ユーザー インストール\)** アドオン セクションに表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。
    
    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon13.png)

1. 構成ページで次の手順を実行します。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > メタデータの解決でエラーが発生しないように、アプリに対してマップされている証明書が 1 つしかないようにします。 証明書が複数ある場合は、メタデータの解決の際に管理者に対してエラーが表示されます。

    a. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付け、**[解決]** ボタンをクリックします。 IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

    b. **識別子、応答 URL、サインオン URL** の値をコピーして、Azure Portal の **[Confluence SAML SSO by Microsoft のドメインと URL]** セクションにある **[識別子]、[応答 URL]、[サインオン URL]** ボックスにそれぞれ貼り付けます。

    c. ユーザーのログイン画面に表示するボタン名を **[Login Button Name]\(ログイン ボタン名\)** に入力します。

    d. **[SAML User ID Locations]\(SAML ユーザー ID の場所\)** で、**[User ID is in the NameIdentifier element of the Subject statement]\(Subject ステートメントの NameIdentifier 要素内のユーザー ID\)**、または **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択します。  この ID は Confluence ユーザー ID である必要があります。ユーザー ID が一致しない場合、システムがユーザーのログインを許可しません。 

    > [!Note]
    > 既定の SAML ユーザー ID の場所は、名前識別子です。 属性オプションでこれを変更して、適切な属性名を入力できます。
    
    e. **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択する場合は、ユーザー ID がある属性の名前を **[Attribute name]\(属性名\)** ボックスに入力します。 

    f. Azure AD でフェデレーション ドメイン (ADFS など) を使用している場合は、**[Enable Home Realm Discovery]\(ホーム領域の検出を有効にする\)** をクリックして **[Domain Name]\(ドメイン名\)** を構成します。
    
    g. ADFS ベースでログインする場合は、**[Domain Name]\(ドメイン名\)** にドメイン名を入力します。

    h. ユーザーが Confluence からログアウトしたときに Azure AD からもログアウトさせる場合は、**[Enable Single Sign out]\(シングル サインアウトを有効にする\)** をオンにします。 

    i. **[Save (保存)]** ボタンをクリックして、設定を保存します。

    > [!NOTE]
    > インストールとトラブルシューティングについて詳しくは、[MS Confluence SSO コネクタ管理者ガイド](../ms-confluence-jira-plugin-adminguide.md)のページをご覧ください。[よくあるご質問](../ms-confluence-jira-plugin-faq.md)も役立ちます。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/confluencemicrosoft-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/confluencemicrosoft-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/confluencemicrosoft-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft テスト ユーザーの作成

オンプレミス サーバーで Azure AD ユーザーの Confluence へのログインを有効にするには、そのユーザーを Confluence SAML SSO by Microsoft にプロビジョニングする必要があります。 Confluence SAML SSO by Microsoft では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの Confluence にログインします。

1. 歯車をポイントし、**[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](./media/confluencemicrosoft-tutorial/user1.png) 

1. [Users]\(ユーザー\) セクションで、**[Add users]\(ユーザーの追加\)** タブをクリックします。**[Add a User]\(ユーザーの追加\)** ダイアログ ページで、次の手順に従います。

    ![従業員の追加](./media/confluencemicrosoft-tutorial/user2.png) 

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーのメール (Britta Simon など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、Britta Simon のパスワードを入力します。

    e. **[Confirm Password]\(パスワードの確認\)** をクリックし、パスワードを再入力します。
    
    f. **[追加]** ボタンをクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Confluence SAML SSO by Microsoft へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Confluence SAML SSO by Microsoft に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[Confluence SAML SSO by Microsoft]** を選択します。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [Confluence SAML SSO by Microsoft] のタイルをクリックすると、Confluence SAML SSO by Microsoft アプリケーションに自動的にサインオンされます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/confluencemicrosoft-tutorial/tutorial_general_203.png
