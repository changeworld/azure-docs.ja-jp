---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Onit の統合 | Microsoft Docs
description: Azure Active Directory Onit の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: 9f60ef41036c9d4b211c32d045607f8234308932
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307226"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Onit の統合

このチュートリアルでは、Onit と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Onit を統合すると、次のことができます。

* Onit にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Onit に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Onit でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Onit では、**SP** Initiated SSO がサポートされます。

## <a name="add-onit-from-the-gallery"></a>ギャラリーからの Onit の追加

Azure AD への Onit の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Onit を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Onit**」と入力します。
1. 結果のパネルから **[Onit]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-onit"></a>Onit の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Onit に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Onit の関連ユーザーとの間にリンク関係を確立する必要があります。

Onit に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Onit SSO の構成](#configure-onit-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Onit テスト ユーザーの作成](#create-onit-test-user)** - Onit で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Onit** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.onit.com`

    b. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.onit.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Onit クライアント サポート チーム](https://www.onit.com/support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

1. **[SAML 署名証明書]** セクションで **[Thumbprint Value]\(拇印の値\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

1. **[Onit のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Onit へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Onit]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-onit-sso"></a>Onit SSO の構成

1. 別の Web ブラウザー ウィンドウで、Onit 企業サイトに管理者としてログインします。

2. 上部のメニューで **[管理]** をクリックします。
   
    !["MS SSO テスト" ページの上部にあるメニューのスクリーンショット。[管理] アクションが選択されています。](./media/onit-tutorial/admin.png "管理")

3. **コーポレーションの編集** をクリックします。
   
    ![コーポレーションの編集](./media/onit-tutorial/corporation.png "コーポレーションの編集")
   
4. **[セキュリティ]** タブをクリックします。
    
    ![会社情報の編集](./media/onit-tutorial/security.png "会社情報の編集")

5. **[セキュリティ]** タブで、次の手順に従います。

    ![シングル サインオン](./media/onit-tutorial/configuration.png "[Single Sign-On]")

    a. **[認証方式]** として **[シングル サインオンとパスワード]** を選びます。
    
    b. **[Idp Target URL]\(Idp ターゲット URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[Idp logout URL]\(Idp ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[Idp Cert Fingerprint (SHA1)]\(Idp 証明書のフィンガープリント (SHA1)\)** テキスト ボックスに、Azure Portal からコピーした証明書の **[拇印]** 値を貼り付けます。

### <a name="create-onit-test-user"></a>Onit テスト ユーザーの作成

Azure AD ユーザーが Onit にログインできるようにするには、ユーザーを Onit にプロビジョニングする必要があります。 Onit の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Onit** 企業サイトに管理者としてログインします。

2. **[ユーザーの追加]** をクリックします。

    ![管理](./media/onit-tutorial/user.png "管理")

3. **[ユーザーの追加]** ダイアログ ページで、次の手順に従います。

    ![ユーザーの追加](./media/onit-tutorial/create-user.png "ユーザーの追加")

    a. プロビジョニングする有効な Azure AD アカウントの関連するテキストボックスに、 **[名前]** と **[電子メール アドレス]** を入力します。

    b. **Create** をクリックしてください。

    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Onit のサインオン URL にリダイレクトされます。 

* Onit のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Onit] タイルをクリックすると、Onit のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Onit を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
