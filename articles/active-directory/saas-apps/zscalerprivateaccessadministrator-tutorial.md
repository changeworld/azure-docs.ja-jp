---
title: 'チュートリアル: Azure Active Directory と Zscaler Private Access Administrator の統合 | Microsoft Docs'
description: Azure Active Directory と Zscaler Private Access Administrator の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 95cec5299405a8e5960eba46b4ee19b37383b829
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302313"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>チュートリアル: Azure Active Directory と Zscaler Private Access Administrator の統合

このチュートリアルでは、Zscaler Private Access Administrator と Azure Active Directory (Azure AD) を統合する方法について説明します。 Zscaler Private Access Administrator を Azure AD と統合すると、次のことが可能になります。

* Zscaler Private Access Administrator にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して、Zscaler Private Access Administrator に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Zscaler Private Access Administrator と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Zscaler Private Access Administrator でのシングル サインオンが有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zscaler Private Access Administrator では **SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。

## <a name="add-zscaler-private-access-administrator-from-the-gallery"></a>ギャラリーからの Zscaler Private Access Administrator の追加

Azure AD への Zscaler Private Access Administrator の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zscaler Private Access Administrator を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zscaler Private Access Administrator**」と入力します。
1. 結果パネルから **[Zscaler Private Access Administrator]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-private-access-administrator"></a>Zscaler Private Access Administrator の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zscaler Private Access Administrator に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Zscaler Private Access Administrator の関連ユーザーの間で、リンク関係を確立する必要があります。

Zscaler Private Access Administrator に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zscaler Private Access Administrator の SSO の構成](#configure-zscaler-private-access-administrator-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zscaler Private Access Administrator のテスト ユーザーの作成](#create-zscaler-private-access-administrator-test-user)** - Zscaler Private Access Administrator で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zscaler Private Access Administrator** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.private.zscaler.com/auth/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.private.zscaler.com/auth/sso` のパターンを使用して URL を入力します

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** テキスト ボックスに、値 `idpadminsso` を入力します。

5.  **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.private.zscaler.com/auth/sso` という形式で URL を入力します。   

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Zscaler Private Access Administrator クライアント サポート チーム](https://help.zscaler.com/zpa-submit-ticket)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Zscaler Private Access Administrator のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Zscaler Private Access Administrator へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Zscaler Private Access Administrator]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zscaler-private-access-administrator-sso"></a>Zscaler Private Access Administrator の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Zscaler Private Access Administrator 企業サイトに管理者としてサインインします。

2. 上部の **[Administration]** をクリックして **[AUTHENTICATION]** セクションに移動し、 **[IdP Configuration]** をクリックします。

    ![Zscaler Private Access Administrator の管理](./media/zscalerprivateaccessadministrator-tutorial/admin.png)

3. 右上隅の **[Add IdP Configuration]** をクリックします。 

    ![Zscaler Private Access Administrator の addidp](./media/zscalerprivateaccessadministrator-tutorial/add-configuration.png)

4. **[Add IdP Configuration]** ページで、次の手順に従います。
 
    ![Zscaler Private Access Administrator の idpselect](./media/zscalerprivateaccessadministrator-tutorial/select-file.png)

    a. **[IdP Metadata File Upload]** フィールドの **[Select File]** をクリックして、Azure AD からダウンロードしたメタデータ ファイルをアップロードします。

    b. Azure AD から **IdP メタデータ** が読み取られ、以下に示すようにすべてのフィールド情報が設定されます。

    ![Zscaler Private Access Administrator の idpconfig](./media/zscalerprivateaccessadministrator-tutorial/metadata.png)

    c. **[Administrator]** として **[Single Sign On]** を選択します。

    d. **[Domains]** フィールドから自分のドメインを選択します。
    
    e. **[保存]** をクリックします。

### <a name="create-zscaler-private-access-administrator-test-user"></a>Zscaler Private Access Administrator のテスト ユーザーの作成

Azure AD ユーザーが Zscaler Private Access Administrator にサインインできるようにするには、ユーザーを Zscaler Private Access Administrator にプロビジョニングする必要があります。 Zscaler Private Access Administrator の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Zscaler Private Access Administrator の会社サイトにサインインします。

2. 上部の **[Administration]** をクリックして **[AUTHENTICATION]** セクションに移動し、 **[IdP Configuration]** をクリックします。

    ![Zscaler Private Access Administrator の管理](./media/zscalerprivateaccessadministrator-tutorial/admin.png)

3. メニューの左側にある **[Administrators]** をクリックします。

    ![Zscaler Private Access Administrator の管理者](./media/zscalerprivateaccessadministrator-tutorial/administrator.png)

4. 右上隅の **[Add Administrator]** をクリックします。

    ![Zscaler Private Access Administrator の管理者の追加](./media/zscalerprivateaccessadministrator-tutorial/add-administrator.png)

5. **[Add Administrator]** ページで、次の手順に従います。

    ![Zscaler Private Access Administrator のユーザー管理](./media/zscalerprivateaccessadministrator-tutorial/user-admin.png)

    a. **[Username]** ボックスに、ユーザーのメール (BrittaSimon@contoso.com など) を入力します。

    b. **[Password]** ボックスに、ユーザーのパスワードを入力します。

    c. **[Confirm Password]** ボックスに、パスワードを入力します。

    d. **[Role]** に **[Zscaler Private Access Administrator]** を選択します。

    e. **[メール]** ボックスに、ユーザーのメール (BrittaSimon@contoso.com など) を入力します。

    f. **[Phone]** ボックスに、電話番号を入力します。

    g. **[Timezone]** ボックスで、タイム ゾーンを選択します。

    h. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zscaler Private Access Administrator のサインオン URL にリダイレクトされます。  

* Zscaler Private Access Administrator のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Zscaler Private Access Administrator に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Zscaler Private Access Administrator] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Zscaler Private Access Administrator に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Zscaler Private Access Administrator を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
