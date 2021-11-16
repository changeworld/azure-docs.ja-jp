---
title: チュートリアル:Azure Active Directory と SpringCM の統合 | Microsoft Docs
description: Azure Active Directory と SpringCM の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: b75de6a5495372a7d79b452b12d79a2612e64f6c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282806"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>チュートリアル:Azure Active Directory と SpringCM の統合

このチュートリアルでは、SpringCM と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SpringCM を統合すると、次のことができます。

* SpringCM にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して SpringCM に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

SpringCM と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SpringCM でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SpringCM では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-springcm-from-the-gallery"></a>ギャラリーからの SpringCM の追加

Azure AD への SpringCM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SpringCM を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SpringCM**」と入力します。
1. 結果のパネルから **[SpringCM]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-springcm"></a>SpringCM の Azure AD SSO の構成とテスト

**B. Simon** というテスト ユーザーを使用して、SpringCM に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと SpringCM の関連ユーザーとの間にリンク関係を確立する必要があります。

SpringCM に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SpringCM SSO の構成](#configure-springcm-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SpringCM テスト ユーザーの作成](#create-springcm-test-user)** - SpringCM で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SpringCM** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<IDENTIFIER>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[SpringCM クライアント サポート チーム](https://knowledge.springcm.com/support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (未加工)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[SpringCM の設定]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に SpringCM へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SpringCM]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-springcm-sso"></a>SpringCM SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として **SpringCM** 企業サイトにサインオンします。

1. 上部にあるメニューの **[GO TO]** をクリックし、 **[Preferences]** をクリックします。次に、 **[Account Preferences]** セクションで、 **[SAML SSO]** をクリックします。

    ![[SAML SSO]](./media/spring-cm-tutorial/preferences.png "SAML SSO")

1. [Identity Provider Configuration] セクションで、次の手順に従います。

    ![[Identity Provider Configuration]\(ID プロバイダーの構成\)](./media/spring-cm-tutorial/configuration.png "ID プロバイダー構成")

    a. ダウンロードした Azure Active Directory 証明書をアップロードするために、 **[Select Issuer Certificate]** または **[Change Issuer Certificate]** をクリックします。

    b. **[発行者]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    c. **[サービス プロバイダー (SP) によって開始されたエンドポイント]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[SAML Enabled]\(SAML の有効化\)** で **[Enable]\(有効\)** を選びます。

    e. **[保存]** をクリックします。

### <a name="create-springcm-test-user"></a>SpringCM テスト ユーザーの作成

Azure Active Directory ユーザーが SpringCM にサインインできるようにするには、そのユーザーを SpringCM にプロビジョニングする必要があります。 SpringCM の場合、プロビジョニングは手動で行います。

> [!NOTE]
> 詳細については、[Create and Edit a SpringCM User](http://community.springcm.com/s/article/Create-and-Edit-a-SpringCM-User-1619481053) (SpringCM ユーザーの作成と編集に関するページ) をご覧ください。 

**ユーザー アカウントを SpringCM にプロビジョニングするには、次の手順に従います。**

1. **SpringCM** 企業サイトに管理者としてサインインします。

1. **[GOTO]\(移動\)** をクリックし、 **[ADDRESS BOOK]\(アドレス帳\)** をクリックします。

    ![ユーザーの作成](./media/spring-cm-tutorial/user.png "[Create User]")

1. **[Create User]** をクリックします。

1. **[User Role]** を選択します。

1. **[Send Activation Email]** を選択します。

1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの姓名と電子メール アドレスを入力します。

1. ユーザーを **[Security group]** に追加します。

1. **[保存]** をクリックします。

   > [!NOTE]
   > 他の SpringCM ユーザー アカウント作成ツールや、SpringCM から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、SpringCM のサインオン URL にリダイレクトされ、そこでログイン フローを開始できます。 

* SpringCM のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SpringCM] タイルをクリックすると、SpringCM のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SpringCM を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
