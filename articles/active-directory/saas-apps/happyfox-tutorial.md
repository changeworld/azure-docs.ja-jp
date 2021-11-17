---
title: 'チュートリアル: Azure Active Directory と HappyFox の統合 | Microsoft Docs'
description: Azure Active Directory と HappyFox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: 9a48ec4cd9bbc62b3a8a54ecaab743749ca6c767
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291466"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>チュートリアル: Azure Active Directory と HappyFox の統合

このチュートリアルでは、HappyFox と Azure Active Directory (Azure AD) を統合する方法について説明します。 HappyFox を Azure AD と統合すると、次のことが可能になります。

* HappyFox にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して HappyFox に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* HappyFox でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* HappyFox では、**SP** Initiated SSO がサポートされています。

* HappyFox では、**Just-In-Time** ユーザー プロビジョニングがサポートされています。

## <a name="add-happyfox-from-the-gallery"></a>ギャラリーから HappyFox を追加する

Azure AD への HappyFox の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HappyFox を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**HappyFox**」と入力します。
1. 結果のパネルから **[HappyFox]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-happyfox"></a>HappyFox の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、HappyFox に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと HappyFox の関連ユーザーとの間にリンク関係を確立する必要があります。

HappyFox に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[HappyFox SSO の構成](#configure-happyfox-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[HappyFox のテスト ユーザーの作成](#create-happyfox-test-user)** - HappyFox で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **HappyFox** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.happyfox.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.happyfox.com/saml/metadata/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[HappyFox クライアント サポート チーム](https://support.happyfox.com/home)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[HappyFox のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に HappyFox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[HappyFox]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-happyfox-sso"></a>HappyFox SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として HappyFox テナントにサインオンします。

2. **[管理]** に移動し、 **[統合]** タブをクリックします。

    ![[Integrations]\(統合\) タブが選択されている [Manage]\(管理\) ページを示すスクリーンショット。](./media/happyfox-tutorial/header.png) 

3. [統合] タブで、 **[SAML 統合]** にある **[構成]** をクリックし、[シングル サインオンの設定] を開きます。

    ![[configure]\(構成\) アクションが選択されている [SAML Integration]\(SAML 統合\) 設定を示すスクリーンショット。](./media/happyfox-tutorial/configure.png)

4. [SAML configuration]\(SAML 構成\) セクションで、 **[SSO Target URL]\(SSO ターゲット URL\)** テキストボックスに Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    ![[SSO Target URL]\(SSO ターゲット URL\) テキストボックスが強調表示されている [SAML Configuration]\(SAML 構成\) セクションを示すスクリーンショット。](./media/happyfox-tutorial/target.png)

5. Azure Portal からダウンロードした証明書をメモ帳で開き、 **[IdP Signature]\(IdP 署名\)** セクションにコンテンツを貼り付けます。

    ![[IdP Signature]\(IdP 署名\) が強調表示されているスクリーンショット。](./media/happyfox-tutorial/certificate.png)

6. **[設定の保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/happyfox-tutorial/save-settings.png)

### <a name="create-happyfox-test-user"></a>HappyFox のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを HappyFox に作成します。 HappyFox では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 HappyFox にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、マイ アプリを使用して Azure AD のシングル サインオン構成をテストします。

1. マイ アプリで [HappyFox] タイルをクリックすると、HappyFox アプリケーションのログイン ページが表示されます。 サインイン ページに **‘SAML’** ボタンが表示されます。

    ![プラグイン](./media/happyfox-tutorial/apps.png)

2. **SAML** ボタンをクリックして、Azure AD アカウントを使って HappyFox にログインします

マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

HappyFox を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
