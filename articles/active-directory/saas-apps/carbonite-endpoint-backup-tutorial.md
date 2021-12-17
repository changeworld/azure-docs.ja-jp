---
title: チュートリアル:Azure Active Directory と Carbonite Endpoint Backup の統合 | Microsoft Docs
description: Azure Active Directory と Carbonite Endpoint Backup の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: c5da66a5bfd91dd352dbd225bab40a65c5ae1a72
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292309"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>チュートリアル:Carbonite Endpoint Backup と Azure Active Directory の統合

このチュートリアルでは、Carbonite Endpoint Backup と Azure Active Directory (Azure AD) を統合する方法について説明します。 Carbonite Endpoint Backup と Azure AD を統合すると、次のことができます。

* Carbonite Endpoint Backup にアクセスするユーザーを Azure AD で管理します。
* ユーザーが自分の Azure AD アカウントを使用して Carbonite Endpoint Backup に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Carbonite Endpoint Backup でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Carbonite Endpoint Backup では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-carbonite-endpoint-backup-from-the-gallery"></a>ギャラリーからの Carbonite Endpoint Backup の追加

Azure AD への Carbonite Endpoint Backup の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Carbonite Endpoint Backup を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Carbonite Endpoint Backup**」と入力します。
1. 結果ウィンドウで **[Carbonite Endpoint Backup]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-carbonite-endpoint-backup"></a>Carbonite Endpoint Backup の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Carbonite Endpoint Backup に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Carbonite Endpoint Backup の関連ユーザーの間で、リンク関係を確立する必要があります。

Carbonite Endpoint Backup に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Carbonite Endpoint Backup の SSO の構成](#configure-carbonite-endpoint-backup-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Carbonite Endpoint Backup テスト ユーザーの作成](#create-carbonite-endpoint-backup-test-user)** - Carbonite Endpoint Backup で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Carbonite Endpoint Backup** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、次のいずれかの URL を入力します。

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. **[応答 URL]** ボックスに、次のいずれかの URL を入力します。

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のいずれかの URL を入力します。

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Carbonite Endpoint Backup のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Carbonite Endpoint Backup へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Carbonite Endpoint Backup]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-carbonite-endpoint-backup-sso"></a>Carbonite Endpoint Backup SSO の構成

1. Carbonite Endpoint Backup 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして、**My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Carbonite Endpoint Backup のセットアップ]** をクリックすると、Carbonite Endpoint Backup アプリケーションに移動します。 そこから、管理者の資格情報を入力して Carbonite Endpoint Backup にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Carbonite Endpoint Backup を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者としてご自分の Carbonite Endpoint Backup 企業サイトにサインインして、次の手順のようにします。

4. 左側のウィンドウの **[Company]\(会社\)** をクリックします。

    ![スクリーンショットは、[Company]\(会社\) が選択されている Carbonite Endpoint を示しています。](media/carbonite-endpoint-backup-tutorial/company.png)

5. **[Single sign-on]\(シングル サインオン\)** をクリックします。

    ![スクリーンショットは、[Single sign-on]\(シングル サインオン\) が選択されている [Company]\(会社\) を示しています。](media/carbonite-endpoint-backup-tutorial/single-sign-on.png)

6. **[Enable]\(有効化\)** をクリックし、 **[Edit settings]\(設定の編集\)** をクリックして構成します。

    ![スクリーンショットは、[Enable]\(有効化\) と [Edit settings]\(設定の編集\) が選択されている [Single sign-on]\(シングル サインオン\) タブを示しています。](media/carbonite-endpoint-backup-tutorial/settings.png)

7. **[Single sign-on]\(シングル サインオン\)** 設定ページで、次の手順を実行します。

    ![スクリーンショットは、この手順で説明されている情報を含む [Single sign-on]\(シングル サインオン\) タブを示しています。](media/carbonite-endpoint-backup-tutorial/save.png)

    1. **[Identity provider name]\(ID プロバイダー名\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    1. **[Identity provider URL]\(ID プロバイダーの URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. **[Choose file]\(ファイルの選択\)** をクリックして、Azure portal からダウンロードした **証明書 (Base64)** をアップロードします。

    1. **[保存]** をクリックします。

### <a name="create-carbonite-endpoint-backup-test-user"></a>Carbonite Endpoint Backup のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、Carbonite Endpoint Backup 企業サイトに管理者としてサインインします。

1. 左側のウィンドウの **[Users]\(ユーザー\)** をクリックし、 **[Add user]\(ユーザーの追加\)** をクリックします。

    ![スクリーンショットは、[Users]\(ユーザー\) と [Add user]\(ユーザーの追加\) が選択されている [Carbonite Endpoint] ページを示しています。](media/carbonite-endpoint-backup-tutorial/add-user-1.png)

1. **[Add user]\(ユーザーの追加\)** ページで、次の手順に従います。

    ![スクリーンショットは、ここで説明されている手順を実行できる [Add user]\(ユーザーの追加\) ページを示しています。](media/carbonite-endpoint-backup-tutorial/add-user-2.png)

    1. ユーザーの **[Email]\(メール\)** 、 **[First name]\(名\)** 、 **[Last name]\(姓\)** を入力し、組織の要件に従って、必要なアクセス許可をユーザーに提供します。

    1. **[ユーザーの追加]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Carbonite Endpoint Backup のサインオン URL にリダイレクトされます。  

* Carbonite Endpoint Backup のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Carbonite Endpoint Backup に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Carbonite Endpoint Backup] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Carbonite Endpoint Backup に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Carbonite Endpoint Backup を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
