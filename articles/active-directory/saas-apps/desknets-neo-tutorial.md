---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と desknets NEO の統合 | Microsoft Docs'
description: Azure Active Directory と desknets NEO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 903c4beeb01899c47aada06f02fc5b24094dd2c3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348941"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と desknet's NEO の統合

このチュートリアルでは、desknet's NEO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と desknet's NEO を統合すると、次のことが可能になります。

* desknet's NEO にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して desknet's NEO に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* desknet's NEO でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* desknet's NEO では、**SP** Initiated SSO がサポートされます。

## <a name="adding-desknets-neo-from-the-gallery"></a>ギャラリーからの desknet's NEO の追加

Azure AD への desknet's NEO の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に desknet's NEO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**desknet's NEO**」と入力します。
1. 結果パネルから **[desknet's NEO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>desknet's NEO の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、desknet's NEO に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと desknet's NEO の関連ユーザーとの間にリンク関係を確立する必要があります。

desknet's NEO に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[desknet's NEO の SSO の構成](#configure-desknets-neo-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[desknet's NEO のテスト ユーザーの作成](#create-desknets-neo-test-user)** - desknet's NEO で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **desknet's NEO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<CUSTOMER_NAME>.dn-cloud.com`

    b. **[応答 URL]** ボックスに、`https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 これらの値を取得するには、[desknet's NEO クライアント サポート チーム](mailto:cloudsupport@desknets.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[desknet's NEO のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に desknet's NEO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[desknet's NEO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-desknets-neo-sso"></a>desknet's NEO の SSO の構成

1. desknet's NEO の企業サイトに管理者としてサインインします。

1. メニューの **[SAML authentication link settings]\(SAML 認証リンクの設定\)** アイコンをクリックします。

    ![[SAML authentication link settings]\(SAML 認証リンクの設定\) のスクリーンショット](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. **[Common settings]\(共通設定\)** の [SAML Authentication Collaboration]\(SAML 認証コラボレーション\) で **[use]\(使用\)** をクリックします。

    ![SAML 認証の使用のスクリーンショット。](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. **[SAML authentication link settings]\(SAML 認証リンクの設定\)** セクションで次の手順を実行します。

    ![[SAML authentication link settings]\(SAML 認証リンクの設定\) セクションのスクリーンショット。](./media/desknets-neo-tutorial/saml-authentication.png)

    a. **[Access URL]\(アクセス URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[SP Entity ID]\(SP エンティティ ID\)** ボックスに、Azure portal からコピーした **ID** の値を貼り付けます。

    c. **[Choose File]\(ファイルの選択\)** をクリックして、Azure portal からダウンロードした **証明書 (Base64)** ファイルを **[x.509 Certificate]\(X.509 証明書\)** ボックスにアップロードします。

    d. **[変更]** をクリックします。

### <a name="create-desknets-neo-test-user"></a>desknet's NEO のテスト ユーザーの作成

1. desknet's NEO の企業サイトに管理者としてサインインします。

1. **[menu]\(メニュー\)** の **[Administrator settings]\(管理者の設定\)** アイコンをクリックします。

    ![[Administrator settings]\(管理者の設定\) のスクリーンショット。](./media/desknets-neo-tutorial/administrator-settings.png)

1. **設定** アイコンをクリックし、 **[Custom settings]\(カスタム設定\)** の **[User management]\(ユーザー管理\)** を選択します。

    ![[User management]\(ユーザー管理\) の設定のスクリーンショット。](./media/desknets-neo-tutorial/user-management.png)

1. **[Create user information]\(ユーザー情報の作成\)** をクリックします。

    ![ユーザー情報ボタンのスクリーンショット。](./media/desknets-neo-tutorial/create-new-user.png)

1. 以下のページの必須フィールドを入力し、 **[creation]\(作成\)** をクリックします。

    ![ユーザー作成セクションのスクリーンショット。](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる desknet's NEO のサインオン URL にリダイレクトされます。 

* desknet's NEO のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [desknet's NEO] タイルをクリックすると、desknet's NEO のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

desknet's NEO を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
