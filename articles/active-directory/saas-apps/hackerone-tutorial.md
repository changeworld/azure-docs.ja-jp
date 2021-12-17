---
title: 'チュートリアル: Azure Active Directory と HackerOne の統合 | Microsoft Docs'
description: Azure Active Directory と HackerOne の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 0959fff0f3020b91ba61168e811f0a4a1cde5f8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309327"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>チュートリアル: Azure Active Directory と HackerOne の統合

このチュートリアルでは、HackerOne と Azure Active Directory (Azure AD) を統合する方法について説明します。 HackerOne と Azure AD を統合すると、次のことができます。

* HackerOne にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して HackerOne に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な HackerOne サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* HackerOne では、**SP** Initiated SSO がサポートされます。
* HackerOne では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-hackerone-from-the-gallery"></a>ギャラリーからの HackerOne の追加

Azure AD への HackerOne の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HackerOne を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**HackerOne**」と入力します。
1. 結果パネルで **[HackerOne]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-hackerone"></a>HackerOne の Azure AD SSO の構成とテスト

**B. Simon** というテスト ユーザーを使用して、HackerOne に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと HackerOne の関連ユーザーとの間にリンク関係を確立する必要があります。

HackerOne に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[HackerOne の SSO の構成](#configure-hackerone-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[HackerOne のテスト ユーザーの作成](#create-hackerone-test-user)** - HackerOne で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **HackerOne** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、`hackerone.com` という値を入力します。

    b. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://hackerone.com/users/saml/sign_in?email=<CONFIGURED_DOMAIN>`

    > [!Note]
    > サインオン URL は実際の値ではありません。 この値を実際のサインオン URL で更新してください。Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[HackerOne のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に HackerOne へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[HackerOne]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-hackerone-sso"></a>HackerOne の SSO の構成

1. HackerOne テナントに管理者としてサインオンします。

2. 上部のメニューで **[Settings]** をクリックします。

    ![メニューで [Settings]\(設定\) が選択されているスクリーンショット。](./media/hackerone-tutorial/menu.png)

3. **[Authentication]\(認証\)** に移動し、 **[Add SAML settings]\(SAML 設定の追加\)** をクリックします。

    ![[Add SAML settings]\(SAML 設定の追加\) ボタンが選択されている [Authentication Settings]\(認証設定\) ページを示すスクリーンショット。](./media/hackerone-tutorial/settings.png)

4. **[SAML Settings (SAML の設定)]** ダイアログで、次の手順を実行します。

    ![Configure single sign-on](./media/hackerone-tutorial/certificate.png)

    a. 登録されているドメインを **[電子メール ドメイン]** ボックスに入力します。

    b. **[Single Sign-On URL]\(シングル サインオン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. Azure portal からダウンロードした **証明書ファイル** をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X509 Certificate]\(X509 証明書\)** ボックスに貼り付けます。

    d. **[保存]** をクリックします。

5. [Authentication Settings] ダイアログで、次の手順を実行します。

    ![[Run test]\(テストの実行\) ボタンが選択されている [Authentication Settings]\(認証設定\) ダイアログを示すスクリーンショット。](./media/hackerone-tutorial/test.png)

    a. **[テストの実行]** をクリックします。

6. テストが正常に完了し、 **[Status]\(状態\)** フィールドに **[Last test status: success]\(最新のテスト状態: 成功\)** と表示されている場合は、 **[Request Verification]\(確認の要求\)** ボタンを選択して HackerOne に承認を求めます。

    ![HackerOne に承認を求める](./media/hackerone-tutorial/verification.png)

7. HackerOne によって設定が承認されたら、 **[Migrate Users]\(ユーザーを移行する\)** ボタンを選択して、すべてのユーザーに SSO 認証を要求することができます。

    ![SAML を有効にする](./media/hackerone-tutorial/users.png)

### <a name="create-hackerone-test-user"></a>HackerOne のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを HackerOne に作成します。 HackerOne では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 HackerOne にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる HackerOne のサインオン URL にリダイレクトされます。 

* HackerOne のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [HackerOne] タイルをクリックすると、HackerOne のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

HackerOne を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
