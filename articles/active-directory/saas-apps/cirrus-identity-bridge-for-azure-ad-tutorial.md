---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cirrus Identity Bridge for Azure AD の統合 | Microsoft Docs
description: Azure Active Directory と Cirrus Identity Bridge for Azure AD の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: jeedes
ms.openlocfilehash: 4046fdafdcd3d661bc608ccf29d73a647e5c5acd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cirrus-identity-bridge-for-azure-ad"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cirrus Identity Bridge for Azure AD の統合

このチュートリアルでは、Cirrus Identity Bridge for Azure AD と Azure Active Directory (Azure AD) を統合する方法について説明します。 Cirrus Identity Bridge for Azure AD と Azure AD を統合すると、次のことができます。

* Cirrus Identity Bridge for Azure AD にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Cirrus Identity Bridge for Azure AD に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Cirrus Identity Bridge for Azure AD でのシングル サインオン (SSO) が有効になったサブスクリプション。 まだサブスクライバーではない場合は、[Cirrus Identity Azure AD Bridge 登録ページ](https://info.cirrusidentity.com/cirrus-identity-azure-ad-app-gallery-registration)にアクセスしてください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Cirrus Identity Bridge for Azure AD では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。

## <a name="add-cirrus-identity-bridge-for-azure-ad-from-the-gallery"></a>ギャラリーからの Cirrus Identity Bridge for Azure AD の追加

Azure AD への Cirrus Identity Bridge for Azure AD の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Cirrus Identity Bridge for Azure AD を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Cirrus Identity Bridge for Azure AD**」と入力します。
1. 結果のパネルから **[Cirrus Identity Bridge for Azure AD]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-cirrus-identity-bridge-for-azure-ad"></a>Cirrus Identity Bridge for Azure AD のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cirrus Identity Bridge for Azure AD 用に Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Cirrus Identity Bridge for Azure AD の関連ユーザーとの間にリンク関係を確立する必要があります。

Cirrus Identity Bridge for Azure AD 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Cirrus Identity Bridge for Azure AD の SSO の構成](#configure-cirrus-identity-bridge-for-azure-ad-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Cirrus Identity Bridge for Azure AD のテストの設定](#setup-cirrus-identity-bridge-for-azure-ad-testing)** - Cirrus Identity Bridge for Azure AD で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Cirrus Identity Bridge for Azure AD** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.cirrusidentity.com/bridge`

    b. **[応答 URL]** ボックスに、`https://<NAME>.proxy.cirrusidentity.com/module.php/saml/sp/saml2-acs.php/<NAME>_proxy` のパターンを使用して URL を入力します

1. アプリケーションを SP 開始モードで構成する場合は、 [追加の URL を設定します] をクリックして次の手順を実行します。
 
    **[サインオン URL]** テキストボックスに、`<CUSTOMER_LOGIN_URL>` という形式で値を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 Cirrus Bridge をまだサブスクライブしていない場合は、[登録ページ](https://info.cirrusidentity.com/cirrus-identity-azure-ad-app-gallery-registration)にアクセスしてください。 既存の Cirrus Bridge のお客様の場合は、[Cirrus Identity Bridge for Azure AD Client サポート チーム](https://www.cirrusidentity.com/resources/service-desk)に連絡して、これらの値を取得してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Cirrus Identity Bridge for Azure AD アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Cirrus Identity Bridge for Azure AD アプリケーションでは、さらにいくつかの属性が SAML 応答で返されることが想定されています。それらの属性を下に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | ---------| --------- |
    | displayName | user.displayname |

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

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

このセクションでは、Cirrus Identity Bridge for Azure AD へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cirrus Identity Bridge for Azure AD]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cirrus-identity-bridge-for-azure-ad-sso"></a>Cirrus Identity Bridge for Azure AD の SSO の構成

**Cirrus Identity Bridge for Azure AD** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [Cirrus Identity Bridge for Azure AD サポート チーム](https://www.cirrusidentity.com/resources/service-desk)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="setup-cirrus-identity-bridge-for-azure-ad-testing"></a>Cirrus Identity Bridge for Azure AD のテストの設定

このセクションでは、Britta Simon というユーザーをテストに使用できることを確認します。 [Cirrus Identity Bridge for Azure AD サポート チーム](https://www.cirrusidentity.com/resources/service-desk)から、Cirrus Identity Bridge for Azure AD プラットフォームで Britta Simon を使用する準備ができていることを確認するためのテスト URL が届きます。 テスト ユーザーの Britta Simon は、Cirrus Identity Bridge for Azure AD を認証方法として使用するアプリケーション (多角的なフェデレーション メタデータを使用するアプリケーションなど) にも追加する必要があります。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、Cirrus Identity Bridge for Azure AD のサインオン URL にリダイレクトされ、そこからログイン フローを開始できます。  

* Cirrus Identity Bridge for Azure AD のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Cirrus Identity Bridge for Azure AD に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Cirrus Identity Bridge for Azure AD] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Cirrus Identity Bridge for Azure AD に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Cirrus Identity Bridge for Azure AD を構成したら、あなたの組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
