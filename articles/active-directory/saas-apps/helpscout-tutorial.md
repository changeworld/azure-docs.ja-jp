---
title: 'チュートリアル: Azure Active Directory と Help Scout の統合 | Microsoft Docs'
description: Azure Active Directory と Help Scout の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: cb2e7edbecfe36889ed94dc0fcdec8eac81fbfaa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132295915"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>チュートリアル: Azure Active Directory と Help Scout の統合

このチュートリアルでは、Help Scout と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Help Scout を統合すると、次のことができます。

* Help Scout にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Help Scout に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Help Scout と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Help Scout でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Help Scout では、**SP と IDP** Initiated SSO がサポートされます。
* Help Scout では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-help-scout-from-the-gallery"></a>ギャラリーから Help Scout を追加する。

Azure AD への Help Scout の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Help Scout を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Help Scout**」と入力します。
1. 結果ウィンドウで **[Help Scout]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-help-scout"></a>Help Scout の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Help Scout に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Help Scout の関連ユーザーとの間にリンク関係を確立する必要があります。

Help Scout に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Help Scout SSO の構成](#configure-help-scout-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Help Scout テスト ユーザーの作成](#create-help-scout-test-user)** - Help Scout で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Help Scout** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **識別子** は、Help Scout の **対象ユーザー URI (サービス プロバイダーのエンティティ ID)** で、先頭は `urn:` です

    b. **応答 URL** は、Help Scout の **ポスト バック URL (Assertion Consumer Service URL)** で、先頭は `https://` です 

    > [!NOTE]
    > これらの URL の値は、単なる例です。 これらの値は、実際の応答 URL と識別子で更新する必要があります。 この値は、[認証] セクションの **[シングル サインオン]** タブから取得します。これについては後で説明します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、URL として「`https://secure.helpscout.net/members/login/`」を入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Help Scout のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Help Scout へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Help Scout]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-help-scout-sso"></a>Help Scout の SSO の構成

1. Help Scout 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Help Scout のセットアップ]** をクリックすると、Help Scout アプリケーションに移動します。 そこから、管理者の資格情報を入力して Help Scout にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Help Scout を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者としてご自分の Help Scout 企業サイトにサインインして、次の手順のようにします。

1. 上部のメニューで **[Manage]\(管理\)** をクリックし、ドロップダウン メニューの **[Company]\(会社\)** を選択します。

    ![[Company]\(会社\) が選択された [Manage]\(管理\) メニューのスクリーンショット。](./media/helpscout-tutorial/settings.png)

1. 左側のナビゲーション ウィンドウで **[Authentication]\(認証\)** を選択します。

    ![選択された [Authentication]\(認証\) を示すスクリーンショット。](./media/helpscout-tutorial/authentication.png)

1. [SAML 設定] セクションが表示されます。ここで次の手順に従います。

    ![指定された情報を入力できる [Single Sign-On]\(シングル サインオン\) タブを示すスクリーンショット。](./media/helpscout-tutorial/configuration.png)

    a. **[Post-back URL (Assertion Consumer Service URL)]\(ポスト バック URL (Assertion Consumer Service URL)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    b. **[Audience URI (Service Provider Entity ID)]\(対象ユーザー URI (サービス プロバイダー エンティティ ID)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

1. **[SAML を有効にする]** をオンにして、次の手順を実行します。

    ![[Single Sign-On]\(シングル サインオン\) タブのスクリーンショット。ここで、SAML を有効にしたり他の情報を追加したりします。](./media/helpscout-tutorial/information.png)

    a. **[Single Sign-On URL]\(シングル サインオン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    b. **[証明書のアップロード]** をクリックして、Azure Portal からダウンロードした **証明書 (Base64)** をアップロードします。

    c. **[メール ドメイン]** ボックスに、組織のメール ドメイン (例: `contoso.com`) を入力します。 複数のドメインを指定する場合は、コンマで区切ります。 Help Scout ユーザーまたは管理者が [Help Scout ログイン ページ](https://secure.helpscout.net/members/login/)で特定のドメインを入力すると必ず、その資格情報で認証するために ID プロバイダーにルーティングされます。

    d. 最後に、ユーザーがこの方法以外で Help Scout にログオンできないようにする場合は、 **[Force SAML Sign-on]\(強制 SAML サインオン\)** の設定を切り替えてオンにします。 Help Scout 資格情報でも引き続きサインインできるようにする場合は、この設定をオフのままにします。 これを有効にしても、アカウント所有者は、いつでも自身のアカウント パスワードで Help Scout にログインにします。

    e. **[保存]** をクリックします。

### <a name="create-help-scout-test-user"></a>Help Scout テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Help Scout に作成します。 Help Scout では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Help Scout にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Help Scout のサインオン URL にリダイレクトされます。  

* Help Scout のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Help Scout に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Help Scout] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Help Scout に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Help Scout を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
