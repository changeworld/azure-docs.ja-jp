---
title: 'チュートリアル: Azure Active Directory と Clear Review の統合 | Microsoft Docs'
description: Azure Active Directory と Clear Review の間のシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 190558bcf528ff45d16a215f4cdd4b6bd1a37963
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305163"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>チュートリアル: Azure Active Directory と Clear Review の統合

このチュートリアルでは、Clear Review と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Clear Review を統合すると、次のことが可能になります。

* Clear Review にアクセスするユーザーを Azure AD 上で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Clear Review に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Azure AD と Clear Review の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Clear Review でのシングル サインオンが有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Clear Review では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="add-clear-review-from-the-gallery"></a>ギャラリーからの Clear Review の追加

Azure AD への Clear Review の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Clear Review を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Clear Review**」と入力します。
1. 結果のパネルから **[Clear Review]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-clear-review"></a>Clear Review の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Clear Review に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Clear Review の関連ユーザーとの間にリンク関係を確立する必要があります。

Clear Review に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Clear Review SSO の構成](#configure-clear-review-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Clear Review のテスト ユーザーの作成](#create-clear-review-test-user)** - Clear Review で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Clear Review** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<CUSTOMER_NAME>.clearreview.com/sso/metadata/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<CUSTOMER_NAME>.clearreview.com/sso/acs/` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<CUSTOMER_NAME>.clearreview.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Clear Review クライアント サポート チーム](https://clearreview.com/contact/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. Clear Review アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Clear Review アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

7. **[ユーザー属性とクレーム]** セクションで、次の手順に従います。

    a. **[名前識別子の値]** の右側にある **[編集] アイコン** をクリックします。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes & Claims]\(ユーザー属性と要求\) を示しています。](./media/clearreview-tutorial/attribute-2.png)

    ![スクリーンショットは、説明されている値を入力できる [ユーザー要求の管理] ダイアログ ボックスを示しています。](./media/clearreview-tutorial/attribute-1.png)

    b. **[ソース属性]** の一覧から、その行の **user.mail** 属性値を選択します。

    c. **[保存]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

9. **[Clear Review のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

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

このセクションでは、B.Simon に Clear Review へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Clear Review]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-clear-review-sso"></a>Clear Review SSO の構成

1. **Clear Review** 側でシングル サインオンを構成するために、**Clear Review** のポータルを管理者の資格情報で開きます。

2. 左側のナビゲーションから **[Admin]\(管理者\)** を選択します。

    ![[Admin]\(管理者\) が選択されている Clear Review ポータルのスクリーンショット。](./media/clearreview-tutorial/admin.png)

3. ページの下部の **[Integrations]\(統合\)** セクションで、 **[Single Sign-On Settings]\(シングル サインオンの設定\)** の右側にある **[Change]\(変更\)** をクリックします。

    ![シングル サインオンの [Change]\(変更\) ボタンのスクリーンショット。](./media/clearreview-tutorial/integrations.png)

4. **[シングル サインオンの設定]** ページで、次の手順を実行します。

    ![このスクリーンショットは、[Single Sign-On Settings]\(シングル サインオンの設定\) ページを示しています。ここで、この手順の情報を入力します。](./media/clearreview-tutorial/settings.png)

    a. **[Issuer URL]\(発行者の URL\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    b. **[SAML Endpoint]\(SAML エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。  

    c. **[SLO Endpoint]\(SLO エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。  

    d. ダウンロードした証明書をメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。   

    e. **[保存]** をクリックします。

### <a name="create-clear-review-test-user"></a>Clear Review のテスト ユーザーの作成

このセクションでは、Clear Review で Britta Simon というユーザーを作成します。 Clear Review [サポート チーム](https://clearreview.com/contact/)と連携し、Clear Review プラットフォームにユーザーを追加してください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Clear Review のサインオン URL にリダイレクトされます。  

* Clear Review のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Clear Review に自動的にサインインします。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Clear Review] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Clear Review に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Clear Review を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
