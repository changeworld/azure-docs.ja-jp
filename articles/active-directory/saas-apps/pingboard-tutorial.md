---
title: 'チュートリアル: Azure Active Directory と PingBoard の統合 | Microsoft Docs'
description: Azure Active Directory と PingBoard の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: jeedes
ms.openlocfilehash: 286176b662f9f02d2d9dd39a1e13cc010ae134e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329826"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>チュートリアル: Azure Active Directory と PingBoard の統合

このチュートリアルでは、PingBoard と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Pingboard を統合すると、次のことが可能になります。

* Pingboard にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Pingboard に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Pingboard でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Pingboard では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。

* Pingboard では、[自動化されたユーザー プロビジョニング](./pingboard-provisioning-tutorial.md)がサポートされます。 

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-pingboard-from-the-gallery"></a>ギャラリーからの PingBoard の追加

Azure AD への PingBoard の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PingBoard を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Pingboard**」と入力します。
1. 結果のパネルから **[Pingboard]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-pingboard"></a>Pingboard の Azure AD SSO の構成とテスト

**B. Simon** というテスト ユーザーを使用して、Pingboard に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Pingboard の関連ユーザーとの間にリンク関係を確立する必要があります。

Pingboard に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Pingboard SSO の構成](#configure-pingboard-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Pingboard のテスト ユーザーの作成](#create-pingboard-test-user)** - Pingboard で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Pingboard** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`http://app.pingboard.com/sp` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<ENTITY_ID>.pingboard.com/auth/saml/consume` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.pingboard.com/sign_in` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 これらの値を取得するには、[PingBoard クライアント サポート チーム](https://support.pingboard.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Pingboard の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Pingboard へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[PingBoard]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-pingboard-sso"></a>Pingboard SSO の構成

1. Pingboard 側で SSO を構成するには、新しいブラウザー ウィンドウを開き、Pingboard アカウントにサインインします。 シングル サインオンを設定するには、PingBoard 管理者である必要があります。

2. 上部のメニューで、 **[アプリ]、[統合]** の順に選択します。

    ![Configure single sign-on](./media/pingboard-tutorial/integration.png)

3. **[Integrations]** ページで、 **[Azure Active Directory]** タイルを探してクリックします。

    ![PingBoard シングル サインオン統合](./media/pingboard-tutorial/directory.png)

4. 続いて表示されるモーダルで、 **[Configure]** をクリックします。

    ![PingBoard 構成ボタン](./media/pingboard-tutorial/configure.png)

5. 次のページに、"Azure SSO 統合が有効になっている" ことを示す通知が表示されます。 ダウンロードしたメタデータ XML ファイルをメモ帳で開き、コンテンツを **[IDP Metadata]** に貼り付けます。

    ![PingBoard SSO 構成画面](./media/pingboard-tutorial/metadata.png)

6. ファイルが検証されます。すべて正しい場合は、シングル サインオンが有効になります。

### <a name="create-pingboard-test-user"></a>Pingboard テスト ユーザーの作成

このセクションの目的は、Pingboard に Bitta Simon というユーザーを作成することです。 Pingboard では、　自動ユーザー プロビジョニングがサポートされています。この設定は、規定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](pingboard-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. Pingboard 企業サイトに管理者としてサインインします。

2. **[Directory]** ページで **[Add Employee]** をクリックします。

    ![従業員の追加](./media/pingboard-tutorial/test-user.png)

3. **[従業員の追加]** ダイアログ ページで、次の手順に従います。

    ![[ユーザーの招待]](./media/pingboard-tutorial/create-name.png)

    a. **[フル ネーム]** ボックスに、ユーザーの氏名 (**Britta Simon** など) を入力します。

    b. **[メール]** ボックスに、ユーザーのメール アドレス ( **brittasimon@contoso.com** など) を入力します。

    c. **[Job Title]** ボックスに、Britta Simon の役職を入力します。

    d. **[Location]** ドロップダウン リストで、Britta Simon の場所を選択します。

    e. **[追加]** をクリックします。

4. ユーザーの追加を確認するための確認画面が表示されます。

    ![確認](./media/pingboard-tutorial/confirm-user.png)

    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Pingboard のサインオン URL にリダイレクトされます。  

* Pingboard のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Pingboard に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Pingboard] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Pingboard に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Pingboard を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を強制することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
