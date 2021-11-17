---
title: 'チュートリアル: Azure Active Directory と Sauce Labs - Mobile and Web Testing の統合 | Microsoft Docs'
description: Azure Active Directory と Sauce Labs - Mobile and Web Testing の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2021
ms.author: jeedes
ms.openlocfilehash: 46037ff025b11af5e7acbd2ecac1d3eac485b63c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341352"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>チュートリアル: Azure Active Directory と Sauce Labs - Mobile and Web Testing の統合

このチュートリアルでは、Sauce Labs - Mobile and Web Testing と Azure Active Directory (Azure AD) を統合する方法について説明します。 Sauce Labs - Mobile and Web Testing と Azure AD を統合すると、次のことができるようになります。

* Sauce Labs - Mobile and Web Testing にアクセスする Azure AD ユーザーを制御する。
* ユーザーが各自の Azure AD アカウントで Sauce Labs - Mobile and Web Testing に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Azure AD と Sauce Labs - Mobile and Web Testing の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Sauce Labs - Mobile and Web Testing でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Sauce Labs - Mobile and Web Testing では、**IDP** によって開始される SSO がサポートされます。
* Sauce Labs - Mobile and Web Testing では、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>ギャラリーから Sauce Labs - Mobile and Web Testing を追加する

Azure AD への Sauce Labs - Mobile and Web Testing の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Sauce Labs - Mobile and Web Testing を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに 「**Sauce Labs - Mobile and Web Testing**」 と入力します。
1. 結果パネルから **[Sauce Labs - Mobile and Web Testing]** を選択して、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-sauce-labs---mobile-and-web-testing"></a>Sauce Labs - Mobile and Web Testing 用に Azure AD SSO を構成してテストする

**B.Simon** というテスト ユーザーを使用して、Sauce Labs - Mobile and Web Testing に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Sauce Labs - Mobile and Web Testing の関連ユーザーとの間にリンク関係を確立する必要があります。

Sauce Labs - Mobile and Web Testing に対して Azure AD SSO を構成し、テストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Sauce Labs - Mobile and Web Testing の SSO の構成](#configure-sauce-labs---mobile-and-web-testing-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Sauce Labs - Mobile and Web Testing テスト ユーザーの作成](#create-sauce-labs---mobile-and-web-testing-test-user)** - Sauce Labs - Mobile and Web Testing で、B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Sauce Labs - Mobile and Web Testing** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Sauce Labs - Mobile and Web Testing のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Sauce Labs - Mobile and Web Testing へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Sauce Labs - Mobile and Web Testing]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sauce-labs---mobile-and-web-testing-sso"></a>Sauce Labs - Mobile and Web Testing の SSO の構成

1. Web ブラウザーの別のウィンドウで、Sauce Labs - Mobile and Web Testing 企業サイトに管理者としてサインインします。

2. **[User]\(ユーザー\) アイコン** をクリックし、 **[Team Management]\(チーム管理\)** タブを選択します。

    ![[User]\(ユーザー\) アイコンと、選択された [Team Management]\(チーム管理\) ドロップダウンを示すスクリーンショット。](./media/saucelabs-mobileandwebtesting-tutorial/user.png)

3. テキストボックスに **ドメイン名** を入力します。

    ![テキスト ボックス内のドメイン名の例を示すスクリーンショット。](./media/saucelabs-mobileandwebtesting-tutorial/domain.png)

4. **[Configure]\(構成\)** タブをクリックします。

    ![[Single Sign On is Enabled]\(シングル サインオンが有効\) で選択されている [Configure]\(構成\) タブを示すスクリーンショット。](./media/saucelabs-mobileandwebtesting-tutorial/configure.png)

5. **[Configure Single Sign On]\(シングル サインオンの構成\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/saucelabs-mobileandwebtesting-tutorial/browse.png)

    a. **[Browse]\(参照\)** をクリックし、Azure AD からダウンロードしたメタデータ ファイルをアップロードします。

    b. **[ALLOW JUST-IN-TIME PROVISIONING]\(Just-In-Time プロビジョニングを許可する\)** チェックボックスをオンにします。

    c. **[保存]** をクリックします。

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Sauce Labs - Mobile and Web Testing テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Sauce Labs - Mobile and Web Testing に作成します。 Sauce Labs - Mobile and Web Testing では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Sauce Labs - Mobile and Web Testing にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Sauce Labs - Mobile and Web Testing サポート チーム](mailto:support@saucelabs.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Sauce Labs - Mobile and Web Testing に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Sauce Labs - Mobile and Web Testing] タイルをクリックすると、SSO を設定した Sauce Labs - Mobile and Web Testing に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Sauce Labs - Mobile and Web Testing を構成すると、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
