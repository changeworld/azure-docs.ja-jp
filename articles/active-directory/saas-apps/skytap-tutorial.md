---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Single Sign-on for Skytap の統合 | Microsoft Docs
description: Azure Active Directory と Single Sign-on for Skytap の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: d84a68a058fb4b4f1df5d3df3ac161121e4b6d5b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317050"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Single Sign-on for Skytap の統合

このチュートリアルでは、Single Sign-on for Skytap と Azure Active Directory (Azure AD) を統合する方法について説明します。 Single Sign-on for Skytap を Azure AD と統合すると、次のことができます。

* Single Sign-on for Skytap にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Single Sign-on for Skytap に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Single Sign-on for Skytap でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Single Sign-on for Skytap では、SP Initiated SSO と IDP Initiated SSO がサポートされます。

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>ギャラリーからの Single Sign-on for Skytap の追加

Azure AD への Single Sign-on for Skytap の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Single Sign-on for Skytap を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Single Sign-on for Skytap**」と入力します。
1. 結果のパネルから **[Single Sign-on for Skytap]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-single-sign-on-for-skytap"></a>Single Sign-on for Skytap の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Single Sign-on for Skytap に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Single Sign-on for Skytap の関連ユーザーとの間にリンク関係を確立します。

Single Sign-on for Skytap で Azure AD SSO を構成してテストする一般的な手順を次に示します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Single Sign-on for Skytap SSO の構成](#configure-single-sign-on-for-skytap-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Single Sign-on for Skytap のテスト ユーザーの作成](#create-single-sign-on-for-skytap-test-user)** - Single Sign-on for Skytap で B.Simon に対応するユーザーを作成します。 このユーザーを Azure AD の対応するユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Single Sign-on for Skytap** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)
   
1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`http://pingone.com/<custom EntityID>` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、URL として「`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`」と入力します。

1. 必要に応じて、 **[追加の URL を設定します]** を選択し、次の手順を実行することにより、**SP** Initiated モードでアプリケーションを構成することができます。

    a. **[サインオン URL]** ボックスに、`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>` という形式で URL を入力します。

    
    b. **[リレー状態]** ボックスに、次のパターンを使用する URL を入力します: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL、リレー状態でこれらの値を更新します。 これらの値を取得するには、[Single Sign-on for Skytap クライアント サポート チーム](mailto:support@skytap.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を見つけます。 **[ダウンロード]** を選択してメタデータ ファイルをダウンロードし、お使いのコンピューターに保存します。

    ![証明書のダウンロード リンクのスクリーンショット](common/metadataxml.png)

1. **[Single Sign-on for Skytap のセットアップ]** セクションで、要件に基づいて適切な 1 つまたは複数の URL をコピーします。

    ![構成 URL のコピーのスクリーンショット](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** フィールドに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Single Sign-on for Skytap へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Single Sign-on for Skytap]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-single-sign-on-for-skytap-sso"></a>Single Sign-on for Skytap SSO の構成

Single Sign-on for Skytap 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Single Sign-on for Skytap サポート チーム](mailto:support@skytap.com)に送信する必要があります。 この設定が構成され、SAML SSO 接続が両側で正しく行われます。

### <a name="create-single-sign-on-for-skytap-test-user"></a>Single Sign-on for Skytap のテスト ユーザーの作成

このセクションでは、Single Sign-on for Skytap で B.Simon というユーザーを作成します。 [Single Sign-on for Skytap クライアント サポート チーム](mailto:support@skytap.com)と連携して、Single Sign-on for Skytap プラットフォームにユーザーを追加してください。 ユーザーを作成してアクティブ化するまでは、シングル サインオンを使用できません。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Single Sign-on for Skytap のサインオン URL にリダイレクトされます。  

* Single Sign-on for Skytap のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Single Sign-on for Skytap に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Single Sign-on for Skytap] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Single Sign-on for Skytap に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Single Sign-on for Skytap を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
