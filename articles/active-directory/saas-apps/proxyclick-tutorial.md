---
title: 'チュートリアル: Azure Active Directory と Proxyclick の統合 | Microsoft Docs'
description: このチュートリアルでは、Azure Active Directory と Proxyclick の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: ec7d8c7f8619fa4b79374eebdba141905a65b714
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329711"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>チュートリアル: Azure Active Directory と Proxyclick の統合

このチュートリアルでは、Proxyclick と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Proxyclick を統合すると、次のことが可能になります。

* Proxyclick にアクセスできる Azure AD ユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して Proxyclick に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Proxyclick でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Proxyclick では、SP-initiated SSO と IdP-initiated SSO がサポートされます。

* Proxyclick では、[自動化されたユーザー プロビジョニング](proxyclick-provisioning-tutorial.md)がサポートされます。

## <a name="add-proxyclick-from-the-gallery"></a>ギャラリーから Proxyclick を追加する

Azure AD への Proxyclick の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Proxyclick を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Proxyclick**」と入力します。
1. 結果のパネルから **[Proxyclick]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-proxyclick"></a>Proxyclick の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Proxyclick 用に Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Proxyclick の関連ユーザーの間にリンク関係を確立する必要があります。

Proxyclick 用に Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Proxyclick SSO の構成](#configure-proxyclick-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Proxyclick テスト ユーザーの作成](#create-proxyclick-test-user)** - Proxyclick で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Proxyclick** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、アプリケーションを IDP 開始モードで構成する場合は、次の手順を実行します。
    
    a. **[識別子]** ボックスに、`https://saml.proxyclick.com/init/<COMPANY_ID>` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://saml.proxyclick.com/consume/<COMPANY_ID>` の形式で URL を入力します。

5. アプリケーションを SP-initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択します。 **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
   
   `https://saml.proxyclick.com/init/<COMPANY_ID>`

    > [!NOTE]
    > これらの値はプレースホルダーです。 実際の識別子、応答 URL、サインオン URL を使用する必要があります。 これらの値を取得する手順については、このチュートリアルの後半で説明します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、要件に従って **[証明書 (Base64)]** の横にある **[ダウンロード]** リンクを選択し、証明書をコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

7. **[Set up Proxyclick]\(Proxyclick の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

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

このセクションでは、B.Simon に Proxyclick へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Proxyclick]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-proxyclick-sso"></a>Proxyclick SSO の構成

1. 新しい Web ブラウザー ウィンドウで、Proxyclick 企業サイトに管理者としてサインインします。

2. **[Account & Settings]\(アカウントと設定\)** を選択します。

    ![[Account & Settings]\(アカウントと設定\) を選択します。](./media/proxyclick-tutorial/account.png)

3. **[Integrations]\(統合\)** セクションまで下にスクロールし、 **[SAML]** を選択します。

    ![[SAML] を選択します。](./media/proxyclick-tutorial/settings.png)

4. **[SAML]** セクションで、次の手順に従います。

    ![[SAML] セクション](./media/proxyclick-tutorial/configuration.png)

    1. **[SAML Consumer URL]\(SAML コンシューマー URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスにある **[応答 URL]** ボックスに貼り付けます。

    1. **[SAML SSO Redirect URL]\(SAML SSO リダイレクト URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスにある **[サインオン URL]** ボックスと **[識別子]** ボックスに貼り付けます。

    1. **[SAML Request Method]\(SAML 要求メソッド\)** 一覧で、**[HTTP Redirect]\(HTTP リダイレクト\)** を選択します。

    1. **[Issuer]/(発行者/)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    1. **[SAML 2.0 Endpoint URL]\(SAML 2.0 エンドポイント URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    1. メモ帳で、Azure portal からダウンロードした証明書ファイルを開きます。 **[証明書]** ボックスにこのファイルの内容を貼り付けます。

    1. **[変更の保存]** を選択します。

### <a name="create-proxyclick-test-user"></a>Proxyclick テスト ユーザーの作成

Azure AD ユーザーが Proxyclick にサインインできるようにするには、それらを Proxyclick に追加する必要があります。 手動で追加する必要があります。

ユーザー アカウントを作成するには、以下の手順に従います。

1. Proxyclick 企業サイトに管理者としてサインインします。

1. ウィンドウの上部にある **[Colleagues]\(仕事仲間\)** を選択します。

    ![[Colleagues]\(仕事仲間\) を選択します。](./media/proxyclick-tutorial/user.png)

1. **[Add Colleague]\(仕事仲間の追加\)** を選択します。

    ![[Add Colleague]\(仕事仲間の追加\) を選択します。](./media/proxyclick-tutorial/add-user.png)

1. **[Add a colleague]\(仕事仲間の追加\)** セクションで、次の手順に従います。

    ![[Add a colleague]\(仕事仲間の追加\) セクション。](./media/proxyclick-tutorial/create-user.png)

    1. **[電子メール]** ボックスに、ユーザーの電子メール アドレスを入力します。 この場合は **brittasimon\@contoso.com** です。

    1. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します。 この場合は **Britta** です。

    1. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します。 この場合は **Simon** です。

    1. **[Add User]\(ユーザーの追加\)** を選択します。

> [!NOTE]
> Proxyclick では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./proxyclick-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Proxyclick のサインオン URL にリダイレクトされます。  

* Proxyclick のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Proxyclick に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Proxyclick] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Proxyclick に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Proxyclick を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
