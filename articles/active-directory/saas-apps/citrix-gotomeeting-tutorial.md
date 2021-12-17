---
title: チュートリアル:Azure Active Directory と GoToMeeting の統合 | Microsoft Docs
description: GoToMeeting と Azure Active Directory (Azure AD) を統合するために必要な手順について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: 918a78006b53a47253fa36a3b9a38e6de7993db1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307767"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-gotomeeting"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と GoToMeeting の統合

このチュートリアルでは、GoToMeeting と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と GoToMeeting を統合すると、次のことができます。

* GoToMeeting にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して GoToMeeting に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* GoToMeeting でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* GoToMeeting では、**IDP** Initiated SSO がサポートされます。
* GoToMeeting では、[自動化されたユーザー プロビジョニング](citrixgotomeeting-provisioning-tutorial.md)がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-gotomeeting-from-the-gallery"></a>ギャラリーからの GoToMeeting の追加

Azure AD への GoToMeeting の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に GoToMeeting を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**GoToMeeting**」と入力します。
1. 結果のパネルから **[GoToMeeting]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-gotomeeting"></a>GoToMeeting の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、GoToMeeting に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと GoToMeeting の関連ユーザーとの間にリンク関係を確立する必要があります。

GoToMeeting に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[GoToMeeting の SSO の構成](#configure-gotomeeting-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[GoToMeeting テスト ユーザーの作成](#create-gotomeeting-test-user)** - GoToMeeting で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **GoToMeeting** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://authentication.logmeininc.com/saml/sp` という URL を入力します。

    b. **[応答 URL]** ボックスに、URL として「`https://authentication.logmeininc.com/saml/acs`」と入力します。

    c. **[追加の URL を設定します]** をクリックして、以下の URL を構成します

    d. **サインオン URL** (空白のまま)

    e. **[リレー状態]** ボックスに、次の URL のいずれかを入力します。

   - GoToMeeting App の場合は、`https://global.gotomeeting.com` を使用します。

   - GoToTraining の場合は、`https://global.gototraining.com` を使用します。

   - GoToWebinar の場合は、`https://global.gotowebinar.com` を使用します。 

   - GoToAssist の場合は、`https://app.gotoassist.com` を使用します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[GoToMeeting のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に GoToMeeting へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[GoToMeeting]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-gotomeeting-sso"></a>GoToMeeting の SSO の構成

1. 別のブラウザー ウィンドウで、[GoToMeeting Organization Center](https://organization.logmeininc.com/) にログインします。 IdP が更新されていることを確認するように求められます。

2. [My Identity Provider has been updated with the new domain]\(マイ ID プロバイダーが新しいドメインで更新されました\) チェックボックスをオンにします。 終了したら、 **[完了]** をクリックします。

### <a name="create-gotomeeting-test-user"></a>GoToMeeting のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを GoToMeeting に作成します。 GoToMeeting では、Just-In-Time プロビジョニングがサポートされています。これは既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ GoToMeeting に存在しない場合は、GoToMeeting にアクセスしようとしたときに新しいユーザーが作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[GoToMeeting サポート チーム](https://support.logmeininc.com/gotomeeting)にお問い合わせください。

> [!NOTE]
>GoToMeeting では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./citrixgotomeeting-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した GoToMeeting に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 [マイ アプリ] で [GoToMeeting] タイルをクリックすると、SSO を設定した GoToMeeting に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

GoToMeeting を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
