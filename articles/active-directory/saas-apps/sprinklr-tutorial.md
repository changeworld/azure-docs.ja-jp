---
title: チュートリアル:Azure Active Directory と Sprinklr の統合 | Microsoft Docs
description: Azure Active Directory と Sprinklr の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: f7dc7215f812938584438d3f212ce31ddcea23df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329067"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>チュートリアル:Azure Active Directory と Sprinklr の統合

このチュートリアルでは、Sprinklr と Azure Active Directory (Azure AD) を統合する方法について説明します。 Sprinklr と Azure AD を統合すると、次のことが可能になります。

* Sprinklr にアクセスするユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Sprinklr に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Sprinklr でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Sprinklr では、**SP** Initiated SSO がサポートされます。

## <a name="add-sprinklr-from-the-gallery"></a>ギャラリーからの Sprinklr の追加

Azure AD への Sprinklr の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sprinklr を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Sprinklr**」と入力します。
1. 結果のパネルから **[Sprinklr]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-sprinklr"></a>Sprinklr の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Sprinklr に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Sprinklr の関連ユーザーとの間にリンク関係を確立する必要があります。

Sprinklr に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Sprinklr の SSO の構成](#configure-sprinklr-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Sprinklr のテスト ユーザーの作成](#create-sprinklr-test-user)** - Sprinklr で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Sprinklr** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.sprinklr.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.sprinklr.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Sprinklr クライアント サポート チーム](https://www.sprinklr.com/contact-us/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Sprinklr のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Sprinklr へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Sprinklr]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sprinklr-sso"></a>Sprinklr SSO の構成

1. 別の Web ブラウザー ウィンドウで、Sprinklr 企業サイトに管理者としてログインします。

1. **[管理] \> [設定]** の順に選択します。

    ![管理](./media/sprinklr-tutorial/settings.png "管理")

1. 左側のパネルで、 **[Manage Partner] \> [Single Sign on]** の順に選択します。

    ![[Manage Partner]\(パートナーの管理\)](./media/sprinklr-tutorial/users.png "パートナーの管理")

1. **[+Add Single Sign Ons]** をクリックします。

    ![[Add Single Sign Ons]\(シングル サインオンの追加\) ボタンのスクリーンショット。](./media/sprinklr-tutorial/add-user.png "シングル サインオン")

1. **[Single Sign on]** ページで、次の手順に従います。

    ![[Single Sign on]\(シングル サインオン\) ページのスクリーンショット。ここで、説明されている値を入力できます。](./media/sprinklr-tutorial/configuration.png "シングル サインオン")

    a. **[Name]\(名前\)** ボックスに、構成の名前を入力します (例:**WAADSSOTest**)。

    b. **[Enabled]** を選択します。

    c. **[Use new SSO Certificate]** を選択します。

    d. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    e. **[Entity Id]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    f. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    g. **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    h. **[SAML User ID Type]\(SAML ユーザー ID の種類\)** として、 **[Assertion contains User’s sprinklr.com username]\(アサーションにユーザーの sprinklr.com ユーザー名を含む\)** を選択します。

    i. **[SAML User ID Location]** として **[User ID is in the Name Identifier element of the Subject statement]** を選択します。

    j. **[保存]** をクリックします。

    ![SAML](./media/sprinklr-tutorial/save-configuration.png "SAML")

### <a name="create-sprinklr-test-user"></a>Sprinklr テスト ユーザーの作成

1. Sprinklr 企業サイトに管理者としてログインします。

1. **[管理] \> [設定]** の順に選択します。

    ![管理](./media/sprinklr-tutorial/settings.png "管理")

1. 左側のパネルで、 **[クライアントの管理] \> [ユーザー]** の順に選択します。

    ![[設定] の [ユーザー] にある [ユーザーの追加] ボタンのスクリーンショット。](./media/sprinklr-tutorial/client.png "設定")

1. **[ユーザーの追加]** をクリックします。

    ![[Edit user]\(ユーザーの編集\) ダイアログ ボックスを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/sprinklr-tutorial/search-users.png "設定")

1. **[Edit user]** ダイアログで、次の手順に従います。

    ![[Edit user]\(ユーザーの編集\)](./media/sprinklr-tutorial/update-users.png "[Edit user]")

    a. **[Email]** 、 **[First Name]** 、および **[Last Name]** テキスト ボックスに、プロビジョニングする Azure AD のユーザー アカウントの情報を入力します。

    b. **[Password Disabled]** を選択します。

    c. **[Language]\(言語\)** を選択します。

    d. **[User Type]\(ユーザー タイプ\)** を選択します。

    e. **[Update]** をクリックします。

    > [!IMPORTANT]
    > **[[Password Disabled]]** を選択する必要があります。 

1. **[Role]** に移動して、次の手順に従います。

    ![パートナーのロール](./media/sprinklr-tutorial/role.png "パートナーのロール")

    a. **[Global]** ボックスの一覧から、 **[ALL_Permissions]** を選択します。  

    b. **[Update]** をクリックします。

> [!NOTE]
> Sprinklr から提供されている他の Sprinklr ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、Sprinklr のサインオン URL にリダイレクトされ、そこでログイン フローを開始できます。 

* Sprinklr のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Sprinklr] タイルをクリックすると、Sprinklr のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Sprinklr を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
