---
title: 'チュートリアル: Azure AD SSO と SkyDesk Email の統合'
description: Azure Active Directory と SkyDesk Email の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/13/2021
ms.author: jeedes
ms.openlocfilehash: bf444b81f24a2ca45d72d9db23fefcc49673eb59
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317116"
---
# <a name="tutorial-azure-ad-sso-integration-with-skydesk-email"></a>チュートリアル: Azure AD SSO と SkyDesk Email の統合

このチュートリアルでは、SkyDesk Email と Azure Active Directory (Azure AD) を統合する方法について説明します。 SkyDesk Email と Azure AD を統合すると、次のことができます。

* SkyDesk Email にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して SkyDesk Email に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SkyDesk Email でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SkyDesk Email では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-skydesk-email-from-the-gallery"></a>ギャラリーからの SkyDesk Email の追加

Azure AD への SkyDesk Email の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SkyDesk Email を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SkyDesk Email**」と入力します。
1. 結果ウィンドウで **[SkyDesk Email]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-skydesk-email"></a>SkyDesk Email のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SkyDesk Email 用に Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SkyDesk Email の関連ユーザーとの間にリンク関係を確立する必要があります。

SkyDesk Email に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SkyDesk Email の SSO の構成](#configure-skydesk-email-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Skydesk Email のテスト ユーザーの作成](#create-skydesk-email-test-user)** - Skydesk Email で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SkyDesk Email** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://mail.skydesk.jp/portal/<companyname>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[SkyDesk Email クライアント サポート チーム](https://www.skydesk.jp/apps/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[SkyDesk Email のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B. Simon に SkyDesk Email へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SkyDesk Email]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-skydesk-email-sso"></a>SkyDesk Email の SSO を構成する

1. 別の Web ブラウザーから、管理者として SkyDesk Email アカウントにサインオンします。

1. 上部のメニューで、 **[設定]** をクリックし、 **[組織]** を選択します。

    ![[設定] メニューから [組織] が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/menu.png)
  
1. 左側のパネルで **[ドメイン]** をクリックします。

    ![コントロール パネルから [ドメイン] が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/domain.png)

1. **[ドメインの追加]** をクリックします。

    ![[ドメインの追加] が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/user.png)

1. 自分のドメイン名を入力し、ドメインを確認します。

    ![[ドメインの追加] タブのスクリーンショット。ここでドメインを入力することができます。](./media/skydeskemail-tutorial/details.png)

1. 左側のパネルで **[SAML Authentication (SAML 認証)]** をクリックします。

    ![コントロール パネルから [SAML Authentication]\(SAML 認証\) が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/authentication.png)

1. **[SAML 認証]** ダイアログ ページで、次の手順に従います。

    ![[SAML Authentication Details]\(SAML 認証の詳細\) ダイアログ ボックスのスクリーンショット。ここで、説明されている値を入力できます。](./media/skydeskemail-tutorial/portal.png)

    > [!NOTE]
    > SAML ベースの認証を使用するには、**ドメインが検証済み** または **ポータル URL** が設定済みである必要があります。 一意の名前でポータル URL を設定できます。

    ![名前を入力する [Portal U R L]\(ポータル U R L\) のスクリーンショット。](./media/skydeskemail-tutorial/file.png)

    a. **[ログイン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[ログアウト URL]** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    c. **[パスワード変更 URL]** は省略可能なので、空白のままにします。

    d. **[Get Key From File (ファイルからキーを取得)]** をクリックし、Azure portal からダウンロードした証明書を選択してから、 **[開く]** をクリックして証明書をアップロードします。

    e. **[アルゴリズム]** として **[RSA]** を選択します。

    f. **[OK]** をクリックして変更を保存します。

### <a name="create-skydesk-email-test-user"></a>SkyDesk Email のテスト ユーザーの作成

このセクションでは、SkyDesk Email で Britta Simon というユーザーを作成します。

SkyDesk Email 内の左側のパネルで **[User Access (ユーザー アクセス)]** をクリックし、自分のユーザー名を入力します。

![コントロール パネルから [User Access]\(ユーザー アクセス\) が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/create-users.png)

> [!NOTE]
> ユーザーを一括で作成する必要がある場合は、[SkyDesk Email クライアント サポート チーム](https://www.skydesk.jp/apps/support/)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SkyDesk Email のサインオン URL にリダイレクトされます。 

* SkyDesk Email のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SkyDesk Email] タイルをクリックすると、SkyDesk Email のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SkyDesk Email を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
