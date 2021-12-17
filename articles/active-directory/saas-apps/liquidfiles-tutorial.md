---
title: 'チュートリアル: Azure AD SSO と LiquidFiles の統合'
description: Azure Active Directory と LiquidFiles の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jeedes
ms.openlocfilehash: 11fdd1ede686dc5261a277d42c74090531174ee6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307596"
---
# <a name="tutorial-azure-ad-sso-integration-with-liquidfiles"></a>チュートリアル: Azure AD SSO と LiquidFiles の統合

このチュートリアルでは、LiquidFiles と Azure Active Directory (Azure AD) を統合する方法について説明します。 LiquidFiles と Azure AD を統合すると、次のことができます。

* LiquidFiles にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して LiquidFiles に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

LiquidFiles と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LiquidFiles でのシングル サインオンが有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LiquidFiles では、**SP** Initiated SSO がサポートされます。

## <a name="add-liquidfiles-from-the-gallery"></a>ギャラリーからの LiquidFiles の追加

Azure AD への LiquidFiles の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LiquidFiles を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LiquidFiles**」と入力します。
1. 結果のパネルから **[LiquidFiles]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-liquidfiles"></a>LiquidFiles の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、LiquidFiles に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと LiquidFiles の関連ユーザーとの間にリンク関係を確立する必要があります。

LiquidFiles に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LiquidFiles SSO の構成](#configure-liquidfiles-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[LiquidFiles のテスト ユーザーの作成](#create-liquidfiles-test-user)** - LiquidFiles で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **LiquidFiles** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<YOUR_SERVER_URL>`

    b. **[応答 URL]** ボックスに、`https://<YOUR_SERVER_URL>/saml/consume` のパターンを使用して URL を入力します。

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<YOUR_SERVER_URL>/saml/init`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[LiquidFiles クライアント サポート チーム](https://www.liquidfiles.com/support.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

6. **[SAML 署名証明書]** セクションで **[THUMBPRINT]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

7. **[Set up LiquidFiles]\(LiquidFiles の設定\)** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B. Simon に LiquidFiles へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LiquidFiles]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-liquidfiles-sso"></a>LiquidFiles SSO の構成

1. LiquidFiles 企業サイトに管理者としてサインオンします。

1. メニューから **[Admin]\(管理\) > [Configuration]\(構成\)** をクリックし、**[Single Sign-On]\(シングル サインオン\)** をクリックします。

1. **[Single Sign-On Configuration]\(シングル サインオンの構成\)** ページで、次の手順を実行します。

    ![Configure single sign-on](./media/liquidfiles-tutorial/configuration.png)

    a. **[Single Sign On Method]\(シングル サインオンの方法\)** として、**[SAML 2]** を選びます。

    b. **[IDP Login URL]\(IDP ログイン URL\)** テキスト ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. **[IDP Logout URL]\(IDP ログアウト URL\)** テキスト ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[IDP Cert Fingerprint]\(IDP 証明書のフィンガープリント)** テキスト ボックスに、Azure portal からコピーした **THUMBPRINT (拇印)** 値を貼り付けます。

    e. [Name identifier Format]\(名前識別子の形式\) テキスト ボックスに、次の値を入力します。`urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`

    f. [Authn Context]\(認証コンテキスト\) テキスト ボックスに、次の値を入力します。`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`

    g. **[保存]** をクリックします。

### <a name="create-liquidfiles-test-user"></a>LiquidFiles のテスト ユーザーの作成

このセクションの目的は、LiquidFiles で Britta Simon というユーザーを作成することです。 LiquidFiles アプリケーションにログインする前に、LiquidFiles サーバー管理者と協力して自分自身をユーザーとして追加します。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる LiquidFiles のサインオン URL にリダイレクトされます。 

* LiquidFiles のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [LiquidFiles] タイルをクリックすると、LiquidFiles のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

LiquidFiles を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
