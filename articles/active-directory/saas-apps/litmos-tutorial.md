---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Litmos の統合 | Microsoft Docs'
description: Azure Active Directory と Litmos の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/12/2021
ms.author: jeedes
ms.openlocfilehash: 37b7a431c38de3742a2b13c98f753823c125f4cf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287972"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Litmos の統合

このチュートリアルでは、Litmos と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Litmos を統合すると、次のことができます。

* Litmos にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Litmos に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Litmos でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Litmos では、**IDP** Initiated SSO がサポートされています。
* Litmos では、**Just In Time** ユーザー プロビジョニングがサポートされています。

## <a name="add-litmos-from-the-gallery"></a>ギャラリーからの Litmos の追加

Azure AD への Litmos の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Litmos を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Litmos**」と入力します。
1. 結果のパネルから **[Litmos]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-litmos"></a>Litmos の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Litmos に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Litmos の関連ユーザーとの間にリンク関係を確立する必要があります。

Litmos に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Litmos の SSO の構成](#configure-litmos-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Litmos のテスト ユーザーの作成](#create-litmos-test-user)** - Litmos で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Litmos** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<companyname>.litmos.com/account/Login` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<companyname>.litmos.com/integration/samllogin` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は実際の識別子と応答 URL で更新してください。これらの値の取得方法については、チュートリアルの後半の説明を参照するか、[Litmos クライアント サポート チーム](https://www.litmos.com/contact-us)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Litmos のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Litmos へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Litmos]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-litmos-sso"></a>Litmos の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として Litmos 企業サイトにサインオンします。

2. 左側にあるナビゲーション バーで、 **[Accounts]** をクリックします。

    ![アプリ側の [Accounts]\(アカウント\) セクション](./media/litmos-tutorial/account.png)

3. **[Integrations]** タブをクリックします。

    ![[Integration]\(統合\) タブ](./media/litmos-tutorial/integrate.png)

4. **[Integrations]** タブで、下へスクロールして **[3rd Party Integrations]** を表示し、**[SAML 2.0]** タブをクリックします。

    ![[SAML 2.0] セクション](./media/litmos-tutorial/third-party.png)

5. **[The SAML endpoint for litmos is:]\(Litmos の SAML エンドポイント:\)** の値をコピーし、Azure Portal の **[Litmos のドメインと URL]** セクションの **[応答 URL]** テキストボックスに貼り付けます。

    ![SAML エンドポイント](./media/litmos-tutorial/certificate.png)

6. **Litmos** アプリケーションで、次の手順を実行します。

    ![Litmos アプリケーション](./media/litmos-tutorial/application.png)

    a. **[Enable SAML]** をクリックします。

    b. Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[SAML X.509 Certificate]** ボックスに貼り付けます。

    c. **[変更を保存]** をクリックします。

### <a name="create-litmos-test-user"></a>Litmos のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Litmos に作成します。 Litmos では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Litmos にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

**Litmos で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 別の Web ブラウザーのウィンドウで、管理者として Litmos 企業サイトにサインオンします。

2. 左側にあるナビゲーション バーで、 **[Accounts]** をクリックします。

    ![アプリ側の [Accounts]\(アカウント\) セクション](./media/litmos-tutorial/account.png)

3. **[Integrations]** タブをクリックします。

    ![[Integrations]\(統合\) タブ](./media/litmos-tutorial/integrate.png)

4. **[Integrations]** タブで、下へスクロールして **[3rd Party Integrations]** を表示し、**[SAML 2.0]** タブをクリックします。

    ![SAML 2.0](./media/litmos-tutorial/third-party.png)

5. **[Autogenerate Users]\(ユーザーを自動生成する\)** をオンにします
  
    ![[Autogenerate Users]\(ユーザーを自動生成する\)](./media/litmos-tutorial/users.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Litmos に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Litmos] タイルをクリックすると、SSO を設定した Litmos に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Litmos を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
