---
title: チュートリアル:Azure Active Directory と Brightidea の統合 | Microsoft Docs
description: Azure Active Directory と Brightidea の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: b2263122e9b009fe7b771f915614540fc7f60346
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338841"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>チュートリアル:Azure Active Directory と Brightidea の統合

このチュートリアルでは、Brightidea と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Brightidea を統合すると、次のことが可能になります。

* Brightidea にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Brightidea に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Brightidea でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Brightidea では、**SP および IDP** Initiated SSO がサポートされます。
* Brightidea では、**Just In Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-brightidea-from-the-gallery"></a>ギャラリーからの Brightidea の追加

Azure AD への Brightidea の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Brightidea を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Brightidea**」と入力します。
1. 結果のパネルから **[Brightidea]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-brightidea"></a>Brightidea の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Brightidea に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Brightidea の関連ユーザーとの間にリンク関係を確立する必要があります。

Brightidea に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Brightidea の SSO の構成](#configure-brightidea-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Brightidea のテスト ユーザーの作成](#create-brightidea-test-user)** - Brightidea で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Brightidea** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **サービス プロバイダー メタデータ ファイル** を保持しており、**IDP** によって開始されるモードに構成したい場合は、 **[基本的な SAML 構成]** セクション上で次の手順を実行します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、Brightidea セクションのテキスト ボックスに自動的に設定されます。

    > [!Note]
    > **識別子** と **応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.brightidea.com` という形式で URL を入力します。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Brightidea のセットアップ]** セクション上で、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Brightidea へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧上で **[Brightidea]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-brightidea-sso"></a>Brightidea SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者の資格情報を使って Brightidea にサインインします。

2. Brightidea システム内の SSO 機能にアクセスするために、 **[Enterprise Setup]\(エンタープライズ セットアップ\)**  ->  **[認証] タブ** の順に移動します。サブ タブとして[Auth Selection]\(認証の選択\) と [SAML Profiles]\(SAML プロファイル\) の 2 つが表示されます。

    ![このスクリーンショットは、[認証] タブが選択された状態の Brightidea サイトを示しています。](./media/brightidea-tutorial/authentication.png)

3. **[Auth Selection]\(認証の選択\)** を選択します。 既定では、2 つの標準的な方法として、"Brightidea ログイン" と "登録" だけが表示されます。 SSO の方法が追加されると、一覧に表示されるようになります。

    ![Brightidea の [認証] タブのスクリーンショット。[Auth Selection]\(認証の選択\) が選択されています。](./media/brightidea-tutorial/selection.png)

4. **[SAML Profiles]\(SAML プロファイル\)** を選択し、次の手順を実行します。

    ![Brightidea の [認証] タブのスクリーンショット。[Download Metadata]\(メタデータのダウンロード\) と [Add New]\(新規追加\) オプションを備えた [SAML Profiles]\(SAML プロファイル\) が選択されています。](./media/brightidea-tutorial/profile.png)

    a. **[メタデータのダウンロード]** をクリックして、Azure portal 内の **[基本的な SAML 構成]** セクションからアップロードを行います。

    b. **[Identity Provider Setting]\(ID プロバイダー設定\)** 下にある **[新規追加]** ボタンをクリックして、次の手順を実行します。

    ![情報の入力先となる Brightidea の [Identity Provider Setting]\(ID プロバイダー設定\) のスクリーンショット。](./media/brightidea-tutorial/metadata.png)

   * たとえば `Azure Ad SSO` のように、 **[SAML Profile Name]\(SAML プロファイル名\)** に入力します。

   * **[Upload Metadata]\(メタデータのアップロード\)** において、[ファイルの選択] をクリックして、Azure portal からダウンロード済みのメタデータ ファイルをアップロードします。

     > [!NOTE]
     > メタデータ ファイルをアップロードすると、残りのフィールド ( **[シングル サインオン サービス]、[ID プロバイダーの発行者]、[公開キーのアップロード ]** ) は自動的に入力されます。

   * **[電子メール]** ボックスに、`mail` の値を入力します。

   * **[画面の名前]** ボックスに、`givenName` の値を入力します。

   * **[変更を保存]** をクリックします。 

### <a name="create-brightidea-test-user"></a>Brightidea テスト ユーザーを作成する

このセクションでは、Brightidea 内に Britta Simon というユーザーを作成します。 Brightidea では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Brightidea にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Brightidea のサインオン URL にリダイレクトされます。  

* Brightidea のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Brightidea に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Brightidea] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Brightidea に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Brightidea を構成すると、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
