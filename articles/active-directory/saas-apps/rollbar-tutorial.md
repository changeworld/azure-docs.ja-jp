---
title: 'チュートリアル: Azure Active Directory と Rollbar の統合 | Microsoft Docs'
description: Azure Active Directory と Rollbar の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 1761979d554bc3318ef8f2e06fe872a6e674d0c4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341675"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>チュートリアル: Azure Active Directory と Rollbar の統合

このチュートリアルでは、Rollbar と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Rollbar を統合すると、次のことが可能になります。

* Rollbar にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Rollbar に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Rollbar と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Rollbar でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Rollbar では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* Rollbar では、[自動化されたユーザー プロビジョニング](rollbar-provisioning-tutorial.md)がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-rollbar-from-the-gallery"></a>ギャラリーから Rollbar を追加する

Azure AD への Rollbar の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Rollbar を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Rollbar**」と入力します。
1. 結果のパネルから **[Rollbar]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-rollbar"></a>Rollbar の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Rollbar に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Rollbar の関連ユーザーとの間にリンク関係を確立する必要があります。

Rollbar に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Rollbar の SSO の構成](#configure-rollbar-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Rollbar のテスト ユーザーの作成](#create-rollbar-test-user)** - Rollbar で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Rollbar** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://saml.rollbar.com` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://rollbar.com/<ACCOUNT_NAME>/saml/sso/azure/` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://rollbar.com/<ACCOUNT_NAME>/saml/login/azure/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Rollbar クライアント サポート チーム](mailto:support@rollbar.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Rollbar のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

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

このセクションでは、B.Simon に Rollbar へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Rollbar]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-rollbar-sso"></a>Rollbar の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Rollbar 企業サイトに管理者としてサインインします。

1. 右上の **[Profile Settings]\(プロファイル設定\)** をクリックし、 **[Account Name settings]\(アカウント名の設定\)** をクリックします。

    ![[Profile Settings]\(プロファイル設定\) から選択されたアカウント名の設定を示すスクリーンショット。](./media/rollbar-tutorial/general.png)

1. [SECURITY]\(セキュリティ\) の **[Identity Provider]\(ID プロバイダー\)** をクリックします。

    ![[SECURITY]\(セキュリティ\) の [Identity Provider]\(ID プロバイダー\) が選択された画面のスクリーンショット。](./media/rollbar-tutorial/security.png)

1. **[SAML Identity Provider]\(SAML ID プロバイダー\)** セクションで、次の手順に従います。

    ![[SAML Identity Provider]\(SAML ID プロバイダー\) を示すスクリーンショット。ここで、説明されている値を入力できます。](./media/rollbar-tutorial/configure.png)

    a. **[SAML Identity Provider]\(SAML ID プロバイダー\)** ドロップダウンから **[AZURE]** を選択します。

    b. メタデータ ファイルをメモ帳で開き、その内容をクリップボードにコピーし、 **[SAML Metadata]\(SAML メタデータ\)** テキストボックスに貼りつけます。

    c. **[保存]** をクリックします。

1. 保存ボタンをクリックすると、次のような画面が表示されます。

    ![[SAML Identity Provider]\(SAML ID プロバイダー\) ページの結果を示すスクリーンショット。](./media/rollbar-tutorial/identity-provider.png)

    > [!NOTE]
    > 次の手順を行うには、最初に、Azure で自分自身を Rollbar アプリにユーザーとして追加する必要があります。
    
    a. Azure による認証をすべてのユーザーに要求する場合は、 **[log in via your identity provider]\(ID プロバイダーを使用してログインする\)** をクリックして Azure で再認証します。  

    b.  画面に戻った後、 **[Require login via SAML Identity Provider]\(SAML ID プロバイダーによるログインを要求する\)** チェック ボックスをオンにします。

    b. **[保存]** をクリックします。

### <a name="create-rollbar-test-user"></a>Rollbar のテスト ユーザーの作成

Azure AD ユーザーが Rollbar にサインインできるようにするには、ユーザーを Rollbar にプロビジョニングする必要があります。 Rollbar の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Rollbar 企業サイトに管理者としてサインインします。

1. 右上の **[Profile Settings]\(プロファイル設定\)** をクリックし、 **[Account Name settings]\(アカウント名の設定\)** をクリックします。

    ![User](./media/rollbar-tutorial/general.png)

1. **[ユーザー]** をクリックします。

    ![従業員の追加](./media/rollbar-tutorial/user.png)

1. **[Invite Team Members]\(チーム メンバーの招待\)** をクリックします。

    ![[Invite Team Members]\(チーム メンバーの招待\) オプションが選択された画面のスクリーンショット。](./media/rollbar-tutorial/invite-user.png)

1. ボックスにユーザー名 (例: **brittasimon\@contoso.com**) を入力し、 **[Add/Invite]\(追加/招待\)** をクリックします。

    ![メンバーの [Add/Invite]\(追加/招待\) および指定されたアドレスを示すスクリーンショット。](./media/rollbar-tutorial/add-user.png)

1. ユーザーが招待状を受け取り、承認すると、システムにそのユーザーが作成されます。

> [!NOTE]
> Rollbar では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./rollbar-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Rollbar のサインオン URL にリダイレクトされます。  

* Rollbar のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Rollbar に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Rollbar] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Rollbar に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Rollbar を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
