---
title: チュートリアル:Azure Active Directory と TAS の統合 | Microsoft Docs
description: Azure Active Directory と TAS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: a77c7dc271ec31f04233d9251ce1e8d220c9c7dd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333126"
---
# <a name="tutorial-azure-active-directory-integration-with-tas"></a>チュートリアル:Azure Active Directory と TAS の統合

このチュートリアルでは、TAS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と TAS を統合すると、次のことができます。

* TAS にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TAS に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* TAS でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TAS では、**SP と IDP** Initiated SSO がサポートされます。

## <a name="add-tas-from-the-gallery"></a>ギャラリーからの TAS の追加

Azure AD への TAS の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に TAS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに **[TAS]** と入力します。
1. 結果のパネルから **[TAS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-tas"></a>TAS の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、TAS で Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと TAS の関連ユーザーとの間にリンク関係を確立する必要があります。

TAS で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[TAS SSO の構成](#configure-tas-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[TAS のテスト ユーザーの作成](#create-tas-test-user)** - TAS で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **TAS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://taseu.combtas.com/<DOMAIN>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://taseu.combtas.com/<ENVIRONMENT_NAME>/AssertionService.aspx` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://taseu.combtas.com/<DOMAIN>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらを実際の識別子、応答 URL、およびサインオン URL に値を置き換えます。実際の値については、チュートリアルの後の方で説明します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[TAS のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に TAS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[TAS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-tas-sso"></a>TAS SSO の構成

1. 別の Web ブラウザー ウィンドウで、TAS に管理者としてログインします。

2. メニューの左側で **[Settings]\(設定\)** をクリックして、 **[Administrator]\(管理者\)** に移動し、 **[Manage Single sign on]\(シングル サインオンの管理\)** をクリックします。

    ![[Manage Single Sign-On]\(シングル サインオンの管理\) が選択されていることを示すスクリーンショット。](./media/tas-tutorial/settings.png)

3. **[Manage Single sign on]\(シングル サインオンの管理\)** ページで、次の手順を実行します。

    ![[Manage Single sign on]\(シングル サインオンの管理\) ページのスクリーンショット。ここで、説明されている値を入力できます。](./media/tas-tutorial/configure.png)

    a. **[Name]\(名前\)** ボックスに環境の名前を入力します。
    
    b. **[Authentication Type]\(認証の種類\)** として **[SAML2]** を選択します。

    c. **[Enter URL]\(URL の入力\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[Enter Certification]\(証明書の入力\)** ボックスに貼り付けます。

    e. **[Enter New IP]\(新しい IP の入力\)** ボックスに、IP アドレスを入力します。

    >[!NOTE]
    > IP アドレスを取得するには、[TAS サポート チーム](mailto:support@combtas.com)にお問い合わせください。

    f. **シングル サインオン** URL をコピーして、Azure portal の **[基本的な SAML 構成]** の **[識別子 (エンティティ ID)]** および **[サインオン URL]** ボックスに貼り付けます。 URL は、大文字と小文字が区別され、スラッシュ (/) で終わる必要があることに注意してください。

    g. セットアップ ページの **アサーション サービス** URL をコピーし、Azure portal の **[基本的な SAML 構成]** の **[応答 URL]** ボックスに貼り付けます。

    h. **[Insert SSO row]\(SSO 行の挿入\)** をクリックします。

### <a name="create-tas-test-user"></a>TAS テスト ユーザーの作成

このセクションでは、TAS で Britta Simon というユーザーを作成します。 [TAS サポート チーム](mailto:support@combtas.com)と連携し、TAS プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる TAS のサインオン URL にリダイレクトされます。  

* TAS のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した TAS に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [TAS] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した TAS に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

TAS を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
