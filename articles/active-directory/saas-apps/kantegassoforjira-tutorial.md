---
title: 'チュートリアル: Azure Active Directory と Kantega SSO for JIRA の統合 | Microsoft Docs'
description: Azure Active Directory と Kantega SSO for JIRA の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: cb972e9ac5b04f4f27c3717fc7b99a4c2d3eff8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313764"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>チュートリアル: Azure Active Directory と Kantega SSO for JIRA の統合

このチュートリアルでは、Kantega SSO for JIRA と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Kantega SSO for JIRA を統合すると、次のことができます。

* Kantega SSO for JIRA にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Kantega SSO for JIRA に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Kantega SSO for JIRA と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

* Kantega SSO for JIRA でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Kantega SSO for JIRA では、**SP と IDP** Initiated SSO がサポートされます。

## <a name="add-kantega-sso-for-jira-from-the-gallery"></a>ギャラリーからの Kantega SSO for JIRA の追加

Azure AD への Kantega SSO for JIRA の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kantega SSO for JIRA を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに **[Kantega SSO for JIRA]** と入力します。
1. 結果のパネルから **[Kantega SSO for JIRA]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-jira"></a>Kantega SSO for JIRA の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Kantega SSO for JIRA で Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Kantega SSO for JIRA の関連ユーザーとの間にリンク関係を確立する必要があります。

