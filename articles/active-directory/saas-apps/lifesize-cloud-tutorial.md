---
title: 'チュートリアル: Azure Active Directory と Lifesize Cloud の統合 | Microsoft Docs'
description: Azure Active Directory と Lifesize Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/24/2021
ms.author: jeedes
ms.openlocfilehash: 948a97d598710ad8cd10a3bf89dfd2cb7b512413
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313479"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>チュートリアル: Azure Active Directory と Lifesize Cloud の統合

このチュートリアルでは、Lifesize Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 Lifesize Cloud と Azure AD を統合すると、次のことができます。

* Lifesize Cloud にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Lifesize Cloud に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Lifesize Cloud でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Lifesize Cloud では、**SP** Initiated SSO がサポートされます。

* Lifesize Cloud では、**自動化された** ユーザー プロビジョニングがサポートされます。

## <a name="add-lifesize-cloud-from-the-gallery"></a>ギャラリーからの Lifesize Cloud の追加

Azure AD への Lifesize Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lifesize Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Lifesize Cloud**」と入力します。
1. 結果のパネルから **[Lifesize Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-lifesize-cloud"></a>Lifesize Cloud の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Lifesize Cloud に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Lifesize Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Lifesize Cloud に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Lifesize Cloud SSO の構成](#configure-lifesize-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Lifesize Cloud のテスト ユーザーの作成](#create-lifesize-cloud-test-user)** - Lifesize Cloud で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Lifesize Cloud** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://login.lifesizecloud.com/ls/?acs` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://login.lifesizecloud.com/<COMPANY_NAME>` の形式で URL を入力します。

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** ボックスに、`https://webapp.lifesizecloud.com/?ent=<IDENTIFIER>` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、リレー状態でこれらの値を更新します。 サインオン URL と識別子の値は、[Lifesize Cloud クライアント サポート チーム](https://legacy.lifesize.com/en/support)に問い合わせます。リレー状態の値は、このチュートリアルで後述する SSO 構成から取得できます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Lifesize Cloud のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Lifesize Cloud へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Lifesize Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-lifesize-cloud-sso"></a>Lifesize Cloud SSO の構成

1. アプリケーション用に構成された SSO を取得するには、管理者権限で Lifesize Cloud アプリケーションにログインします。

2. ページの右上にある自分の名前をクリックし、 **[詳細設定]** をクリックします。

    ![[Advanced Settings]\(詳細設定\) メニュー項目を示すスクリーンショット。](./media/lifesize-cloud-tutorial/settings.png)

3. [詳細設定] で **[SSO 構成]** のリンクをクリックします。 インスタンスの [SSO 構成] ページが開きます。

    ![[S S O Configuration]\(S S O 構成\) を選択できる [Advanced Settings]\(詳細設定\) を示すスクリーンショット。](./media/lifesize-cloud-tutorial/configuration.png)

4. SSO 構成 UI で、次の値を構成します。

    ![説明されている値を入力できる [S S O Configuration]\(S S O 構成\) ページを示すスクリーンショット。](./media/lifesize-cloud-tutorial/values.png)

    a. **[Identity Provider Issuer]\(ID プロバイダー発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    b.  **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X.509 証明書]** ボックスに貼り付けます。
  
    d. [名] ボックスの SAML 属性マッピングに、値を「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    e. **[姓]** ボックスの SAML 属性マッピングに、値を「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。

    f. **[電子メール]** ボックスの SAML 属性マッピングに、値を「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

5. 構成を確認するには、 **[テスト]** ボタンをクリックします。

    >[!NOTE]
    >テストを成功させるには、Azure AD の構成ウィザードを完了し、テストを実行するユーザーやグループにもアクセスを提供する必要があります。

6. **[SSO を有効にする]** ボタンをクリックして、SSO を有効にします。

7. **[更新]** ボタンをクリックして、すべての設定を保存します。 これにより、RelayState 値が生成されます。 テキスト ボックスに生成された RelayState の値をコピーし、 **[Lifesize Cloud のドメインと URL]** セクションの **[リレー状態]** ボックスに貼り付けます。

### <a name="create-lifesize-cloud-test-user"></a>Lifesize Cloud のテスト ユーザーの作成

このセクションでは、Lifesize Cloud で Britta Simon というユーザーを作成します。 Lifesize Cloud では、ユーザーの自動プロビジョニングがサポートされています。 Azure AD での認証に成功すると、ユーザーはアプリケーションで自動的にプロビジョニングされます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Lifesize Cloud のサインオン URL にリダイレクトされます。 

* Lifesize Cloud のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Lifesize Cloud] タイルをクリックすると、Lifesize Cloud のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Lifesize Cloud を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
