---
title: チュートリアル:Azure Active Directory と SuccessFactors の統合 | Microsoft Docs
description: Azure Active Directory と SuccessFactors の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 467db4046c0600142338dcfa39e136f45255caba
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976751"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>チュートリアル:Azure Active Directory と SuccessFactors の統合

このチュートリアルでは、SuccessFactors と Azure Active Directory (Azure AD) を統合する方法について説明します。

SuccessFactors と Azure AD の統合には、次の利点があります。

- SuccessFactors にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SuccessFactors にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SuccessFactors と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SuccessFactors でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SuccessFactors の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-successfactors-from-the-gallery"></a>ギャラリーからの SuccessFactors の追加

Azure AD への SuccessFactors の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SuccessFactors を追加する必要があります。

**ギャラリーから SuccessFactors を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**SuccessFactors**」と入力し、結果ウィンドウで **[SuccessFactors]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の SuccessFactors](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SuccessFactors で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SuccessFactors ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SuccessFactors の関連ユーザーの間で、リンク関係が確立されている必要があります。

SuccessFactors で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SuccessFactors のテスト ユーザーの作成](#creating-a-successfactors-test-user)** - SuccessFactors 内で Britta Simon に対応するユーザーを作成し、Azure AD 内の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portalで Azure AD のシングル サインオンを有効にして、SuccessFactors アプリケーションでシングル サインオンを構成します。

**SuccessFactors で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SuccessFactors** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[SuccessFactors のドメインと URL] のシングル サインオン情報](./media/successfactors-tutorial/tutorial_successfactors_url.png)

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. **[識別子]** ボックスに、次のパターンを使用して URL を入力します。
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[SuccessFactors クライアント サポート チーム](https://www.successfactors.com/content/ssf-site/en/support.html)に問い合わせてください。 

5. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. **[SuccessFactors のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![SuccessFactors の構成](common/configuresection.png)

7. 別の Web ブラウザー ウィンドウで、**SuccessFactors 管理者ポータル**に管理者としてログインします。
    
8. **[アプリケーション セキュリティ]** で **[Single Sign On Feature (シングル サインオン機能)]** に移動します。 

9. **[Reset Token (リセット トークン)]** に任意の値を入力し、**[Save Token (トークンの保存)]** をクリックして、SAML SSO を有効にします。
   
    ![Configuring single sign-on on app side][11]

    > [!NOTE] 
    > この値は、オン/オフのスイッチとして使用されます。 任意の値を保存すると、SAML SSO はオンになります。 値が空白のまま保存すると、SAML SSO はオフになります。

10. 次のスクリーンショットの画面に移動して、次の操作を実行します。
   
    ![Configuring single sign-on on app side][12]
   
    a. **[SAML v2 SSO]** オプションをクリックします。
   
    b. **[SAML Asserting Party Name]\(SAML アサーティング パーティ名\)** を設定します (例: SAML 発行者 + 会社名)。
   
    c. **[発行者の URL]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。
   
    d. **[Require Mandatory Signature]\(必須の署名が必要\)** として **[アサーション]** を選択します。
   
    e. **[Enable SAML Flag (SAML フラグを有効にする)]** で **[Enabled (有効にする)]** を選択します。
   
    f. **[Login Request Signature(SF Generated/SP/RP) (ログイン要求署名 (SF 生成/SP/RP))]** で **[No (いいえ)]** を選択します。
   
    g. **[SAML Profile (SAML プロファイル)]** で **[Browser/Post Profile (Browser/Post プロファイル)]** を選択します。
   
    h. **[Enforce Certificate Valid Period (証明書の有効期間を適用する)]** で **[No (いいえ)]** を選択します。
   
    i. Azure Portal からダウンロードした証明書ファイルのコンテンツをコピーし、**[SAML Verifying Certificate]\(SAML で確認する証明書\)** ボックスに貼り付けます。

    > [!NOTE] 
    > この証明書には、証明書の開始タグおよび終了タグが必要です。

11. [SAML V2] に移動して、次の手順に従います。
   
    ![Configuring single sign-on on app side][13]
   
    a. **[Support SP-initiated Global Logout (SP によって開始されたグローバル ログアウトのサポート)]** で **[Yes (はい)]** を選択します。
   
    b. **[Global Logout Service URL (LogoutRequest destination)]\(グローバル ログアウト サービスの URL (LogoutRequest の送信先)\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。
   
    c. **[Require sp must encrypt all NameID element (すべての NameID 要素で SP での暗号化を要求)]** で **[No (いいえ)]** を選択します。
   
    d. **[NameID Format (NameID の形式)]** で **[unspecified (未指定)]** を選択します。
   
    e. **[Enable sp initiated login (AuthnRequest) (SP によって開始されたログインを有効にする (AuthnRequest))]** で **[Yes (はい)]** を選択します。
   
    f. Azure Portal からコピーした **SAML シングル サインオン サービスの URL** の値を、**[Send request as Company-Wide issuer]\(会社全体の発行者として要求を送信する\)** ボックスに貼り付けます。

12. 大文字と小文字を区別しないログイン ユーザー名を作成する場合、次の手順を実行します。
   
    ![Configure single sign-on][29]
    
    a. 下部の **[Company Settings (会社設定)]** に移動します。
   
    b. **[Enable Non-Case-Sensitive Username (大文字と小文字を区別しないユーザー名)]** の横のチェック ボックスをオンにします。
   
    c. **[Save (保存)]** をクリックします。
   
    > [!NOTE] 
    > これを有効にしようとすると、重複する SAML ログイン名が作成されるかどうかがシステムによって確認されます。 たとえば、顧客が User1 および user1 というユーザー名を持っている場合などです。 大文字と小文字の区別をしないと、これらの重複が発生します。 システムによってエラー メッセージが表示され、機能は有効になりません。 顧客はユーザー名の一方を、違うスペルになるように変更する必要があります。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-successfactors-test-user"></a>SuccessFactors のテスト ユーザーの作成

Azure AD ユーザーが SuccessFactors にログインできるようにするには、ユーザーを SuccessFactors にプロビジョニングする必要があります。  
SuccessFactors の場合、プロビジョニングは手動で行います。

SuccessFactors でユーザーを作成するには、 [SuccessFactors のサポート チーム](https://www.successfactors.com/content/ssf-site/en/support.html)に連絡する必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SuccessFactors へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[SuccessFactors]** を選択します。

    ![Configure single sign-on](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SuccessFactors] タイルをクリックすると、自動的に SuccessFactors アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
