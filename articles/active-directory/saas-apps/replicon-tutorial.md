---
title: チュートリアル:Azure Active Directory と Replicon の統合 | Microsoft Docs
description: Azure Active Directory と Replicon の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/13/2021
ms.author: jeedes
ms.openlocfilehash: a65906e84ac85e8c4e5282eb426d2d700bbf2c66
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333753"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>チュートリアル:Azure Active Directory と Replicon の統合

このチュートリアルでは、Replicon と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Replicon を統合すると、次のことができます。

* Replicon にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Replicon に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Replicon でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

* Replicon では、**SP** によって開始される SSO がサポートされます。

## <a name="add-replicon-from-the-gallery"></a>ギャラリーからの Replicon の追加

Azure AD への Replicon の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Replicon を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Replicon**」と入力します。
1. 結果ウィンドウで **[Replicon]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-replicon"></a>Replicon に対する Azure AD SSO の構成およびテスト

**B.Simon** というテスト ユーザーを使用して、Replicon に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Replicon の関連ユーザーとの間にリンク関係を確立する必要があります。

Replicon に Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Replicon SSO の構成](#configure-replicon-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Replicon のテスト ユーザーの作成](#create-replicon-test-user)** - Replicon で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Replicon** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://global.replicon.com/!/saml2/<client name>/sp-sso/post` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://global.replicon.com/!/saml2/<client name>` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://global.replicon.com/!/saml2/<client name>/sso/post` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Replicon クライアント サポート チーム](https://www.replicon.com/customerzone/contact-support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML 署名証明書]** の鉛筆アイコンをクリックして設定を編集します。

    ![署名アルゴリズム](common/signing-algorithm.png)

    1. **[署名オプション]** として **[SAML アサーションへの署名]** を選択します。

    1. **[署名アルゴリズム]** として **[SHA-256]** を選択します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Replicon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Replicon]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-replicon-sso"></a>Replicon の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Replicon 企業サイトに管理者としてサインインします。

2. SAML 2.0 を構成するには、次の手順に従います。

    ![SAML 認証の有効化](./media/replicon-tutorial/authentication.png "SAML 認証の有効化")

    a. **[EnableSAML Authentication2]** ダイアログを表示するには、URL の企業キーの後に次のパスを追加します。`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    1. 完全な URL のスキーマを次に示します。`https://na2.replicon.com/<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. **+** をクリックして、 **[v20Configuration]** セクションを展開します。

   c. **+** をクリックして、 **[metaDataConfiguration]** セクションを展開します。

   d. xmlSignatureAlgorithm に **[SHA256]** を選択します。

   e. **[Choose File]** をクリックして、ID プロバイダー メタデータ XML ファイルを選択し、 **[送信]** をクリックします。

### <a name="create-replicon-test-user"></a>Replicon テスト ユーザーの作成

このセクションの目的は、Replicon で B.Simon というユーザーを作成することです。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. Web ブラウザー ウィンドウで、Replicon 企業サイトに管理者としてサインインします。

2. **[Administration] \> [Users]** に移動します。

    ![ユーザー](./media/replicon-tutorial/administration.png "ユーザー")

3. **[+Add User]** をクリックします。

    ![ユーザーの追加](./media/replicon-tutorial/user.png "ユーザーの追加")

4. **[User Profile]** セクションで、次の手順に従います。

    ![ユーザー プロファイル](./media/replicon-tutorial/profile.png "User Profile")

    a. **[Login Name]\(ログイン名\)** ボックスに、プロビジョニングする Azure AD ユーザーの Azure AD メール アドレスを入力します (例: `B.Simon@contoso.com`)。

    > [!NOTE]
    > [Login Name]\(ログイン名\) は Azure AD のユーザーのメール アドレスと一致する必要があります。

    b. **[Authentication Type]** として **[SSO]** を選択します。

    c. [Authentication ID]\(認証 ID\) を [Login Name]\(ログイン名\) (ユーザーの Azure AD のメール アドレス) と同じ値に設定します

    d. **[Department]** テキストボックスに、ユーザーの所属を入力します。

    e. **[Employee Type]** として **[Administrator]** を選択します。

    f. **[Save User Profile]** をクリックします。

> [!NOTE]
> Replicon から提供されている他の Replicon ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Replicon のサインオン URL にリダイレクトされます。 

* Replicon のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 [マイ アプリ] で [Replicon] タイルをクリックすると、Replicon のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Replicon を構成したら、あなたの組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
