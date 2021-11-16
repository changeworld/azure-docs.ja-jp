---
title: 'チュートリアル: Azure Active Directory と Picturepark の統合 | Microsoft Docs'
description: Azure Active Directory と Picturepark の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: jeedes
ms.openlocfilehash: 9d245ab63e65048f91179b6255f23ee3a12942ee
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280064"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>チュートリアル: Azure Active Directory と Picturepark の統合

このチュートリアルでは、Picturepark と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Picturepark を統合すると、次のことが可能になります。

* Picturepark にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Picturepark に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Picturepark と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Picturepark でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Picturepark では、**SP** Initiated SSO がサポートされます。

## <a name="add-picturepark-from-the-gallery"></a>ギャラリーからの Picturepark の追加

Azure AD への Picturepark の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Picturepark を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Picturepark**」と入力します。
1. 結果のパネルから **[Picturepark]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-picturepark"></a>Picturepark の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Picturepark に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Picturepark の関連ユーザーとの間にリンク関係を確立する必要があります。

Picturepark を使用した Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Picturepark の構成](#configure-picturepark-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Picturepark のテスト ユーザーの作成](#create-picturepark-test-user)** - Picturepark で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Picturepark** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | 識別子 |
    |---|
    |`https://<COMPANY_NAME>.current-picturepark.com`|
    |`https://<COMPANY_NAME>.picturepark.com`|
    |`https://<COMPANY_NAME>.next-picturepark.com`|
    |
    
    b. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<COMPANY_NAME>.picturepark.com`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[Picturepark クライアント サポート チーム](https://picturepark.com/company/picturepark-customer-support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

6. **[SAML 署名証明書]** セクションで **[Thumbprint]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

7. **[Picturepark のセットアップ]** セクションで、要件に従って適切な URL をコピーします。 **[ログイン URL]** には、次のパターンの値を使用します: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ は Azure AD サブスクリプションのテナント ID です。

    ![構成 URL のコピー](./media/picturepark-tutorial/configure.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Picturepark へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Picturepark]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-picturepark-sso"></a>Picturepark の SSO の構成

1. 別の Web ブラウザーのウィンドウで、Picturepark 企業サイトに管理者としてサインインします。

2. 画面上部のツール バーで、[**管理ツール**]、[**管理コンソール**] の順にクリックします。
   
    ![[Management Console]](./media/picturepark-tutorial/tools.png "[Management Console]")

3. [**認証**]、[**ID プロバイダー**] の順にクリックします。
   
    ![認証](./media/picturepark-tutorial/identity-provider.png "認証")

4. [**ID プロバイダーの構成**] セクションで、次の手順を実行します。
   
    ![[Identity provider configuration]](./media/picturepark-tutorial/add-configuration.png "[Identity provider configuration]")
   
    a. **[追加]** をクリックします。
  
    b. 構成の名前を入力します。
   
    c. [**既定として設定**] を選択します。
   
    d. **[発行者 URI]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。
   
    e. **[Trusted Issuer Thumb Print]\(信頼された発行者の拇印\)** ボックスに、**[SAML 署名証明書]** セクションからコピーした **拇印** の値を貼り付けます。 

5. [**JoinDefaultUsersGroup**] をクリックします。

6. **[Claim]\(要求\)** ボックスで **[Emailaddress]\(電子メール アドレス\)** 属性を設定するには、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力して **[Save]\(保存\)** をクリックします。

      ![Configuration](./media/picturepark-tutorial/claim.png "構成")

### <a name="create-picturepark-test-user"></a>Picturepark テスト ユーザーの作成

Azure AD ユーザーが Picturepark にサインインできるようにするには、ユーザーを Picturepark にプロビジョニングする必要があります。 Picturepark の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Picturepark** テナントにサインインします。

1. 画面上部のツール バーで、[**管理ツール**]、[**ユーザー**] の順にクリックします。
   
    ![ユーザー](./media/picturepark-tutorial/user.png "ユーザー")

1. [**ユーザーの概要**] タブで、[**新規**] をクリックします。
   
    ![ユーザー管理](./media/picturepark-tutorial/new-user.png "[ユーザー管理]")

1. **[Create User]\(ユーザーの作成\)** ダイアログで、プロビジョニングする有効な Azure Active Directory ユーザーを次の手順で設定します。
   
    ![ユーザーの作成](./media/picturepark-tutorial/details.png "[Create User]")
   
    a. **[Email Address]\(電子メール アドレス\)** テキストボックスに、ユーザーの **電子メール アドレス** を「`BrittaSimon@contoso.com`」と入力します。  
   
    b. **[Password]\(パスワード\)** および **[Confirm Password]\(確認パスワード\)** ボックスに、BrittaSimon の **パスワード** を入力します。 
   
    c. **[First Name]\(名\)** ボックスに、ユーザーの **名** を「**Britta**」と入力します。 
   
    d. **[Last Name]\(姓\)** ボックスに、ユーザーの **姓** を「**Simon**」と入力します。
   
    e. **[Company]\(会社\)** ボックスに、ユーザーの **会社名** を入力します。 
   
    f. **[Country]\(国\)** テキストボックスに、ユーザーの **国/リージョン** を入力します。
  
    g. **[ZIP]\(郵便番号\)** ボックスに、ユーザーの **郵便番号** を入力します。
   
    h. **[City]\(市区町村\)** ボックスに、ユーザーの **市区町村名** を入力します。

    i. **[Language]** を選択します。
   
    j. **Create** をクリックしてください。

>[!NOTE]
>Picturepark から提供されている他の Picturepark ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
>

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Picturepark のサインオン URL にリダイレクトされます。 

* Picturepark のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Picturepark] タイルをクリックすると、Picturepark のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Picturepark を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
