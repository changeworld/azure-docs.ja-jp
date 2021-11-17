---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Sentry の統合 | Microsoft Docs
description: Azure Active Directory と Sentry の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 6aa1d82982203a3d2c057fc0c4190dfbc7e60e71
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279882"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sentry"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Sentry の統合

このチュートリアルでは、Sentry と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Sentry を統合すると、次のことができます。

* Sentry にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Sentry に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Sentry でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Sentry では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Sentry では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-sentry-from-the-gallery"></a>ギャラリーからの Sentry の追加

Azure AD への Sentry の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Sentry を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Sentry**」と入力します。
1. 結果のパネルから **[Sentry]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-sentry"></a>Sentry の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Sentry に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Sentry の関連ユーザーとの間にリンク関係を確立する必要があります。

Sentry に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Sentry の SSO の構成](#configure-sentry-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Sentry のテスト ユーザーの作成](#create-sentry-test-user)** - Sentry で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Sentry** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://sentry.io/saml/metadata/<ORGANIZATION_SLUG>/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://sentry.io/saml/acs/<ORGANIZATION_SLUG>/` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://sentry.io/organizations/<ORGANIZATION_SLUG>/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の値の識別子、応答 URL、サインオン URL でこれらの値を更新してください。 これらの値の詳細については、[Sentry のドキュメント](https://docs.sentry.io/product/accounts/sso/azure-sso/#installation)を参照してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー アイコンをクリックして **[アプリのメタデータ URL]** をコピーし、コンピューターに保存します。

   ![証明書のダウンロードのリンク](common/copy-metadataurl.png)
    
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

このセクションでは、B.Simon に Sentry へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Sentry]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sentry-sso"></a>Sentry の SSO の構成

**Sentry** 側でシングル サインオンを構成するには、 **[組織の設定]**  >  **[認証]** に移動し (または `https://sentry.io/settings/<YOUR_ORG_SLUG>/auth/` に移動します)、Active Directory の **[構成]** を選択します。 Azure SAML 構成から [アプリのフェデレーション メタデータ URL] を貼り付けます。

### <a name="create-sentry-test-user"></a>Sentry のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Sentry に作成します。 Sentry では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Sentry にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

1. Azure portal で、 **[このアプリケーションをテストします]** を選択します。 サインイン フローを開始することができる Sentry のサインオン URL にリダイレクトされます。  

1. Sentry のサインオン URL に直接移動し、そこからサインイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で、 **[このアプリケーションをテストします]** を選択します。 SSO を設定した Sentry アプリケーションに自動的にサインインされます。 

#### <a name="either-mode"></a>両方のモード:

マイ アプリ ポータルを使用して、任意のモードでアプリケーションをテストできます。 マイ アプリ ポータルで [Sentry] タイルをクリックすると、SP モードで構成されている場合は、アプリケーションのサインオン ページにリダイレクトされ、サインイン フローが開始されます。 IDP モードで構成されている場合は、SSO を設定した Sentry アプリケーションに自動的にサインインされます。 マイ アプリ ポータルの詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)」を参照してください。

## <a name="next-steps"></a>次のステップ

Sentry を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
