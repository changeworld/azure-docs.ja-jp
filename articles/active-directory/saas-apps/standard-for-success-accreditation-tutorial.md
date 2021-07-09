---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Standard for Success Accreditation の統合 | Microsoft Docs'
description: Azure Active Directory と Standard for Success Accreditation の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2021
ms.author: jeedes
ms.openlocfilehash: a9ecec2456354c2d766d528d2c29ceb1833a7ad9
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110481775"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-standard-for-success-accreditation"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Standard for Success Accreditation の統合

このチュートリアルでは、Standard for Success Accreditation と Azure Active Directory (Azure AD) を統合する方法について説明します。 Standard for Success Accreditation と Azure AD を統合すると、次のことができます。

* Standard for Success Accreditation にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Standard for Success Accreditation に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Standard for Success Accreditation でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Standard for Success Accreditation では **SP および IDP** の Initiated SSO がサポートされています。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-standard-for-success-accreditation-from-the-gallery"></a>ギャラリーから Standard for Success Accreditation を追加する

Standard for Success Accreditation の Azure AD への統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Standard for Success Accreditation を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Standard for Success Accreditation**」と入力します。
1. 結果パネルから **[Standard for Success Accreditation]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-standard-for-success-accreditation"></a>Standard for Success Accreditation の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Standard for Success Accreditation に Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Standard for Success Accreditation の関連ユーザーとの間にリンク関係を確立する必要があります。

Standard for Success Accreditation に Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Standard for Success Accreditation SSO の構成](#configure-standard-for-success-accreditation-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Standard for Success Accreditation のテスト ユーザーの作成](#create-standard-for-success-accreditation-test-user)** - Standard for Success Accreditation で B. Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Standard for Success Accreditation** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://edu.sfsed.com/access/saml_consume?did=<INSTITUTIONID>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://edu.sfsed.com/access/saml_int?did=<INSTITUTIONID>` という形式で URL を入力します。

    b. **[リレー状態]** ボックスに、`https://edu.sfsed.com/access/saml_consume?did=<INSTITUTIONID>` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の応答 URL、サインオン URL、リレー状態で更新します。 これらの値を取得するには、[Standard for Success Accreditation クライアント サポート チーム](mailto:help_he@standardforsuccess.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

1. **[SAML 署名証明書]** セクションで **[Thumbprint Value]\(拇印の値\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

1. **[Standard for Success Accreditation のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Standard for Success Accreditation へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Standard for Success Accreditation]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-standard-for-success-accreditation-sso"></a>Standard for Success Accreditation SSO を構成する

1. 新しい Web ブラウザー ウィンドウを開き、スーパーユーザー アクセス権を持つ管理者として Standard for Success Accreditation サイトにサインインします。

1. メニューから **[管理ポータル]** をクリックします。

1. **[シングル サインオンの設定]** まで下にスクロールし、 **[Microsoft Azure シングル サインオン]** リンクをクリックして、次の手順を実行します。

    ![[Microsoft Azure シングル サインオン] ページ。](./media/standard-for-success-accreditation-tutorial/configuration.png)

    a. **[Enable Azure Single Sign On]\(Azure シングル サインオンを有効にする\)** チェック ボックスをオンにします。

    b. **[Azure テナント ID]** テキスト ボックスに、Azure portal からテナント ID 値を入力します。

    c. **[アプリケーション ID]** テキスト ボックスに アプリケーション ID を入力します。

    d. **[証明書の拇印]** テキスト ボックスに、Azure portal からコピーした **拇印の値** を貼り付けます。

    e. **[保存]** をクリックします。 

### <a name="create-standard-for-success-accreditation-test-user"></a>Standard for Success Accreditation テスト ユーザーを作成する

1.  スーパーユーザー特権を持つ管理者として Standard for Success Accreditation にサインインします。

1. メニューから **[管理ポータル] -> [新しい評価の作成]** をクリックし、次の手順を実行します。

    ![テスト ユーザーの作成。](./media/standard-for-success-accreditation-tutorial/new-user.png)

    a. **[名]** テキスト ボックスに「B」と入力します。

    b. **[姓]** テキスト ボックスに「Simon」と入力します。

    c. **[University Email]\(大学の電子メール\)** テキスト ボックスに、組織の電子メール アドレスを入力します。

    d. 一番下までスクロールし、 **[ユーザーの作成]** をクリックします。


## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Standard for Success Accreditation のサインオン URL にリダイレクトされます。  

* Standard for Success Accreditation のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Standard for Success Accreditation に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Standard for Success Accreditation] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Standard for Success Accreditation に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Standard for Success Accreditation を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。


