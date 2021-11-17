---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と BlogIn の統合 | Microsoft Docs
description: Azure Active Directory と BlogIn の間にシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: c53810f2d313cbb2d4ca34f2776aa9123c2caf6c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349074"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blogin"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と BlogIn の統合

このチュートリアルでは、BlogIn と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と BlogIn を統合すると、次のことができます。

* BlogIn にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して BlogIn に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* BlogIn でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* BlogIn では、**SP と IDP** Initiated SSO がサポートされます。
* BlogIn では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。
* BlogIn では、[自動化されたユーザー プロビジョニング](blogin-provisioning-tutorial.md)がサポートされます。

## <a name="add-blogin-from-the-gallery"></a>ギャラリーからの BlogIn の追加

Azure AD への BlogIn の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に BlogIn を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**BlogIn**」と入力します。
1. 結果のパネルから **[BlogIn]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-blogin"></a>BlogIn の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、BlogIn に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと BlogIn の関連ユーザーとの間にリンク関係を確立する必要があります。

BlogIn に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[BlogIn SSO の構成](#configure-blogin-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[BlogIn のテスト ユーザーの作成](#create-blogin-test-user)** - BlogIn で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **BlogIn** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** Initiated モードで構成する場合は、次の手順を行います。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.blogin.co/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.blogin.co/sso/saml/callback` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.blogin.co/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらのフィールドの正確な値は、BlogIn の **[設定]** ページ ( **[User Athentication]\(ユーザー認証\)** タブ > **[Configure SSO and User Provisioning]\(SSO とユーザー プロビジョニングの構成\)** ) で取得できます。 または、[BlogIn クライアント サポート チーム](mailto:support@blogin.co)に連絡して、これらの値を取得することもできます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. BlogIn アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、BlogIn アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらを次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性 |
    | ------ | --------- |
    | title |user.jobtitle |
    

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

このセクションでは、B.Simon に BlogIn へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[BlogIn]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-blogin-sso"></a>BlogIn SSO の構成

**BlogIn** 側でシングル サインオンを構成するには、BlogIn アカウントにログインし、次の手順を実行します。

1. **[Settings]\(設定\)**  >  **[User Authentication]\(ユーザー認証\)**  >  **[Configure SSO & User provisioning]\(SSO とユーザー プロビジョニングの構成\)** に移動します。
2. 次の画面で、シングル サインオンの状態を **[On]\(オン\)** に変更し、ログイン画面に表示される SSO ログイン ボタンに対してカスタム名を選択します。

3. 前のセクションの最終手順で **アプリのフェデレーション メタデータ URL** を保存した場合、構成方法 **[メタデータ URL]** を選択し、**アプリのフェデレーション メタデータ URL** を [メタデータ URL] フィールドに貼り付けます。 それ以外の場合は、構成方法を **[手動]** に変更し、 **[Identity Provider SSO URL (Login URL)]\(ID プロバイダーの SSO URL (ログイン URL)\)** および **[Identity Provider Issuer (entity ID)]\(ID プロバイダーの発行者 (エンティティ ID)\)** に手動でデータを入力し、Azure AD から取得した **証明書 (base64)** をアップロードします。

4. SSO を使用して BlogIn に参加する新規ユーザーの既定のユーザー ロールを選択します。

5. **[変更の保存]** を選択します。

BlogIn での SSO の設定について詳しくは、[BlogIn で Microsoft Azure AD の SSO を設定する方法](https://blogin.co/blog/how-to-set-up-single-sign-on-sso-for-microsoft-azure-active-directory-azure-ad-267/)に関するページを参照してください。 ご不明な点がある場合やサポートが必要な場合は、いつでも [BlogIn サポート チーム](mailto:support@blogin.co)にお問い合わせください。

### <a name="create-blogin-test-user"></a>BlogIn テスト ユーザーの作成

このセクションでは、B. Simon というユーザーを BlogIn に作成します。 BlogIn では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 BlogIn にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

BlogIn では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./blogin-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる BlogIn のサインオン URL にリダイレクトされます。  

* BlogIn のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した BlogIn に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [BlogIn] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した BlogIn に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

BlogIn を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
