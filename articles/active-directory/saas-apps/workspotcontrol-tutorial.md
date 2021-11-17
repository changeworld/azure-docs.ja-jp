---
title: チュートリアル:Azure Active Directory と Workspot Control の統合 | Microsoft Docs
description: Azure Active Directory と Workspot Control に対してシングル サインオンを構成する方法について学習します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: b8087fb44d2e40b01bac37da7193af7f3a45f13f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298733"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>チュートリアル:Azure Active Directory と Workspot Control の統合

このチュートリアルでは、Workspot Control と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Workspot Control を統合すると、次のことができます。

* Workspot Control にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Workspot Control に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Azure AD と Workspot Control の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

* Workspot Control のシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Workspot Control では、SP-initiated SSO と IDP-initiated SSO がサポートされます。

## <a name="add-workspot-control-from-the-gallery"></a>ギャラリーからの Workspot Control の追加

Azure AD への Workspot Control の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workspot Control を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Workspot Control**」と入力します。
1. 結果ウィンドウで **[Workspot Control]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-workspot-control"></a>Workspot Control の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Workspot Control に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Workspot Control の関連ユーザーとの間にリンク関係を確立する必要があります。

Workspot Control に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Workspot Control の SSO の構成](#configure-workspot-control-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Workspot Control テスト ユーザーの作成](#create-workspot-control-test-user)** - Workspot Control で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Workspot Control** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを IDP-initiated モードで構成する場合は、次の手順のようにします。

    1. **[識別子]** ボックスに、次の形式で URL を入力します。<br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata`

    1. **[応答 URL]** ボックスに、次の形式で URL を入力します。<br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion`

5. アプリケーションを SP-initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択します。

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。<br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を置き換えます。 これらの値を取得するには、[Workspot Control クライアント サポート チーム](mailto:support@workspot.com)に問い合わせてください。 または、Azure portal の **[基本的な SAML 構成]** セクションのパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** を選択し、要件に従って使用可能なオプションから **証明書 (Base64)** をダウンロードします。 それを自分のコンピューターに保存します。

    ![証明書 (Base64) ダウンロード リンク](common/certificatebase64.png)

7. **[Workspot Control の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Workspot Control へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Workspot Control]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-workspot-control-sso"></a>Workspot Control の SSO の構成

1. 異なる Web ブラウザー ウィンドウで、セキュリティ管理者として Workspot Control にサインインします。

2. ページの上部にあるツール バーで **[Setup]\(セットアップ\)** を選択し、次に **[SAML]** を選択します。

    ![セットアップ オプション](./media/workspotcontrol-tutorial/setup.png)

3. **[Security Assertion Markup Language Configuration]\(Security Assertion Markup Language の構成\)** ウィンドウで、次の手順のようにします。
 
    ![[Security Assertion Markup Language Configuration]\(Security Assertion Markup Language の構成\) ウィンドウ](./media/workspotcontrol-tutorial/security.png)

    1. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** を貼り付けます。

    1. **[Signon Service URL]\(サインオン サービス URL\)** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。

    1. **[Logout Service URL]\(ログアウト サービス URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** を貼り付けます。

    1. **[Update File]\(ファイルの更新\)** を選択して、Azure portal からダウンロードした base 64 でエンコードされた証明書を X.509 証明書にアップロードします。

    1. **[保存]** を選択します。

### <a name="create-workspot-control-test-user"></a>Workspot Control のテスト ユーザーを作成する

Azure AD ユーザーが Workspot Control にサインインできるようにするには、ユーザーを Workspot Control にプロビジョニングする必要があります。 プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順のようにします。**

1. セキュリティ管理者として Workspot Control にサインインします。

2. ページの上部にあるツール バーで **[Users]\(ユーザー\)** を選択し、次に **[Add User]\(ユーザーの追加\)** を選択します。

    ![[Users]\(ユーザー\) オプション](./media/workspotcontrol-tutorial/user.png)

3. **[Add a New User]\(新しいユーザーの追加\)** ウィンドウで、次の手順のようにします。

    ![[Add a New User]\(新しいユーザーの追加\) ウィンドウ](./media/workspotcontrol-tutorial/new-user.png)

    1. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    1. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    1. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: **Brittasimon@contoso.<i></i>com**)。

    1. **[Role]\(ロール\)** ドロップダウン リストから適切なユーザー ロールを選択します。

    1. **[Group]\(グループ\)** ドロップダウン リストから適切なユーザー グループを選択します。

    1. **[Add User]\(ユーザーの追加\)** を選択します。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Workspot Control のサインオン URL にリダイレクトされます。  

* Workspot Control のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Workspot Control に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで Workspot Control タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Workspot Control に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Workspot Control を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
