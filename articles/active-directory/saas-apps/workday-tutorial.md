---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday の統合 | Microsoft Docs
description: Azure Active Directory と Workday の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 4ad86601abf86c11e899d481b9e981f810c9a6d1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132328668"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday の統合

このチュートリアルでは、Workday と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Workday を統合すると、次のことができます。

* Workday にアクセスする Azure AD を制御する。
* ユーザーが自分の Azure AD アカウントを使用して Workday に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Workday でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Workday では、**SP** によって開始される SSO がサポートされます。

* Workday Mobile アプリケーションを Azure AD に構成して SSO を有効にできるようになりました。 構成方法の詳細については、こちらの[リンク](workday-mobile-tutorial.md)を参照してください。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-workday-from-the-gallery"></a>ギャラリーからの Workday の追加

Azure AD への Workday の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workday を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Workday**」と入力します。
1. 結果ウィンドウで **[Workday]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Workday の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Workday に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Workday の関連ユーザーの間で、リンク関係を確立する必要があります。

Workday に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Workday の構成](#configure-workday)** - アプリケーション側で SSO 設定を構成します。
    1. **[Workday テスト ユーザーの作成](#create-workday-test-user)** - Workday で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Workday** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml2.flex` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml.htmld` のパターンを使用して URL を入力します

    c. **[ログアウト URL]** テキスト ボックスに、`https://impl.workday.com/<tenant>/login-saml.htmld` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、応答 URL、ログアウト URL で更新します。 応答 URL には必ずサブドメインを入れます (例: www、wd2、wd3、wd3-impl、wd5、wd5-impl)。
    > `http://www.myworkday.com` のような URL は動作しますが、`http://myworkday.com` は動作しません。 これらの値を取得するには、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Workday アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Workday アプリケーションでは、**nameidentifier** が **user.mail** や **UPN** などにマップされると想定されているため、**[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

    > [!NOTE]
    > ここでは、既定値として UPN (user.userprincipalname) を使用して名前 ID をマップしています。 SSO を正常に動作させるには、Workday アカウント (メール、UPN など) の実際のユーザー ID を使用して名前 ID をマップする必要があります。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[署名]** オプションを要件に応じて変更するには、**[編集]** ボタンをクリックして **[SAML 署名証明書]** ダイアログを開きます。

    ![Certificate](common/edit-certificate.png) 

    ![[SAML Signing Certificate (SAML 署名証明書)]](./media/workday-tutorial/signing-option.png)

    a. **[署名オプション]** で **[SAML 応答とアサーションへの署名]** を選択します。

    b. **[保存]**

1. **[Workday のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

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

このセクションでは、B. Simon に Workday へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Workday]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-workday"></a>Workday の構成

1. 別の Web ブラウザー ウィンドウで、Workday 企業サイトに管理者としてサインインします。

1. ホーム ページの左上にある **[Search]\(検索\)** ボックスで、「**Edit Tenant Setup – Security**」(テナントのセットアップの編集 - セキュリティ) という名前を検索します。

    ![テナントのセキュリティの編集](./media/workday-tutorial/search-box.png "テナントのセキュリティの編集")


1. **[SAML Setup]\(SAML のセットアップ\)** セクションの **[Import Identity Provider]\(ID プロバイダーのインポート\)** をクリックします。

    ![[SAML 設定]](./media/workday-tutorial/saml-setup.png "[SAML 設定]")

1. **[Import Identity Provider]\(ID プロバイダーのインポート\)** セクションで、次の手順を実行します。

    ![ID プロバイダーのインポート](./media/workday-tutorial/import-identity-provider.png)

    a. **ID プロバイダー名** (`AzureAD` など) をテキスト ボックスに入力します。

    b. **[Used for Environments]\(環境に使用\)** テキストボックスで、ドロップダウンから適切な環境名を選択します。

    c. **[Select files]\(ファイルの選択\)** をクリックして、ダウンロードした **フェデレーション メタデータ XML** ファイルをアップロードします。

    d. **[OK]** 、 **[Done]\(完了\)** の順にクリックします。

1. **[Done]\(完了\)** をクリックすると、 **[SAML Identity Providers]\(SAML ID プロバイダー\)** に新しい行が追加されます。その後、新しく作成された行について、以下の手順を追加できます。

    ![SAML ID プロバイダー。](./media/workday-tutorial/saml-identity-providers.png "SAML ID プロバイダー")

    a. **[Enable IDP Initiated Logout]\(IDP によって開始されたログアウトを有効にする\)** チェックボックスをオンにします。

    b. **[Logout Response URL]\(ログアウト応答 URL\)** テキストボックスに、「 **http://www.workday.com** 」と入力します。

    c. **[Enable Workday Initiated Logout]\(Workday によって開始されたログアウトを有効にする\)** チェック ボックスをオンにします。

    d. **[Logout Request URL]\(ログアウト要求 URL\)** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    e. **[SP Initiated]\(SP によって開始\)** チェックボックスをオンにします。

    f. **[サービス プロバイダー ID]** ボックスに、「**http://www.workday.com**」と入力します。

    g. **[SP によって開始された認証要求を圧縮しない]** を選択します。

1. 下図の場所で次の手順を行います。

    ![Workday](./media/workday-tutorial/service-provider.png "SAML ID プロバイダー")

    a. **[Service Provider ID (Will be Deprecated)]\(サービス プロバイダー ID (非推奨になる予定)\)** テキストボックスに、「 **http://www.workday.com** 」と入力します。

    b. **[IDP SSO Service URL (Will be Deprecated)]\(IDP SSO サービスの URL (非推奨になる予定)\)** テキストボックスに、**ログイン URL** 値を入力します。

    c. **[Do Not Deflate SP-initiated Authentication Request (Will be Deprecated)]\(SP によって開始された認証要求を圧縮しない (非推奨になる予定)\)** を選択します。

    d. **[Authentication Request Signature Method]\(認証要求署名方法\)** として **[SHA256]** を選択します。

    e. **[OK]** をクリックします。

    > [!NOTE]
    > シングル サインオンが正しく設定されていることを確認してください。 セットアップが正しくないシングル サインオンを有効にした場合、資格情報を使用してアプリケーションに入ることができず、ロックアウトされることがあります。このような場合、Workday では、ユーザーが通常のユーザー名とパスワードを使用して、[ご使用の Workday URL]/login.flex?redirect=n という形式でサインインできるバックアップ ログイン URL を提供します

### <a name="create-workday-test-user"></a>Workday テスト ユーザーの作成

1. 自分の Workday 企業サイトに管理者としてサインインします。

1. 右上隅の **[Profile]\(プロファイル\)** をクリックし、 **[Home]\(ホーム\)** を選択し、 **[Applications]\(アプリケーション\)** タブの **[Directory]\(ディレクトリ\)** をクリックします。 

1. **[Directory]\(ディレクトリ\)** ページで、[View]\(表示\) タブの **[Find Workers]\(従業員の検索\)** を選択します。

    ![Find workers\(従業員の検索\)](./media/workday-tutorial/user-directory.png)

1.  **[Find Workers]** \(従業員の検索\) ページで、結果からユーザーを選択します。

1. 次のページで、 **[Job]\(ジョブ\)、[Worker Security]\(従業員のセキュリティ\)** を選択します。ここで **[Workday account]\(Workday アカウント\)** が、Azure Active Directory の **[名前 ID]** 値として一致している必要があります。

    ![Worker Security\(従業員のセキュリティ\)](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Workday テスト ユーザーを作成する方法の詳細については、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始する Workday のサインオン URL にリダイレクトされます。 

* Workday のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Workday] タイルをクリックすると、SSO を設定した Workday に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Workday を構成したら、自分の組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
