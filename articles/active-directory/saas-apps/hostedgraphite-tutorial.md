---
title: 'チュートリアル: Azure Active Directory と Hosted Graphite の統合 | Microsoft Docs'
description: Azure Active Directory と Hosted Graphite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/11/2021
ms.author: jeedes
ms.openlocfilehash: 185838b45f656e1b28b25749b215ab992d039131
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291371"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>チュートリアル: Azure Active Directory と Hosted Graphite の統合

このチュートリアルでは、Hosted Graphite と Azure Active Directory (Azure AD) を統合する方法について説明します。 Hosted Graphite と Azure AD を統合すると、次のことができるようになります。

* Hosted Graphite にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Hosted Graphite に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Hosted Graphite シングル サインオン (SSO) 対応のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Hosted Graphite では、**SP および IDP** Initiated SSO がサポートされています。
* Hosted Graphite では、**Just-In-Time** ユーザー プロビジョニングがサポートされています。

## <a name="add-hosted-graphite-from-the-gallery"></a>ギャラリーからの Hosted Graphite の追加

Azure AD への Hosted Graphite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Hosted Graphite を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Hosted Graphite**」と入力します。
1. 結果パネルから **[Hosted Graphite]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-hosted-graphite"></a>Hosted Graphite の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Hosted Graphite を使用した Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Hosted Graphite の関連ユーザーの間にリンク関係を確立する必要があります。

Hosted Graphite を使用した Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Hosted Graphite SSO の構成](#configure-hosted-graphite-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Hosted Graphite のテスト ユーザーの作成](#create-hosted-graphite-test-user)** - Hosted Graphite で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Hosted Graphite** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://www.hostedgraphite.com/metadata/<USER_ID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://www.hostedgraphite.com/complete/saml/<USER_ID>` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://www.hostedgraphite.com/login/saml/<USER_ID>/` という形式で URL を入力します。

    > [!NOTE]
    > これは実際の値ではないので注意してください。 実際の識別子、応答 URL、サインオン URL にこれらの値を置き換える必要があります。 これらの値を取得するには、アプリケーション側で [Access]\(アクセス\) > [SAML setup]\(SAML のセットアップ\) と移動するか、[Hosted Graphite サポート チーム](mailto:help@hostedgraphite.com)に問い合わせてください。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Hosted Graphite のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Hosted Graphite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Hosted Graphite]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-hosted-graphite-sso"></a>Hosted Graphite SSO の構成

1. Hosted Graphite テナントに管理者としてサインオンします。

2. サイド バーの **SAML のセットアップ ページ** に移動します ( **[Access (アクセス)]、[SAML Setup (SAML のセットアップ)]** の順に移動)。

    ![[SAML Setup]\(SAML のセットアップ\) が選択された [Access]\(アクセス\) メニューのスクリーンショット。](./media/hostedgraphite-tutorial/setup.png)

3. これらの URL が、Azure portal の **[基本的な SAML 構成]** セクションで行った構成と一致することを確認します。

    ![[基本的な SAML 構成] を示すスクリーンショット。](./media/hostedgraphite-tutorial/configuration.png)

4. **[Entity or Issuer ID]\(エンティティまたは発行者 ID\)** および **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** と **ログイン URL** の値を貼り付けます。

    ![ID プロバイダーに関する入力画面のスクリーンショット。](./media/hostedgraphite-tutorial/integration.png)

5. **[Default User Role]\(既定のユーザー ロール\)** として **[Read-only]\(読み取り専用\)** を選択します。

    ![[Default User Role]\(既定のユーザー ロール\) として [Read-only]\(読み取り専用\) が表示された画面のスクリーンショット。](./media/hostedgraphite-tutorial/role.png)

6. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X.509 証明書]** ボックスに貼り付けます。

    ![[X.509 証明書] を示すスクリーンショット。](./media/hostedgraphite-tutorial/certificate.png)

7. **[保存]** ボタンをクリックします。

### <a name="create-hosted-graphite-test-user"></a>Hosted Graphite のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Hosted Graphite に作成します。 Hosted Graphite では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Hosted Graphite にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Hosted Graphite のサポート チーム](mailto:help@hostedgraphite.com)に問い合わせる必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Hosted Graphite のサインオン URL にリダイレクトされます。  

* Hosted Graphite のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Hosted Graphite に自動的にサインインします。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Hosted Graphite] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Hosted Graphite に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Hosted Graphite を構成した後、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
