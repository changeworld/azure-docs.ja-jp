---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Wootric の統合 | Microsoft Docs
description: Azure Active Directory と Wootric の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: ef484784517b93a2fa935d558aed29598d782e42
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316434"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wootric"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Wootric の統合

このチュートリアルでは、Wootric と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Wootric を統合すると、次のことができます。

* Wootric にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Wootric に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Wootric でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Wootric では、**IDP** Initiated SSO がサポートされています。
* Wootric では、**Just-In-Time** ユーザー プロビジョニングがサポートされています。

## <a name="adding-wootric-from-the-gallery"></a>ギャラリーからの Wootric の追加

Azure AD への Wootric の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Wootric を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Wootric**」と入力します。
1. 結果のパネルから **[Wootric]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-wootric"></a>Wootric の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Wootric に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Wootric の関連ユーザーとの間にリンク関係を確立する必要があります。

Wootric に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Wootric の SSO の構成](#configure-wootric-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Wootric のテスト ユーザーの作成](#create-wootric-test-user)** - Wootric で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Wootric** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。


1. Wootric アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Wootric アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性 |
    | -------------- | --------- |
    | id | user.objectid |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Wootric のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Wootric へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Wootric]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-wootric-sso"></a>Wootric の SSO の構成

1. Wootric 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Set up Wootric]\(Wootric のセットアップ\)** をクリックすると、Wootric アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Wootric にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Wootric を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Wootric 企業サイトに管理者としてサインインします。

1. 上部のメニューで **[Settings]\(設定\) アイコン** をクリックします。

    ![Wootric サイトから [Settings]\(設定\) アイコンが選択されていることを示すスクリーンショット。](./media/wootric-tutorial/configure-1.PNG)

1. **[INTEGRATIONS]\(統合\)** で、左側のメニューの **[Authentication]\(認証\)** を選択し、 **[Enable Single Sign On with Azure Active Directory]\(Azure Active Directory でのシングル サインオンを有効にする\)** をクリックします。

    ![[Authentication]\(認証\) 項目で [Enable Single Sign On with Azure Active Directory]\(Azure Active Directory でのシングル サインオンを有効にする\) が [connected]\(接続済み\) になっていることを示すスクリーンショット。](./media/wootric-tutorial/configure-2.PNG)

1. 次のページで、以下の手順を実行します。

    ![[Settings]\(設定\) ページを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/wootric-tutorial/configure-3.PNG)

    a. **[ID プロバイダー のシングル サインオン URL]** テキストボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    b. **[Identity Provider Issuer]\(ID プロバイダー発行者\)** ボックスに、Azure portal からコピーした **エンティティ ID** の値を貼り付けます。

    c. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    d. **[Automatically grant access to new users]\(新しいユーザーへのアクセスを自動的に許可する\)** チェック ボックスをオンにします。
    
    e. **[Save]** をクリックします。

### <a name="create-wootric-test-user"></a>Wootric のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Wootric に作成します。 Wootric では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Wootric にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Wootric に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Wootric] タイルをクリックすると、SSO を設定した Wootric に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Wootric を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
