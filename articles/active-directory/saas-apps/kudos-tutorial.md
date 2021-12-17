---
title: 'チュートリアル: Azure Active Directory と Kudos の統合 | Microsoft Docs'
description: Azure Active Directory と Kudos の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: 0f7d9c9516d0712b6a23d43857af70a2b239a5f4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290979"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>チュートリアル: Azure Active Directory と Kudos の統合

このチュートリアルでは、Kudos と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Kudos を統合すると、次のことができます。

* Kudos にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Kudos に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Kudos と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Kudos でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Kudos では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-kudos-from-the-gallery"></a>ギャラリーからの Kudos の追加

Azure AD への Kudos の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kudos を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに **[Kudos]** と入力します。
1. 結果のパネルから **[Kudos]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-kudos"></a>Kudos の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Kudos で Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Kudos の関連ユーザーとの間にリンク関係を確立する必要があります。

Kudos で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Kudos SSO の構成](#configure-kudos-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Kudos のテスト ユーザーの作成](#create-kudos-test-user)** - Kudos で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Kudos** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<COMPANY>.kudosnow.com` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Kudos クライアント サポート チーム](http://success.kudosnow.com/home)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Set up Kudos]\(Kudos の設定\)** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に Kudos へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Kudos]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-kudos-sso"></a>Kudos SSO の構成

1. 別の Web ブラウザーのウィンドウで、Kudos 企業サイトに管理者としてサインインします。

1. 上部のメニューで **[設定] アイコン** をクリックします。

    ![設定](./media/kudos-tutorial/menu.png "設定")

1. **[Integrations]\(統合\) > [SSO]** をクリックして、次の手順に従います。

    ![SSO](./media/kudos-tutorial/account.png "SSO")

    a. **[サインオン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. base-64 でエンコードされた証明書をメモ帳で開き、内容をクリップボードにコピーし、[**X.509 証明書**] ボックスに貼り付けます。

    c. **[Logout To URL]\(ログアウト先 URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. [**Your Kudos URL**] ボックスに、会社名を入力します。

    e. **[保存]** をクリックします。

### <a name="create-kudos-test-user"></a>Kudos のテスト ユーザーの作成

Azure AD ユーザーが Kudos にサインインできるようにするには、そのユーザーを Kudos にプロビジョニングする必要があります。 Kudos の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Kudos** の企業サイトに管理者としてサインインします。

1. 上部のメニューで **[設定] アイコン** をクリックします。

   ![設定](./media/kudos-tutorial/menu.png "設定")

1. [**ユーザー管理者**] をクリックします。

1. **[Users]\(ユーザー\)** タブをクリックして、 **[Add a User]\(ユーザーの追加\)** をクリックします。

   ![[ユーザー管理者]](./media/kudos-tutorial/users.png "[ユーザー管理者]")

1. [**ユーザーの追加**] セクションで、次の手順を実行します。

    ![[ユーザーの追加]](./media/kudos-tutorial/create-users.png "ユーザーを追加する")

    a. 関連テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの [**名**]、[**姓**]、[**電子メール**]、およびその他の詳細情報を入力します。

    b. **[Create User]** をクリックします。

> [!NOTE]
> 他の Kudos ユーザー アカウント作成ツールや、Kudos によって提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Kudos のサインオン URL にリダイレクトされます。 

* Kudos のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Kudos] タイルをクリックすると、Kudos のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Kudos を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
