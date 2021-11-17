---
title: 'チュートリアル: Azure AD SSO と AlexisHR の統合'
description: Azure Active Directory と AlexisHR の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: d0cab8f8ff33f5e402a4303f9c40529052bd0053
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305304"
---
# <a name="tutorial-azure-ad-sso-integration-with-alexishr"></a>チュートリアル: Azure AD SSO と AlexisHR の統合

このチュートリアルでは、AlexisHR と Azure Active Directory (Azure AD) を統合する方法について説明します。 AlexisHR と Azure AD を統合すると、次のことができます。

* AlexisHR にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して AlexisHR に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AlexisHR でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* AlexisHR では、**IDP** Initiated SSO がサポートされます。

## <a name="add-alexishr-from-the-gallery"></a>ギャラリーから AlexisHR を追加する

Azure AD への AlexisHR の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AlexisHR を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**AlexisHR**」と入力します。
1. 結果のパネルから **[AlexisHR]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-alexishr"></a>AlexisHR のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、AlexisHR に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと AlexisHR の関連ユーザーとの間にリンク関係を確立する必要があります。

AlexisHR 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ AlexisHR の SSO の構成](#configure-alexishr-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[AlexisHR のテスト ユーザーの作成](#create-alexishr-test-user)** - AlexisHR で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **AlexisHR** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`urn:auth0:alexishr:<YOUR_CONNECTION_NAME>` の形式で値を入力します。

    b. **[応答 URL]** ボックスに、`https://auth.alexishr.com/login/callback?connection=<YOUR_CONNECTION_NAME>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[AlexisHR クライアント サポート チーム](mailto:support@alexishr.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. AlexisHR アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、AlexisHR アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性 |
    | ------------ | --------- |
    | email | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up AlexisHR]\(AlexisHR のセットアップ\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に AlexisHR へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[AlexisHR]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-alexishr-sso"></a>AlexisHR の SSO の構成

1. AlexisHR 企業サイトに管理者としてログインします。

1. **[Settings]\(設定\)**  >  **[SAML Single sign-on]\(SAML シングル サインオン\)** に移動して、 **[New identity provider]\(新しい ID プロバイダー\)** をクリックします。

1. **[New identity provider]\(新しい ID プロバイダー\)** セクションで、次の手順を実行します。

    ![[Account Settings]\(アカウント設定\) を示すスクリーンショット。](./media/alexishr-tutorial/account.png "設定")

    1. **[Identity provider SSO URL]\(ID プロバイダーの SSO URL\)** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. **[Identity provider sign out URL]\(ID プロバイダーのサインアウト URL\)** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    1. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[Public x509 certificate]\(公開 x509 証明書\)** テキストボックスに貼り付けます。

    1. **[Create identity provider]\(ID プロバイダーの作成\)** をクリックします。

1. ID プロバイダーを作成した後に、次の情報が表示されます。

    ![SSO の設定を示すスクリーンショット。](./media/alexishr-tutorial/certificate.png "SSO 構成")

    1. **[Audience URI]\(対象ユーザー URI\)** の値をコピーし、その値を Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

    1. **[Assertion Consumer Service URL]** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスに貼り付けます。

### <a name="create-alexishr-test-user"></a>AlexisHR のテスト ユーザーの作成

このセクションでは、AlexisHR で Britta Simon というユーザーを作成します。 [AlexisHR サポート チーム](mailto:support@alexishr.com)と連携して、AlexisHR プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した AlexisHR に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [AlexisHR] タイルをクリックすると、SSO を設定した Akamai に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

AlexisHR を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
