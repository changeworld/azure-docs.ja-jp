---
title: 'チュートリアル: Azure Active Directory と LearnUpon の統合 | Microsoft Docs'
description: Azure Active Directory と LearnUpon の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: ba3b6e9eb68ff7365e3037d61463f7e5c8681cb3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344543"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>チュートリアル: Azure Active Directory と LearnUpon の統合

このチュートリアルでは、LearnUpon と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と LearnUpon を統合すると、次のことができます。

* LearnUpon にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LearnUpon に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

LearnUpon と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LearnUpon でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LearnUpon では、**IDP** Initiated SSO がサポートされます。

* LearnUpon では、**Just In Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-learnupon-from-the-gallery"></a>ギャラリーから LearnUpon を追加する

Azure AD への LearnUpon の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LearnUpon を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LearnUpon**」と入力します。
1. 結果のパネルから **[LearnUpon]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-learnupon"></a>LearnUpon の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、LearnUpon に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと LearnUpon の関連ユーザーとの間にリンク関係を確立する必要があります。

LearnUpon に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LearnUpon SSO の構成](#configure-learnupon-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[LearnUpon テスト ユーザーの作成](#create-learnupon-test-user)** - Azure AD の B.Simon にリンクさせるために、対応するユーザーを LearnUpon で作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **LearnUpon** アプリケーション統合ページで **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[応答 URL]** ボックスに、`https://<companyname>.learnupon.com/saml/consumer` のパターンを使用して URL を入力します

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[LearnUpon クライアント サポート チーム](https://www.learnupon.com/contact/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページで、 **[拇印]** を確認します。これが LearnUpon SAML 設定に追加されます。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[LearnUpon のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に LearnUpon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LearnUpon]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-learnupon-sso"></a>LearnUpon SSO の構成

1. 別のブラウザー インスタンスを開き、管理者アカウントを使用して LearnUpon にサインインします。

1. **[settings (設定)]** タブをクリックします。

    ![設定タブのスクリーンショット。](./media/learnupon-tutorial/settings.png)

1. **[Single Sign On - SAML (シングル サインオン - SAML)]** 、 **[General Settings (全般設定)]** の順にクリックし、SAML 設定を構成します。
   
    ![[Single Sign On - SAML]\(シングル サインオン - SAML\) で [全般設定] が選択された状態を示したスクリーンショット。](./media/learnupon-tutorial/general-settings.png) 

1. **[General Settings (全般設定)]** セクションで、次の手順に従います。
   
    ![ここで説明されている値を入力できる [全般設定] セクションのスクリーンショット。](./media/learnupon-tutorial/values.png)  
  
    a. **[Enabled]** を選択します。

    b. **[Version]\(バージョン\)** として **[2.0]** を選択します。

    c. **[Skip conditions]\(条件をスキップする\)** で **[いいえ]** を選択します。

    d. **[SAML Token POST param name (SAML トークン POST パラメーター名)]** ボックスに、前述の SAML コンシューマー URL に対する POST 要求パラメーターの名前を入力します。ここには、確認と認証の対象である SAML アサーションが含まれます (**SAMLResponse** など)。

    e. **[Name Identifier Format]\(名前識別子形式\)** テキストボックスに、SAML アサーション内のユーザー ID (メール アドレス) の場所を示す値を入力します (例: `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`)。
  
    f. Azure Portal のログイン画面でアップロード済みアイコンをクリックした際のユーザーの移動先を示す値を、 **[Identify Provider Location]\(プロバイダーの場所を特定\)** ボックスに入力します。
  
    g. **[Sign Out URL]\(サインアウト URL\)** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    h. **[Manage finger prints (指紋の管理)]** をクリックし、ダウンロードした証明書の指紋をアップロードします。

1. **[User Settings (ユーザー設定)]** をクリックし、次の手順を実行します。

     ![ここで説明されている値を入力できる [ユーザー設定] セクションのスクリーンショット。](./media/learnupon-tutorial/user-settings.png)  

    a. **[First Name Identifier Format]\(名識別子形式\)** テキストボックスに、SAML アサーション内のユーザーの名の場所を示す値を入力します (例: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`)。
  
    b. **[Last Name Identifier Format]\(姓識別子形式\)** テキストボックスに、SAML アサーション内のユーザーの姓の場所を示す値を入力します (例: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`)。

### <a name="create-learnupon-test-user"></a>LearnUpon テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを LearnUpon に作成します。 LearnUpon では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 LearnUpon にユーザーがまだ存在していない場合は、認証後に新規に作成されます。 ユーザーを手動で作成する必要がある場合は、[LearnUpon のサポート チーム](https://www.learnupon.com/contact/)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した LearnUpon に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリの [LearnUpon] タイルをクリックすると、SSO を設定した LearnUpon に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

LearnUpon を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
