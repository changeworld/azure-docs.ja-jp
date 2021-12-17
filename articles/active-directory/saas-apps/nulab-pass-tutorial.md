---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Nulab Pass (Backlog,Cacoo,Typetalk) の統合 | Microsoft Docs'
description: Azure Active Directory と Nulab Pass (Backlog,Cacoo,Typetalk) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2021
ms.author: jeedes
ms.openlocfilehash: fe7a0960d43e2666b356ff4da61b421660413ae5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294520"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nulab-pass-backlogcacootypetalk"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Nulab Pass (Backlog,Cacoo,Typetalk) の統合

このチュートリアルでは、Nulab Pass (Backlog,Cacoo,Typetalk) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Nulab Pass (Backlog,Cacoo,Typetalk) を統合すると、次のことが可能になります。

* Nulab Pass (Backlog,Cacoo,Typetalk) にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Nulab Pass (Backlog,Cacoo,Typetalk) に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Nulab Pass (Backlog,Cacoo,Typetalk) でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Nulab Pass (Backlog,Cacoo,Typetalk) では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="add-nulab-pass-backlogcacootypetalk-from-the-gallery"></a>ギャラリーからの Nulab Pass (Backlog,Cacoo,Typetalk) の追加

Azure AD への Nulab Pass (Backlog,Cacoo,Typetalk) の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Nulab Pass (Backlog,Cacoo,Typetalk) を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Nulab Pass (Backlog,Cacoo,Typetalk)** 」と入力します。
1. 結果パネルから **[Nulab Pass (Backlog,Cacoo,Typetalk)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-nulab-pass-backlogcacootypetalk"></a>Nulab Pass (Backlog,Cacoo,Typetalk) の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Nulab Pass (Backlog,Cacoo,Typetalk) で Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Nulab Pass (Backlog,Cacoo,Typetalk) の関連ユーザーとの間にリンク関係を確立する必要があります。

Nulab Pass (Backlog,Cacoo,Typetalk) に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Nulab Pass SSO の構成](#configure-nulab-pass-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Nulab Pass のテスト ユーザーの作成](#create-nulab-pass-test-user)** - Nulab Pass (Backlog,Cacoo,Typetalk) で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Nulab Pass (Backlog,Cacoo,Typetalk)** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://apps.nulab.com/signin/spaces/<Space Key>/saml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://apps.nulab.com/signin/spaces/<Space Key>/saml/callback` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://apps.nulab.com/signin/spaces/<INSTANCE_NAME>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Nulab Pass (Backlog,Cacoo,Typetalk) クライアント サポート チーム](mailto:support@apps.nulab.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Nulab Pass (Backlog,Cacoo,Typetalk) アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[一意のユーザー ID]** の既定値は **user.userprincipalname** ですが、Nulab Pass (Backlog,Cacoo,Typetalk) ではこれをユーザーのメール アドレスにマップする必要があります。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。

    ![image](common/default-attributes.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Nulab Pass (Backlog,Cacoo,Typetalk) のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B. Simon に Nulab Pass (Backlog,Cacoo,Typetalk) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Nulab Pass (Backlog,Cacoo,Typetalk)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-nulab-pass-sso"></a>Nulab Pass SSO の構成

**Nulab Pass (Backlog,Cacoo,Typetalk)** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を [Nulab Pass (Backlog,Cacoo,Typetalk) サポート チーム](mailto:support@apps.nulab.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-nulab-pass-test-user"></a>Nulab Pass のテスト ユーザーの作成

このセクションでは、Nulab Pass (Backlog,Cacoo,Typetalk) で Britta Simon というユーザーを作成します。 [Nulab Pass (Backlog,Cacoo,Typetalk) サポートチーム](mailto:support@apps.nulab.com)と連携して、Nulab Pass (Backlog,Cacoo,Typetalk) プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Nulab Pass (Backlog,Cacoo,Typetalk) のサインオン URL にリダイレクトされます。  

* Nulab Pass (Backlog,Cacoo,Typetalk) のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Nulab Pass (Backlog,Cacoo,Typetalk) に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリの [Nulab Pass (Backlog,Cacoo,Typetalk)] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Nulab Pass (Backlog,Cacoo,Typetalk) に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Nulab Pass (Backlog,Cacoo,Typetalk) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
