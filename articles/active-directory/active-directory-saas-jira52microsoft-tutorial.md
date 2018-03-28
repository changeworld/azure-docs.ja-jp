---
title: 'チュートリアル: Azure Active Directory と Microsoft Azure Active Directory single sign-on for JIRA 5.2 の統合 | Microsoft Docs'
description: Azure Active Directory と Microsoft Azure Active Directory single sign-on for JIRA 5.2 の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 456a0c63881f6eb15e8166bee0105e25c951b536
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-microsoft-azure-active-directory-single-sign-on-for-jira-52"></a>チュートリアル: Azure Active Directory と Microsoft Azure Active Directory single sign-on for JIRA 5.2 の統合

このチュートリアルでは、Microsoft Azure Active Directory single sign-on for JIRA 5.2 と Azure Active Directory (Azure AD) を統合する方法を説明します。

Microsoft Azure Active Directory single sign-on for JIRA 5.2 と Azure AD の統合には、次の利点があります。

- Microsoft Azure Active Directory single sign-on for JIRA 5.2 にアクセスできるユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Microsoft Azure Active Directory single sign-on for JIRA 5.2 にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="description"></a>[説明]

Microsoft Azure Active Directory アカウントと Atlassian JIRA サーバーを使用して、シングル サインオンを有効にします。 これにより、組織のすべてのユーザーが、Azure AD の資格情報を使用して JIRA アプリケーションにログインできます。 このプラグインは、フェデレーションに SAML 2.0 を使用します。

## <a name="prerequisites"></a>前提条件

Microsoft Azure Active Directory single sign-on for JIRA 5.2 と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- JIRA Core と Software 5.2が Windows 64 ビット版にインストールされて構成されている
- JIRA サーバーの HTTPS が有効になっていること
- JIRA プラグインがサポートされているバージョンであること (下記のセクションをご覧ください)
- JIRA サーバーが認証のためにインターネット、特に Azure AD ログイン ページにアクセスでき、Azure AD からトークンを受け取れること
- 管理者の資格情報が JIRA に設定されていること
- WebSudo が JIRA で無効になっていること
- テスト ユーザーが JIRA サーバー アプリケーションで作成されていること

> [!NOTE]
> このチュートリアルの手順をテストする場合、JIRA の運用環境を使用しないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

**サポートされているバージョン:**

*   JIRA Core と Software: 5.2
*   JIRA は、6.0 と 7.2.0 もサポートします。 詳細については、[Microsoft Azure Active Directory single sign-on for JIRA](./active-directory-saas-jiramicrosoft-tutorial.md) を参照してください。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Microsoft Azure Active Directory single sign-on for JIRA 5.2 の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-microsoft-azure-active-directory-single-sign-on-for-jira-52-from-the-gallery"></a>ギャラリーからの Microsoft Azure Active Directory single sign-on for JIRA 5.2 の追加
Azure AD への Microsoft Azure Active Directory single sign-on for JIRA 5.2 の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Microsoft Azure Active Directory single sign-on for JIRA 5.2 を追加する必要があります。

**ギャラリーから Microsoft Azure Active Directory single sign-on for JIRA 5.2 を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Microsoft Azure Active Directory single sign-on for JIRA 5.2**」と入力します。結果パネルから **[Microsoft Azure Active Directory single sign-on for JIRA 5.2]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果の一覧の Microsoft Azure Active Directory single sign-on for JIRA 5.2](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーを使用して、Microsoft Azure Active Directory single sign-on for JIRA 5.2 との Azure AD シングル サインオンを構成してテストする方法を説明します。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Microsoft Azure Active Directory single sign-on for JIRA 5.2 ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Microsoft Azure Active Directory single sign-on for JIRA 5.2 の関連ユーザーの間で、リンク関係が確立されている必要があります。

Microsoft Azure Active Directory single sign-on for JIRA 5.2 での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Microsoft Azure Active Directory single sign-on for JIRA 5.2 テスト ユーザーの作成](#create-a-singlesign-onforjira5.2-test-user)** - Microsoft Azure Active Directory single sign-on for JIRA 5.2 で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Microsoft Azure Active Directory single sign-on for JIRA 5.2 アプリケーションでシングル サインオンを構成します。

**Microsoft Azure Active Directory single sign-on for JIRA 5.2 で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Microsoft Azure Active Directory single sign-on for JIRA 5.2** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_samlbase.png)

