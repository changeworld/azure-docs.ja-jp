---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と New Relic の統合 | Microsoft Docs
description: Azure Active Directory と New Relic の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 67bbd26e4c565a5c7372be81fdf2398afe53c277
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307227"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と New Relic の統合

このチュートリアルでは、New Relic と Azure Active Directory (Azure AD) を統合する方法について説明します。 New Relic を Azure AD と統合すると、次のことができます。

* New Relic にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して New Relic に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* [New Relic One アカウント/ユーザー モデル](https://docs.newrelic.com/docs/accounts/original-accounts-billing/original-product-based-pricing/overview-changes-pricing-user-model/#user-models)の New Relic 組織 (Pro または Enterprise エディション)。 詳細については、[New Relic の要件](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more)を参照してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* New Relic では、SP Initiated SSO または IDP Initiated SSO がサポートされています。

* New Relic では、[自動化されたユーザー プロビジョニング](new-relic-by-organization-provisioning-tutorial.md)がサポートされます。

## <a name="add-new-relic-from-the-gallery"></a>ギャラリーからの New Relic の追加

Azure AD への New Relic の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に **New Relic (By Organization)** を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. **Azure Active Directory** サービスを選択します。
1. **[エンタープライズ アプリケーション]**  >  **[新しいアプリケーション]** の順に選択します。
1. **[Azure AD ギャラリーの参照]** ページで、検索ボックスに「**New Relic (By Organization)** 」と入力します。
1. 結果から **[New Relic (By Organization)]** を選択し、 **[作成]** を選択します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>New Relic に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、New Relic に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと New Relic の関連ユーザーとの間にリンク関係を確立する必要があります。

New Relic に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
   1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
   1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [New Relic の SSO の構成](#configure-new-relic-sso) - New Relic 側でシングル サインオン設定を構成します。
   1. [New Relic のテスト ユーザーの作成](#create-a-new-relic-test-user) - New Relic で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **New Relic by Organization** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 続けて、 **[シングル サインオン]** を選択します。

1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML でシングル サインオンをセットアップします] のスクリーンショット。](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、 **[識別子]** および **[応答 URL]** の値を入力します。

   * これらの値は、[New Relic の認証ドメイン UI](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#ui) から取得します。 そこから、次の手順に従います。 
      1. 複数の認証ドメインがある場合は、Azure AD SSO を接続するものを選択します。 ほとんどの企業では、**Default (既定値)** という認証ドメインしかありません。 認証ドメインが 1 つしかない場合は、何も選択する必要はありません。
      1. **[Authentication]\(認証\)** セクションの **[Assertion consumer URL]\(アサーション コンシューマー URL\)** には、 **[Reply URL]\(応答 URL\)** に使用する値が含まれています。
      1. **[Authentication]\(認証\)** セクションの **[Our entity ID]\(自分たちのエンティティ ID\)** には、 **[Identifier]\(識別子\)** に使用する値が含まれています。

1. **[User Attributes & Claims]\(ユーザー属性と要求\)** セクションで、 **[Unique User Identifier]\(一意のユーザー識別子\)** が、New Relic で使用されているメール アドレスを含むフィールドにマッピングされていることを確認します。

   * 既定のフィールド **[user.userprincipalname]** の値が New Relic のメール アドレスと同じである場合は、それで適切に機能します。
   * **[user.userprincipalname]** が New Relic のメール アドレスでない場合は、フィールド **[user.mail]** の方が適切に機能する可能性があります。

1. **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで **[App Federation Metadata Url]\(アプリのフェデレーション メタデータ URL\)** をコピーし、後で使用できるようにその値を保存します。

1. **[Set up New Relic by Organization]\(New Relic by Organization のセットアップ\)** セクションで **[Login URL]\(ログイン URL\)** をコピーし、後で使用できるようにその値を保存します。

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

このセクションでは、B.Simon に New Relic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[New Relic]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-new-relic-sso"></a>New Relic SSO の構成

次の手順に従って、New Relic で SSO を構成します。 

1. New Relic に[サインイン](https://login.newrelic.com/)します。

1. [認証ドメイン UI](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#ui) に移動します。 

1. Azure AD SSO を接続する認証ドメインを選択します (複数の認証ドメインがある場合)。 ほとんどの企業では、**Default (既定値)** という認証ドメインしかありません。 認証ドメインが 1 つしかない場合は、何も選択する必要はありません。

1. **[Authentication]\(認証\)** セクションで、 **[Configure]\(構成\)** を選択します。

   1. **[Source of SAML metadata]\(SAML メタデータのソース\)** には、以前に Azure AD の **[アプリのフェデレーション メタデータ URL]** フィールドから保存した値を入力します。

   1. **[SSO target URL]\(SSO ターゲット URL\)** には、以前に Azure AD の **[ログイン URL]** フィールドから保存した値を入力します。

   1. Azure AD 側と New Relic 側の両方で設定がうまくいっていることを確認した後、 **[保存]** を選択します。 どちらかの側が適切に構成されていない場合、ユーザーは New Relic にサインインできません。

### <a name="create-a-new-relic-test-user"></a>New Relic テスト ユーザーを作成する

このセクションでは、New Relic で B.Simon というユーザーを作成します。

1. New Relic に[サインイン](https://login.newrelic.com/)します。

1. [**ユーザー管理** ＵＩ](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#where) に移動します。

1. **[ユーザーの追加]** を選択します。

   1. **[Name]\(名前\)** には、「**B.Simon**」と入力します。
   
   1. **[Email]\(メール\)** には、Azure AD SSO によって送信される値を入力します。
   
   1. ユーザーの **[Type]\(種類\)** と **[Group]\(グループ\)** を選択します。 テスト ユーザーの場合、[Type]\(種類\) は **[Basic User]\(基本ユーザー\)** 、[Group]\(グループ\) は **[User]\(ユーザー\)** が適切な選択肢です。
   
   1. ユーザーを保存するには、 **[Add User]\(ユーザーの追加\)** を選択します。

> [!NOTE]
> New Relic では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./new-relic-by-organization-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる New Relic のサインオン URL にリダイレクトされます。  

* New Relic のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した New Relic に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [New Relic] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した New Relic に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

完了したら、[**ユーザー管理** UI](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#where) に移動してユーザーが表示されているか見ることで、New Relic に追加されたことを確認できます。 

次に、ユーザーを特定の New Relic アカウントまたはロールに割り当てる必要があります。 詳細については、[ユーザー管理の概念](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#understand-concepts)に関するセクションを参照してください。 

New Relic の認証ドメイン UI では、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御など、[その他の設定](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#session-mgmt)を構成できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