Kantega SSO for JIRA で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Kantega SSO for JIRA SSO の構成](#configure-kantega-sso-for-jira-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Kantega SSO for JIRA のテスト ユーザーの作成](#create-kantega-sso-for-jira-test-user)** - Kantega SSO for JIRA で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Kantega SSO for JIRA** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は JIRA プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Kantega SSO for JIRA のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Kantega SSO for JIRA へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、**[Kantega SSO for JIRA]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-kantega-sso-for-jira-sso"></a>Kantega SSO for JIRA SSO の構成

1. 別の Web ブラウザー ウィンドウで、オンプレミス サーバーの JIRA に管理者としてサインインします。

1. 歯車をポイントし、 **[アドオン]** をクリックします。

    !["歯車" アイコンが選択され、ドロップダウンの [Add-ons]\(アドオン\) が強調表示されているスクリーンショット。](./media/kantegassoforjira-tutorial/settings.png)

1. [Add-ons]\(アドオン\) タブ セクションで、 **[Find new add-ons]\(新しいアドオンの検索\)** をクリックします。 **[Kantega SSO for JIRA (SAML & Kerberos)]** を検索し、 **[インストール]** をクリックして、新しい SAML プラグインをインストールします。

    ![検索ボックスに [Kantega SSO for JIRA (SAML & Kerberos)] があり、[インストール] ボタンが選択されている [Find new add-ons]\(新しいアドオンの検索\) セクションを示すスクリーンショット。](./media/kantegassoforjira-tutorial/install-tab.png)

1. プラグインのインストールが開始されます。

    ![プラグインの [Installing]\(インストール中\) ダイアログを示すスクリーンショット。](./media/kantegassoforjira-tutorial/installation.png)

1. インストールが完了したら、 **[閉じる]** をクリックします。

    !["Installed and ready to go!"\(インストールされ、使用できるようになりました\) と示すスクリーンショット。 [閉じる] アクションが選択されているダイアログ。](./media/kantegassoforjira-tutorial/close-tab.png)

1.  **Manage** をクリックします。

    ![[管理] ボタンが選択されている "Kantega SSO" アプリ ページを示すスクリーンショット。](./media/kantegassoforjira-tutorial/manage-tab.png)
    
1. 新しいプラグインが **[統合]** の下に一覧表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![左側のナビゲーション メニューの [INTEGRATIONS]\(統合\) が強調表示され、[Manage add-ons]\(アドオンの管理\) セクションで [Configuration]\(構成\) ボタンが選択されたスクリーンショット。](./media/kantegassoforjira-tutorial/integration.png)

1. **[SAML]** セクションに移動します。 **[Add identity provider]\(ID プロバイダーの追加\)** ボックスで **[Azure Active Directory (Azure AD)]** を選択します。

    ![[Add identity provider]\(ID プロバイダーの追加\) ドロップダウンで [Azure Active Directory (Azure AD)] が選択されているスクリーンショット。](./media/kantegassoforjira-tutorial/identity-provider.png)

1. サブスクリプション レベルは **[Basic]** を選択します。

    ![[Basic] が選択されている [Preparing Azure AD]\(Azure AD の準備\) セクションを示すスクリーンショット。](./media/kantegassoforjira-tutorial/basic-tab.png)

1. **[App properties]\(アプリのプロパティ\)** セクションで、次の手順を実行します。 

    ![[アプリケーション ID/URI] というテキストボックスと [コピー] ボタンが強調表示され、[次へ] ボタンが選択された [App properties]\(アプリのプロパティ\) セクションを示すスクリーンショット。](./media/kantegassoforjira-tutorial/properties.png)

    1. **[アプリケーション ID/URI]** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションで **識別子、応答 URL、サインオン URL** として使用します。

    1. **[次へ]** をクリックします。

1. **[Metadata import]\(メタデータのインポート\)** セクションで、次の手順を実行します。 

    ![[Metadata file on my computer]\(コンピューターにあるメタデータ ファイル\) が選択されている [Metadata import]\(メタデータのインポート\) セクションを示すスクリーンショット。](./media/kantegassoforjira-tutorial/metadata.png)

    1. **[Metadata file on my computer]\(コンピューターにあるメタデータ ファイル\)** を選び、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    1. **[次へ]** をクリックします。

1. **[Name and SSO location]\(名前と SSO の場所\)** セクションで、次の手順を実行します。

    ![[Identity provider name]\(ID プロバイダー名\) テキストボックスが強調表示され、[次へ] ボタンが選択されている [Name and SSO location]\(名前と SSO の場所\) を示すスクリーンショット。](./media/kantegassoforjira-tutorial/location.png)

    1. **[Identity provider name]\(ID プロバイダー名\)** ボックスに、ID プロバイダーの名前 (例: Azure AD) を追加します。

    1. **[次へ]** をクリックします。

1. 署名証明書を確認し、 **[Next]\(次へ\)** をクリックします。

    ![[次へ] ボタンが選択されている [Signature verification]\(署名の検証\) セクションを示すスクリーンショット。](./media/kantegassoforjira-tutorial/certificate.png)

1. **[JIRA user accounts]\(JIRA ユーザー アカウント\)** セクションで、次の手順を実行します。

    ![[Create users in JIRA's internal Directory if needed]\(必要に応じて JIRA の内部ディレクトリにユーザーを作成する\) オプションが強調表示され、[次へ] ボタンが選択されている [JIRA user accounts]\(JIRA ユーザー アカウント\) を示すスクリーンショット。](./media/kantegassoforjira-tutorial/accounts.png)

    1. **[Create users in JIRA's internal Directory if needed]\(必要に応じて JIRA の内部ディレクトリにユーザーを作成する\)** を選択して、ユーザー グループの適切な名前を入力します (グループはコンマで区切られた複数の番号 になる場合があります)。

    1. **[次へ]** をクリックします。

1. **[完了]** をクリックします。

    ![[完了] ボタンが選択されている [Summary]\(概要\) セクションを示すスクリーンショット。](./media/kantegassoforjira-tutorial/finish-tab.png)

1. **[Known domains for Azure AD]\(既知の Azure AD ドメイン\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforjira-tutorial/save-tab.png)

    1. ページの左側のパネルにある **[Known domains]\(既知のドメイン\)** を選択します。

    2. **[Known domains]\(既知のドメイン\)** ボックスにドメイン名を入力します。

    3. **[保存]** をクリックします。

### <a name="create-kantega-sso-for-jira-test-user"></a>Kantega SSO for JIRA のテスト ユーザーの作成

Azure AD ユーザーが JIRA にサインインできるようにするには、そのユーザーを JIRA にプロビジョニングする必要があります。 Kantega SSO for JIRA では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの JIRA にサインインします。

1. 歯車をポイントし、 **[User management]\(ユーザー管理\)** をクリックします。

    !["歯車" アイコンが選択され、ドロップダウンの [User management]\(ユーザー管理\) が強調表示されているスクリーンショット。](./media/kantegassoforjira-tutorial/user.png) 

1. **[ユーザー管理]** タブ セクションで、 **[ユーザーの作成]** をクリックします。

    ![[Create user]\(ユーザーの作成\) ボタンが選択されている [User management]\(ユーザー管理\) セクションを示すスクリーンショット。](./media/kantegassoforjira-tutorial/create-user.png) 

1. **[Create new user]\(新しいユーザーの作成\)** ダイアログ ページで、以下の手順を実行します。

    ![従業員の追加](./media/kantegassoforjira-tutorial/new-user.png) 

    1. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    2. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    3. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    4. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    5. **[Create user]\(ユーザーの作成\)** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Kantega SSO for JIRA のサインオン URL にリダイレクトされます。  

* Kantega SSO for JIRA のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Kantega SSO for JIRA に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Kantega SSO for JIRA] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Kantega SSO for JIRA に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Kantega SSO for JIRA を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
