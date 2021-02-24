---
title: チュートリアル:Azure Active Directory と SolarWinds Service Desk (旧称 Samanage) の統合 | Microsoft Docs
description: Azure Active Directory と SolarWinds Service Desk (旧称 Samanage) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: ab720430af0341f3a42d9f4d4dc19b9469872211
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675595"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>チュートリアル:Azure Active Directory と SolarWinds Service Desk (旧称 Samanage) の統合

このチュートリアルでは、SolarWinds と Azure Active Directory (Azure AD) を統合する方法について説明します。
SolarWinds と Azure AD の統合には、次の利点があります。

* SolarWinds にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SolarWinds に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

SolarWinds Service Desk (旧称 Samanage) と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Samanage でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SolarWinds では、 **SP** によって開始される SSO がサポートされます。

## <a name="adding-solarwinds-from-the-gallery"></a>ギャラリーからの SolarWinds の追加

Azure AD への SolarWinds の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SolarWinds を追加する必要があります。

**ギャラリーから SolarWinds を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** アイコンを選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「 **SolarWinds** 」と入力し、結果パネルで **SolarWinds** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の SolarWinds](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、 **Britta Simon** というテスト ユーザーに基づいて、SolarWinds で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SolarWinds 内の関連ユーザー間にリンク関係が確立されている必要があります。

SolarWinds で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SolarWinds Service Desk シングル サインオンの構成](#configure-solarwinds-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SolarWinds Service Desk テスト ユーザーの作成](#create-solarwinds-test-user)** - SolarWinds Service Desk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SolarWinds で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SolarWinds** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Samanage のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<Company Name>.samanage.com`

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については後で説明します。 詳細については、[Samanage クライアント サポート チーム](https://www.samanage.com/support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[SolarWinds のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

<a name="configure-solarwinds-single-sign-on"></a>

### <a name="configure-solarwinds-service-desk-single-sign-on"></a>SolarWinds Service Desk のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、SolarWinds 企業サイトに管理者としてログインします。

2. **[Dashboard]** をクリックして、左のナビゲーション ウィンドウで **[Setup]** を選択します。
   
    ![ダッシュボード](./media/samanage-tutorial/tutorial_samanage_001.png "ダッシュボード")

3. **[シングル サインオン]** をクリックします。
   
    ![シングル サインオン](./media/samanage-tutorial/tutorial_samanage_002.png "[Single Sign-On]")

4. **[Login using SAML (SAML でログイン)]** セクションで、次の手順を実行します。
   
    ![[Login using SAML]\(SAML でログイン\)](./media/samanage-tutorial/tutorial_samanage_003.png "[Login using SAML (SAML でログイン)]")
 
    a. **[Enable Single Sign-On with SAML (SAML でのシングル サインオンを有効にする)]** をクリックします。  
 
    b. **[Identity Provider URL]\(ID プロバイダー URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。    
 
    c. **[Login URL]\(ログイン URL\)** が Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** と一致することを確認します。
 
    d. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を入力します。
 
    e. **[SAML Issuer]** \(SAML 発行者\) ボックスに、ID プロバイダーに設定されたアプリ ID URI を入力します。
 
    f. Azure Portal からダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[Paste your Identity Provider x.509 Certificate below]\(ID プロバイダー x.509 証明書を貼り付けてください\)** ボックスに貼り付けます。
 
    g. **[Create users if they do not exist in SolarWinds (SolarWinds に存在しない場合にユーザーを作成する)]** をクリックします。
 
    h. **[Update]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「 **BrittaSimon** 」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「 **brittasimon\@yourcompanydomain.extension** 」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SolarWinds へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[SolarWinds]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SolarWinds]** を選択します。

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-solarwinds-test-user"></a>SolarWinds のテスト ユーザーを作成する

Azure AD ユーザーが SolarWinds にログインできるようにするには、そのユーザーを SolarWinds にプロビジョニングする必要があります。  
SolarWinds の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. SolarWinds 企業サイトに管理者としてログインします。

2. **[Dashboard (ダッシュボード)]** をクリックし、左のナビゲーション ウィンドウで **[Setup (セットアップ)]** を選択します。
   
    ![セットアップ](./media/samanage-tutorial/tutorial_samanage_001.png "セットアップ")

3. [ **Users** ] タブをクリックします。
   
    ![ユーザー](./media/samanage-tutorial/tutorial_samanage_006.png "ユーザー")

4. [ **New User** ] をクリックします。
   
    ![[New User]\(新しいユーザー\)](./media/samanage-tutorial/tutorial_samanage_007.png "[新しいユーザー]")

5. プロビジョニングする Azure Active Directory アカウントの **[Name]\(名前\)** と **[Email Address]\(電子メール アドレス\)** を入力し、 **[Create user]\(ユーザーの作成\)** をクリックします。
   
    ![ユーザーの作成](./media/samanage-tutorial/tutorial_samanage_008.png "[Create User]")
   
   >[!NOTE]
   >Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。 SolarWinds から提供されている他の SolarWinds ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SolarWinds] タイルをクリックすると、SSO を設定した SolarWinds に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)