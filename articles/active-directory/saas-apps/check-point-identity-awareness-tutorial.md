---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Check Point Identity Awareness の統合 | Microsoft Docs'
description: Azure Active Directory と Check Point Identity Awareness の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2021
ms.author: jeedes
ms.openlocfilehash: 7cdfa5f3e36b36862d4b96cf3b15d6d97bbd7a58
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344848"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Check Point Identity Awareness の統合

このチュートリアルでは、Check Point Identity Awareness と Azure Active Directory (Azure AD) を統合する方法について説明します。 Check Point Identity Awareness と Azure AD を統合すると、次のことができます。

* Check Point Identity Awareness にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Check Point Identity Awareness に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Check Point Identity Awareness でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Check Point Identity Awareness では、**SP** Initiated SSO がサポートされます。

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>ギャラリーからの Check Point Identity Awareness の追加

Azure AD への Check Point Identity Awareness の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Check Point Identity Awareness を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Check Point Identity Awareness**」と入力します。
1. 結果のパネルから **[Check Point Identity Awareness]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Check Point Identity Awareness の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Check Point Identity Awareness に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Check Point Identity Awareness の関連ユーザーとの間にリンク関係を確立する必要があります。

Check Point Identity Awareness に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Check Point Identity Awareness の SSO の構成](#configure-check-point-identity-awareness-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Check Point Identity Awareness のテスト ユーザーの作成](#create-check-point-identity-awareness-test-user)** - Check Point Identity Awareness で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Check Point Identity Awareness** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. **[応答 URL]** ボックスに、`https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 これらの値を取得するには、[Check Point Identity Awareness クライアント サポート チーム](mailto:support@checkpoint.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Check Point Identity Awareness のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Check Point Identity Awareness へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Check Point Identity Awareness]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-check-point-identity-awareness-sso"></a>Check Point Identity Awareness の SSO の構成

1. Check Point Identity Awareness 企業サイトに管理者としてサインインします。

1. SmartConsole > **[Gateways & Servers]\(ゲートウェイとサーバー\)** ビューで、 **[New]\(新規\) > [More]\(詳細\) > [User/Identity]\(ユーザーまたは ID\) > [Identity Provider]\(ID プロバイダー\)** をクリックします。

    ![新しい ID プロバイダーのスクリーンショット。](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. **[New Identity Provider]\(新しい ID プロバイダー\)** ウィンドウで、次の手順を実行します。

    ![[Identity Provider]\(ID プロバイダー\) セクションのスクリーンショット。](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. **[Gateway]\(ゲートウェイ\)** フィールドで、SAML 認証を実行する必要があるセキュリティ ゲートウェイを選択します。

    b. **[Service]\(サービス\)** フィールドで、ドロップダウンから **[Identity Awareness]\(ID 認識\)** を選択します。

    c. **[Identifier(Entity ID)]\(識別子 (エンティティ ID)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** テキスト ボックスにこの値を貼り付けます。

    d. **[Reply URL]\(応答 URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスにこの値を貼り付けます。

    e. **[Import Metadata File]\(メタデータ ファイルのインポート\)** を選択して、Azure portal からダウンロードした **フェデレーション メタデータ XML** をアップロードします。

    > [!NOTE]
    > または、 **[Insert Manually]\(手動で挿入\)** を選択した後、**エンティティ ID** と **ログイン URL** の値を対応するフィールドに手動で貼り付け、Azure portal から取得した **証明書ファイル** をアップロードすることもできます。

    f. **[OK]** をクリックします。

### <a name="create-check-point-identity-awareness-test-user"></a>Check Point Identity Awareness のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Check Point Identity Awareness に作成します。 [Check Point Identity Awareness サポート チーム](mailto:support@checkpoint.com)と連携して、Check Point Identity Awareness プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Check Point Identity Awareness のサインオン URL にリダイレクトされます。 

* Check Point Identity Awareness のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Check Point Identity Awareness] タイルをクリックすると、Check Point Identity Awareness のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Check Point Identity Awareness を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