3. **[Microsoft Azure Active Directory single sign-on for JIRA 5.2 のドメインと URL]** セクションで次の手順を実行します。

    ![[Microsoft Azure Active Directory single sign-on for JIRA 5.2 のドメインと URL] のシングル サインオン情報](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_url.png)

    a. **[サインオン URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<domain:port>/` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 名前付き URL である場合は、ポートは省略できます。 これらの値は JIRA プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。
 
4. **メタデータ** URL を生成するには、次の手順を実行します。

    a. **[アプリの登録]** をクリックします。
    
    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\appregistrations.png)
   
    b. **[エンドポイント]** をクリックして **[エンドポイント]** ダイアログ ボックスを開きます。  
    
    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\endpointicon.png)

    c. コピー ボタンをクリックして、**フェデレーション メタデータ ドキュメント**の URL をコピーしてノートパッドに貼り付けます。
    
    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\endpoint.png)
     
    d. 次に、**Microsoft Azure Active Directory single sign-on for JIRA 5.2** のプロパティ ページに移動し、**[コピー]** ボタンで **[アプリケーション ID]** をコピーしてメモ帳に貼り付けます。
 
    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\appid.png)

    e. `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` パターンを使用して**メタデータ URL** を生成し、あとでプラグインの構成の際に使用するために、この値をメモ帳にコピーしておきます。

5. **[保存]** ボタンをクリックします。

    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_400.png)

6. 別の Web ブラウザー ウィンドウで、JIRA インスタンスに管理者としてログインします。

7. 歯車をポイントし、**[Add-ons]\(アドオン\)** をクリックします。
    
    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon1.png)

8. [アドオン] タブ セクションで、**[アドオンの管理]** をクリックします。

    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon7.png)

9. [Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=56521)からプラグインをダウンロードします。 **[Upload add-on]\(アドオンのアップロード\)** メニューを使用して、Microsoft が提供しているプラグインを手動でアップロードします。 プラグインのダウンロードは、[Microsoft サービス規約](https://www.microsoft.com/en-us/servicesagreement/)の対象です。

    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon12.png)

10. プラグインがインストールされると、**[User Installed]\(ユーザー インストール\)** アドオン セクションに表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon13.png)

11. 構成ページで次の手順を実行します。

    ![[Configure Single Sign-On]](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon52.png)

    > [!TIP]
    > メタデータの解決でエラーが発生しないように、アプリに対してマップされている証明書が 1 つしかないようにします。 証明書が複数ある場合は、メタデータの解決の際に管理者に対してエラーが表示されます。
 
    a. **[Metadata URL]\(メタデータ URL\)** に Azure AD から生成した**メタデータ URL** を貼り付け、**[Resolve]\(解決\)** をクリックします。 IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

    b. **識別子、応答 URL、サインオン URL** の値をコピーして、Azure Portal の **[Microsoft Azure Active Directory single sign-on for JIRA 5.2 のドメインと URL]** セクションにある**[識別子]、[応答 URL]、[サインオン URL]** ボックスにそれぞれ貼り付けます。

    c. ユーザーのログイン画面に表示するボタン名を **[Login Button Name]\(ログイン ボタン名\)** に入力します。

    d. **[SAML User ID Locations]\(SAML ユーザー ID の場所\)** で、**[User ID is in the NameIdentifier element of the Subject statement]\(Subject ステートメントの NameIdentifier 要素内のユーザー ID\)**、または **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択します。  この ID は JIRA ユーザー ID である必要があります。ユーザー ID が一致しない場合、システムがユーザーのログインを許可しません。

    > [!Note]
    > 既定の SAML ユーザー ID の場所は、名前識別子です。 属性オプションでこれを変更して、適切な属性名を入力できます。 
    
    e. **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択する場合は、ユーザー ID がある属性の名前を **[Attribute name]\(属性名\)** ボックスに入力します。 

    f. Azure AD でフェデレーション ドメイン (ADFS など) を使用している場合は、**[Enable Home Realm Discovery]\(ホーム領域の検出を有効にする\)** をクリックして **[Domain Name]\(ドメイン名\)** を構成します。
    
    g. ADFS ベースでログインする場合は、**[Domain Name]\(ドメイン名\)** にドメイン名を入力します。

    h. ユーザーが JIRA からログアウトしたときに Azure AD からもログアウトさせる場合は、**[Enable Single Sign out]\(シングル サインアウトを有効にする\)** をオンにします。 

    i. **[Save (保存)]** ボタンをクリックして、設定を保存します。

    > [!NOTE]
    > インストールとトラブルシューティングについて詳しくは、[MS JIRA SSO コネクタ管理者ガイド](ms-confluence-jira-plugin-adminguide.md)のページをご覧ください。[よくあるご質問](ms-confluence-jira-plugin-faq.md)も役立ちます。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-microsoft-azure-active-directory-single-sign-on-for-jira-52-test-user"></a>Microsoft Azure Active Directory single sign-on for JIRA 5.2 テスト ユーザーの作成

Azure AD ユーザーの JIRA オンプレミス サーバーへのログインを有効にするには、そのユーザーを Microsoft Azure Active Directory single sign-on for JIRA 5.2 にプロビジョニングする必要があります。 Microsoft Azure Active Directory single sign-on for JIRA 5.2 では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの JIRA にログインします。

2. 歯車をポイントし、**[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](.\media\active-directory-saas-msaadssojira5.2-tutorial\user1.png) 

3. [Administrator Access]\(管理者アクセス\) のページにリダイレクトされるので、**パスワード**を入力し、**[Confirm]\(確認\)** ボタンをクリックします。

    ![従業員の追加](.\media\active-directory-saas-msaadssojira5.2-tutorial\user2.png) 

4. **[User management]\(ユーザー管理\)** タブ セクションで、**[create user]\(ユーザーの作成\)** をクリックします。

    ![従業員の追加](.\media\active-directory-saas-msaadssojira5.2-tutorial\user3.png) 

5. **[Create new user]\(新しいユーザーの作成\)** ダイアログ ページで、以下の手順を実行します。

    ![従業員の追加](.\media\active-directory-saas-msaadssojira5.2-tutorial\user4.png) 

    a. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    e. **[Create user]\(ユーザーの作成\)** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Microsoft Azure Active Directory single sign-on for JIRA 5.2 へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Microsoft Azure Active Directory single sign-on for JIRA 5.2 に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Microsoft Azure Active Directory single sign-on for JIRA 5.2]** を選択します。

    ![アプリケーションの一覧の Microsoft Azure Active Directory single sign-on for JIRA 5.2 リンク](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Microsoft Azure Active Directory single sign-on for JIRA 5.2 のタイルをクリックすると、自動的に Microsoft Azure Active Directory single sign-on for JIRA 5.2 アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_01.png
[2]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_02.png
[3]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_03.png
[4]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_04.png

[100]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_100.png

[200]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_200.png
[201]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_201.png
[202]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_202.png
[203]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_203.png

