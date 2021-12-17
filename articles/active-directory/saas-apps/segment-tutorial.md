---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) とセグメントの統合 | Microsoft Docs
description: Azure Active Directory とセグメントの間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: b6106b6c10a503f7bfb80b2f50656bddd387d942
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320832"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) とセグメントの統合

このチュートリアルでは、セグメントと Azure Active Directory (Azure AD) を統合する方法について説明します。 セグメントを Azure AD と統合すると、次のことができます。

* セグメントにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用してセグメントに自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* セグメントでのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* セグメントでは、**SP および IDP**  Initiated SSO がサポートされています。
* セグメントでは、**Just-In-Time** ユーザー プロビジョニングがサポートされています。
* Segment では、[自動化されたユーザー プロビジョニング](segment-provisioning-tutorial.md)がサポートされます。

## <a name="add-segment-from-the-gallery"></a>ギャラリーからのセグメントの追加

Azure AD へのセグメントの統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧にセグメントを追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**セグメント**」と入力します。
1. [結果] パネルから **[セグメント]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-segment"></a>セグメントの Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、セグメントに対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーとセグメントの関連ユーザーとの間にリンク関係を確立する必要があります。

セグメントに対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[セグメント SSO の構成](#configure-segment-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[セグメント テスト ユーザーの作成](#create-segment-test-user)** - セグメントで B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **セグメント** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>` の形式で値を入力します。

    b. **[応答 URL]** ボックスに、`https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://app.segment.com`」と入力します。

    > [!NOTE]
    > これらの値はプレースホルダーです。 実際の識別子と応答 URL を使用する必要があります。 これらの値を取得する手順については、このチュートリアルの後半で説明します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[セグメントのセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon にセグメントへのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[セグメント]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-segment-sso"></a>セグメント SSO の構成

1. 新しい Web ブラウザー ウィンドウで、セグメント企業サイトに管理者としてサインインします。

1. **[設定] アイコン** をクリックし、 **[認証]** まで下にスクロールして、 **[接続]** をクリックします。

    ![[Setting]\(設定\) アイコンが選択され、[Authentication]\(認証\) メニューの [Connections]\(接続\) が選択されているスクリーンショット。](./media/segment-tutorial/connections.PNG)

1. **[新しい接続の追加]** をクリックします。

    ![[Connections]\(接続\) セクションを示すスクリーンショット。[Add new Connection]\(新しい接続の追加\) ボタンが選択されています。](./media/segment-tutorial/new-connections.PNG)

1. 構成する接続として **[SAML 2.0]** を選択し、 **[接続の選択]** ボタンをクリックします。

    ![[Choose a Connection]\(接続の選択\) セクションを示すスクリーンショット。[S A M L 2.0] および [Select Connection]\(接続を選択\) ボタンが選択されています。](./media/segment-tutorial/select-connections.PNG)

1. 次のページで、以下の手順を実行します。

    ![[Configure Identity Provider]\(I D プロバイダーの構成\) ページを示すスクリーンショット。[Single Sign-On U R L]\(シングル サインオン U R L\) および [Audience U R L]\(対象 U R L\) テキスト ボックスが強調表示され、[Next]\(次へ\) ボタンが選択されています。](./media/segment-tutorial/configure.PNG)

    a. **[シングル サインオン URL]** の値をコピーし、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスにある **[応答 URL]** ボックスに貼り付けます。

    b. ****[Audience URL]\(対象 URL\)**** の値をコピーし、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスにある **[識別子]** ボックスに貼り付けます。

    c. **[次へ]** をクリックします。

    ![セグメント構成](./media/segment-tutorial/certificate.PNG)

1. **[SAML 2.0 Endpoint URL]\(SAML 2.0 エンドポイント URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

1. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[Public Certificate]\(公開証明書\)** ボックスに貼り付けます。

1. **[接続の構成]** をクリックします。

### <a name="create-segment-test-user"></a>セグメント テスト ユーザーの作成

このセクションでは、B.Simon というユーザーをセグメントに作成します。 セグメントでは、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 セグメントにユーザーがまだ存在していない場合は、認証後に新規に作成されます。

Segment では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./segment-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できるセグメントのサインオン URL にリダイレクトされます。  

* セグメントのサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定したセグメントに自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [セグメント] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定したセグメントに自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

セグメントを構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
