---
title: 'チュートリアル: Azure Active Directory と Menlo Security の統合 | Microsoft Docs'
description: Azure Active Directory と Menlo Security の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: d199cfc7ef3f69badd47c149966b44326e7baa8a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342340"
---
# <a name="tutorial-azure-active-directory-integration-with-menlo-security"></a>チュートリアル: Azure Active Directory と Menlo Security の統合

このチュートリアルでは、Menlo Security と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Menlo Security を統合すると、次のことができます。

* Menlo Security にアクセスするユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Menlo Security に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Menlo Security でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Menlo Security では、**SP** Initiated SSO がサポートされます。

## <a name="add-menlo-security-from-the-gallery"></a>ギャラリーからの Menlo Security の追加

Azure AD への Menlo Security の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Menlo Security を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Menlo Security**」と入力します。
1. 結果のパネルから **[Menlo Security]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-menlo-security"></a>Menlo Security の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Menlo Security に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Menlo Security の関連ユーザーとの間にリンク関係を確立する必要があります。

Menlo Security に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Menlo Security の SSO の構成](#configure-menlo-security-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Menlo Security のテスト ユーザーの作成](#create-menlo-security-test-user)** - Menlo Security で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Menlo Security** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.menlosecurity.com/account/login`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.menlosecurity.com/safeview-auth-server/saml/metadata`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Menlo Security クライアント サポート チーム](https://www.menlosecurity.com/menlo-contact)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Menlo Security のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Menlo Security へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Menlo Security]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-menlo-security-sso"></a>Menlo Security SSO の構成

1. **Menlo Security 側** でシングル サインオンを構成するために、**Menlo Security** の Web サイトに管理者としてログインします。

2. **[Settings (設定)]** の **[Authentication (認証)]** に移動し、次の操作を行います。
    
    ![Configure single sign-on](./media/menlosecurity-tutorial/authentication.png)

    a. **[Enable user authentication using SAML (SAML を使用してユーザー認証を有効にする)]** チェックボックスをオンにします。

    b. **[Allow External Access (外部アクセスを許可する)]** で **[Yes (はい)]** を選択します。

    c. **[SAML Provider (SAML プロバイダー)]** で、**[Azure Active Directory]** を選択します。

    d. **[SAML 2.0 Endpoint]\(SAML 2.0 エンドポイント\)** : Azure portal からコピーした **ログイン URL** を貼り付けます。

    e. **[Service Identifier (Issuer)]\(サービス識別子 (発行者)\)** : Azure portal からコピーした **Azure AD 識別子** を貼り付けます。

    f. **[X.509 Certificate (X.509 証明書)]**: Azure Portal からダウンロードした **証明書 (Bas64)** をメモ帳で開いてこのボックスにコピーします。

    g. **[保存]** をクリックして設定を保存します。

### <a name="create-menlo-security-test-user"></a>Menlo Security のテスト ユーザーの作成

このセクションでは、Menlo Security で Britta Simon というユーザーを作成します。 [Menlo Security クライアント サポート チーム](https://www.menlosecurity.com/menlo-contact)と連携して、Menlo Security プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Menlo Security のサインオン URL にリダイレクトされます。 

* Menlo Security のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Menlo Security] タイルをクリックすると、Menlo Security サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Menlo Security を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
