---
title: チュートリアル:Azure Active Directory と Fluxx Labs の統合 | Microsoft Docs
description: Azure Active Directory と Fluxx Labs の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: jeedes
ms.openlocfilehash: 61d4db378abd9bb4b9284198473ed1d76947f32d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Fluxx Labs の統合

このチュートリアルでは、Fluxx Labs と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Fluxx Labs を統合すると、次のことができます。

* Fluxx Labs にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Fluxx Labs に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Fluxx Labs のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Fluxx Labs では、**IDP** Initiated SSO がサポートされます。

## <a name="add-fluxx-labs-from-the-gallery"></a>ギャラリーからの Fluxx Labs の追加

Azure AD への Fluxx Labs の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Fluxx Labs を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Fluxx Labs**」と入力します。
1. 結果のパネルから **[Fluxx Labs]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-fluxx-labs"></a>Fluxx Labs の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Fluxx Labs に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Fluxx Labs の関連ユーザーとの間にリンク関係を確立する必要があります。

Fluxx Labs に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Fluxx Labs の SSO の構成](#configure-fluxx-labs-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Fluxx Labs テスト ユーザーの作成](#create-fluxx-labs-test-user)** - Fluxx Labs で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Fluxx Labs** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    a. **[識別子]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | 環境 | URL パターン|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | 運用前 | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. **[応答 URL]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | 環境 | URL パターン|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | 運用前 | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Fluxx Labs クライアント サポート チーム](https://fluxx.zendesk.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Fluxx Labs のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Fluxx Labs へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Fluxx Labs]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-fluxx-labs-sso"></a>Fluxx Labs での SSO の構成

1. 別の Web ブラウザーのウィンドウで、Fluxx Labs 企業サイトに管理者としてサインインします。

2. **[Settings]\(設定\)** セクションで **[Admin]\(管理者\)** を選択します。

    ![[Admin]\(管理者\) が選択されている [Settings]\(設定\) セクションを示すスクリーンショット。](./media/fluxxlabs-tutorial/configure-1.png)

3. 管理パネルで、 **[Plug-ins]\(プラグイン\)**  >  **[Integrations]\(インテグレーション\)** の順に選択し、 **[SAML SSO-(Disabled)]\(SAML SSO (無効)\)** を選択します。

    ![[S A M L S S O - (Disabled)]\(S A M L S S O - (無効)\) が選択されている [Integrations]\(インテグレーション\) タブを示すスクリーン。](./media/fluxxlabs-tutorial/configure-2.png)

4. [Attribute]\(属性\) セクションで、次の手順に従います。

    ![[S A M L S S O] がオンになり、フィールドに値が入力され、[Save]\(保存\) ボタンが選択されている [Attributes]\(属性\) セクションを示すスクリーンショット。](./media/fluxxlabs-tutorial/configure-3.png)

    a. **[SAML SSO]** チェックボックスをオンにします。

    b. **[Request Path]\(要求パス\)** ボックスに、「 **/auth/saml**」と入力します。

    c. **[Callback Path]\(コールバック パス\)** ボックスに、「 **/auth/saml/callback**」と入力します。

    d. **[Assertion Consumer Service Url(Single Sign-On URL)]\(Assertion Consumer Service URL (シングル サインオン URL)\)** テキストボックスに、Azure portal で入力した **[応答 URL]** の値を入力します。

    e. **[Audience(SP Entity ID)]\(対象 (SP エンティティ ID)\)** テキストボックスに、Azure portal で入力した **[ID]** の値を入力します。

    f. **[Identity Provider SSO Target URL]\(ID プロバイダーの SSO ターゲット URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    g. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    h. **[Name identifier Format]\(名前識別子の形式\)** ボックスに、次の値を入力します。`urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`

    i. **[保存]** をクリックします。

    > [!NOTE]
    > 内容が保存されると、フィールドはセキュリティのために空白で表示されますが、値は構成内に保存されています。

### <a name="create-fluxx-labs-test-user"></a>Fluxx Labs テスト ユーザーを作成する

Azure AD ユーザーが Fluxx Labs にサインインできるようにするには、そのユーザーを Fluxx Labs にプロビジョニングする必要があります。 Fluxx Labs の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Fluxx Labs 企業サイトに管理者としてサインインします。

2. 次の図に示されている **アイコン** をクリックします。

    ![[Your Dashboard is Empty]\(ダッシュボードが空です\) の下のプラス記号アイコンが選択されている管理者オプションを示すスクリーンショット。](./media/fluxxlabs-tutorial/configure-6.png)

3. ダッシュボードで、次の図に示されているアイコンをクリックして、 **[New PEOPLE]\(新規ユーザー\)** カードを開きます。

    ![[People]\(ユーザー\) の隣のプラス記号アイコンが選択されている [Contact Management]\(連絡先の管理\) メニューを示すスクリーンショット。](./media/fluxxlabs-tutorial/configure-4.png)

4. **[NEW PEOPLE]\(新規ユーザー\)** セクションで、次の手順を実行します。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/configure-5.png)

    a. Fluxx Labs では、SSO ログインの一意識別子として電子メールを使用します。 **[SSO UID]** フィールドにユーザーの電子メール アドレスを入力します。これは、SSO でのログインとして使用される電子メールアドレスと一致する電子メール アドレスです。

    b. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Fluxx Labs に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Fluxx Labs] タイルをクリックすると、SSO を設定した Fluxx Labs に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Fluxx Labs を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
