---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Harness の統合 | Microsoft Docs
description: Azure Active Directory と Harness の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: a5368ab5b5d1464bb2b5638b58366f208ba3d1fb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317940"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Harness の統合

このチュートリアルでは、Harness と Azure Active Directory (Azure AD) を統合する方法について説明します。 Harness を Azure AD と統合すると、次のことができます。

* Harness にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Harness に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Harness でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Harness では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

* Harness では、[自動化されたユーザー プロビジョニング](harness-provisioning-tutorial.md)がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-harness-from-the-gallery"></a>ギャラリーからの Harness の追加

Azure AD への Harness の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Harness を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Harness**」と入力します。
1. 結果パネルで **[Harness]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-harness"></a>Harness の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Harness に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Harness の関連ユーザーとの間にリンク関係を確立する必要があります。

Harness に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Harness SSO の構成](#configure-harness-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Harness のテスト ユーザーの作成](#create-harness-test-user)** - Harness で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Harness** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    **[応答 URL]** ボックスに、`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://app.harness.io/`」と入力します。

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL は、このチュートリアルの後半で説明する「**Harness SSO の構成**」セクションで取得します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Harness のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Harness へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Harness]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-harness-sso"></a>Harness SSO の構成

1. Harness 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup Harness]\(Harness の設定)** をクリックすると、Harness アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Harness にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Harness を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者としてご自分の Harness 企業サイトにサインインして、次の手順のようにします。

4. ページの右上で、 **[Continuous Security]\(継続的なセキュリティ\)**  >  **[Access Management]\(アクセス管理\)**  >  **[Authentication Settings]\(認証設定\)** の順にクリックします。

    ![[Continuous Security]\(継続的なセキュリティ\) メニューと [Access Management]\(アクセス管理\)、[Authentication Settings]\(認証設定\) が選択されたているスクリーンショット。](./media/harness-tutorial/authentication.png)

5. **[SSO Providers]\(SSO プロバイダー\)** セクションで、 **[+ Add SSO Providers]\(+ SSO プロバイダーの追加\)**  >  **[SAML]** の順にクリックします。

    ![[+ Add SSO Providers]\(+ SSO プロバイダーの追加\)、[SAML] が選択されている [SSO Providers]\(SSO プロバイダー\) を示すスクリーンショット。](./media/harness-tutorial/providers.png)

6. **[SAML Provider]\(SAML プロバイダー\)** ポップアップで、次の手順を実行します。

    ![[URL] と [Display Name]\(表示名\) のフィールドが強調表示され、[Choose File]\(ファイルの選択\) と [Submit]\(送信\) ボタンが選択されている [SAML Provider]\(SAML プロバイダー\) というポップアップを示すスクリーンショット。](./media/harness-tutorial/file.png)

    a. **[In your SSO Provider, please enable SAML-based login, then enter the following URL]\(SSO プロバイダーで SAML ベースのログインを有効にした後で次の URL を入力してください\)** インスタンスをコピーし、Azure portal の **[基本的な SAML 構成]** セクションの [応答 URL] ボックスに貼り付けます。

    b. **[Display Name]\(表示名\)** ボックスに表示名を入力します。

    c. **[Choose file]\(ファイルの選択\)** をクリックして、Azure AD からダウンロードしたフェデレーション メタデータ XML ファイルをアップロードします。

    d. **[SUBMIT]\(送信\)** をクリックします。

### <a name="create-harness-test-user"></a>Harness のテスト ユーザーの作成

Azure AD ユーザーが Harness にサインインできるようにするには、そのユーザーを Harness にプロビジョニングする必要があります。 Harness では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Harness にサインインします。

1. ページの右上で、 **[Continuous Security]\(継続的なセキュリティ\)**  >  **[Access Management]\(アクセス管理\)**  >  **[Users]\(ユーザー\)** の順にクリックします。

    ![[Continuous Security]\(継続的なセキュリティ\) メニューと [Access Management]\(アクセス管理\)、[ユーザー]\(ユーザー\) が選択されたているスクリーンショット。](./media/harness-tutorial/users.png)

1. ページの右側にある **[+ Add User]\(+ ユーザーの追加)** をクリックします。

    ![[+ Add User]\(+ ユーザーの追加) が選択されている [Users]\(ユーザー\) ページを示すスクリーンショット。](./media/harness-tutorial/add-user.png)

1. **[Add User]\(ユーザーの追加\)** ポップアップで、次の手順を実行します。

    ![Harness の構成](./media/harness-tutorial/configure.png)

    a. **[Email Address(es)]\(メール アドレス\)** ボックスに、ユーザーのメール アドレスを入力します (例: `B.simon@contoso.com`)。

    b. 対象の **ユーザー グループ** を選択します。

    c. **[送信]** をクリックします。

Harness では、自動ユーザー プロビジョニングもサポートされています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./harness-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Harness のサインオン URL にリダイレクトされます。  

* Harness のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Harness に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Harness] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Harness に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Harness を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
