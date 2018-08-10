---
title: 'チュートリアル: Azure Active Directory と Workday の統合 | Microsoft Docs'
description: Azure Active Directory と Workday の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: 65b103d9dd4a2d50d9d51aabb1728d759351a548
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420993"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>チュートリアル: Azure Active Directory と Workday の統合

このチュートリアルでは、Workday と Azure Active Directory (Azure AD) を統合する方法について説明します。

Workday と Azure AD の統合には、次の利点があります。

- Workday にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Workday にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Workday と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workday でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Workday の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-workday-from-the-gallery"></a>ギャラリーからの Workday の追加
Azure AD への Workday の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workday を追加する必要があります。

**ギャラリーから Workday を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Workday**」と入力し、結果ウィンドウで **[Workday]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Workday](./media/workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Workday で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Workday ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workday の関連ユーザーの間で、リンク関係が確立されている必要があります。

Workday で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Workday で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Workday テスト ユーザーの作成](#create-a-workday-test-user)** - Workday で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Workday アプリケーションでシングル サインオンを構成します。

**Workday で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Workday** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/workday-tutorial/tutorial_workday_samlbase.png)

1. **[Workday のドメインと URL]** セクションで、次の手順に従います。

    ![[Workday のドメインと URL] のシングル サインオン情報](./media/workday-tutorial/tutorial_workday_url.png)

    a. **[サインオン URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml2.htmld` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに次の URL を入力します。`http://www.workday.com`

1. **[詳細な URL 設定の表示]** をオンにして、次の手順を実行します。

    ![[Workday のドメインと URL] のシングル サインオン情報](./media/workday-tutorial/tutorial_workday_url1.png)

    **[応答 URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml.htmld` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 応答 URL には必ずサブドメインを入れます (例: www、wd2、wd3、wd3-impl、wd5、wd5-impl)。 "*http://www.myworkday.com*" のような URL は動作しますが、"*http://myworkday.com*" は動作しません。 これらの値を取得するには、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせてください。  

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/workday-tutorial/tutorial_workday_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/workday-tutorial/tutorial_general_400.png)
    
1. **[Workday 構成]** セクションで、**[Workday の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Workday の構成](./media/workday-tutorial/tutorial_workday_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Workday 企業サイトに管理者としてログインします。

1. ホーム ページの左上にある **[Search]\(検索\)** ボックスで、「**Edit Tenant Setup – Security**」(テナントのセットアップの編集 - セキュリティ) という名前を検索します。
   
    ![テナントのセキュリティの編集](./media/workday-tutorial/IC782925.png "Edit Tenant Security")

1. **[リダイレクト URL]** セクションで、次の手順を実行します。
   
    ![リダイレクト URL](./media/workday-tutorial/IC7829581.png "Redirection URLs")
   
    a. **[行の追加]** をクリックします。
   
    b. **[Login Redirect URL]\(ログイン リダイレクト URL\)** ボックスと **[Mobile Redirect URL]\(モバイル リダイレクト URL\)** ボックスに、Azure Portal の **[Workday のドメインと URL]** セクションで入力した**サインオン URL** を入力します。
   
    c. Azure Portal の **[サインオンの構成]** ウィンドウで **[サインアウト URL]** をコピーし、**[Logout Redirect URL]\(ログアウト リダイレクト URL\)** ボックスに貼り付けます。

    d. **[Used for Environments]\(環境に使用\)** テキストボックスで、環境名を選択します。  

    >[!NOTE]
    > [環境] 属性の値が、テナント URL の値に関連付けられます。  
    >- Workday テナント URL のドメイン名が impl で始まる場合 (例: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*)、**[Environment]\(環境\)** 属性を [Implementation]\(実装\) に設定する必要があります。  
    >- ドメイン名が impl 以外で始まる場合は、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせて、対応する **[Environment]\(環境\)** の値を取得してください。

1. **[SAML 設定]** セクションで、次の手順を実行します。
   
    ![SAML のセットアップ](./media/workday-tutorial/IC782926.png "SAML Setup")
   
    a.  **[Enable SAML Authentication]** を選択します。
   
    b.  **[行の追加]** をクリックします。

1. **[SAML Identity Providers]\(SAML ID プロバイダー\)** セクションで、次の手順を実行します。
   
    ![SAML ID プロバイダー](./media/workday-tutorial/IC7829271.png "SAML Identity Providers")
   
    a. **[Identity Provider Name]\(ID プロバイダー名\)** テキスト ボックスに、プロバイダー名を入力します (例: *SPInitiatedSSO*)。
   
    b. Azure Portal の **[サインオンの構成]** ウィンドウで **[SAML エンティティ ID]** の値をコピーし、**[Issuer]\(発行者\)** ボックスに貼り付けます。

    ![SAML ID プロバイダー](./media/workday-tutorial/IC7829272.png "SAML Identity Providers")
   
    c. Azure Portal の **[サインオンの構成]** ウィンドウで **[サインアウト URL]** の値をコピーし、**[Logout Response URL]\(ログアウト応答 URL\)** テキストボックスに貼り付けます。

    d. Azure Portal の **[サインオンの構成]** ウィンドウで **[SAML シングル サインオン サービスの URL]** の値をコピーし、**[IdP SSO Service URL]\(IdP SSO サービス URL\)** ボックスに貼り付けます。

    e. **[Used for Environments]\(環境に使用\)** テキストボックスで、環境名を選択します。

    f. **[ID プロバイダーの公開鍵証明書]** をクリックし、**[作成]** をクリックします。 

    ![作成](./media/workday-tutorial/IC782928.png "作成")

    g. **[x509 公開鍵の作成]** をクリックします。 

    ![作成](./media/workday-tutorial/IC782929.png "作成")

1. **[x509 公開鍵の表示]** セクションで、次の手順を実行します。 
   
    ![x509 公開鍵の表示](./media/workday-tutorial/IC782930.png "x509 公開鍵の表示") 
   
    a. **[Name]\(名前\)** テキスト ボックスに、証明書の名前を入力します (例: *PPE\_SP*)。
   
    b. **[有効期間の開始日]** テキスト ボックスに、証明書の有効期間の開始日を示す属性の値を入力します。
   
    c.  **[有効期間の終了日]** テキスト ボックスに、証明書の有効期間の終了日を示す属性の値を入力します。
   
    > [!NOTE]
    > 有効期間の開始日と終了日は、ダウンロードした証明書をダブルクリックして確認できます。  日付は **[詳細]** タブに表示されます。
    > 
    >
   
    d.  Base 64 でエンコードされた証明書をメモ帳で開き、その内容をコピーします。
   
    e.  **[証明書]** テキスト ボックスに、クリップボードの内容を貼り付けます。
   
    f.  Click **OK**.

1. 次の手順に従います。 
   
    ![SSO 構成](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO configuration")
   
    a.  **[サービス プロバイダー ID]** ボックスに、「**http://www.workday.com**」と入力します。
   
    b. **[SP によって開始された認証要求を圧縮しない]** を選択します。
   
    c. **[認証要求署名方法]** として **[SHA256]** を選択します。 
   
    ![認証要求署名方法](./media/workday-tutorial/WorkdaySSOConfiguration.png "Authentication Request Signature Method") 
   
    d. Click **OK**. 
   
    ![OK](./media/workday-tutorial/IC782933.png "OK")

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/workday-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/workday-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/workday-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/workday-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-workday-test-user"></a>Workday テスト ユーザーの作成

このセクションでは、Workday で Britta Simon というユーザーを作成します。 [Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)と連携し、Workday プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Workday へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Workday に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Workday]** を選択します。

    ![アプリケーションの一覧の Workday のリンク](./media/workday-tutorial/tutorial_workday_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Workday] タイルをクリックすると、自動的に Workday アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/workday-tutorial/tutorial_general_01.png
[2]: ./media/workday-tutorial/tutorial_general_02.png
[3]: ./media/workday-tutorial/tutorial_general_03.png
[4]: ./media/workday-tutorial/tutorial_general_04.png

[100]: ./media/workday-tutorial/tutorial_general_100.png

[200]: ./media/workday-tutorial/tutorial_general_200.png
[201]: ./media/workday-tutorial/tutorial_general_201.png
[202]: ./media/workday-tutorial/tutorial_general_202.png
[203]: ./media/workday-tutorial/tutorial_general_203.png
