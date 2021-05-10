---
title: 'チュートリアル: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の統合| Microsoft Docs'
description: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222284"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>チュートリアル: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の統合

このチュートリアルでは、Veritas Enterprise Vault.cloud SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Veritas Enterprise Vault.cloud SSO を Azure AD と統合すると、次のことができます。

* Veritas Enterprise Vault.cloud SSO にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Veritas Enterprise Vault.cloud SSO に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Veritas Enterprise Vault.cloud SSO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Veritas Enterprise Vault.cloud SSO でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Veritas Enterprise Vault.cloud SSO では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>ギャラリーからの Veritas Enterprise Vault.cloud SSO の追加

Azure AD への Veritas Enterprise Vault.cloud SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Veritas Enterprise Vault.cloud SSO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Veritas Enterprise Vault.cloud SSO**」と入力します。
1. 結果のパネルから **[Veritas Enterprise Vault.cloud SSO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>Veritas Enterprise Vault.cloud SSO の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Veritas Enterprise Vault.cloud SSO に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Veritas Enterprise Vault.cloud SSO の関連ユーザーとの間にリンク関係を確立する必要があります。

Veritas Enterprise Vault.cloud SSO に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Veritas Enterprise Vault.cloud SSO の SSO の構成](#configure-veritas-enterprise-vaultcloud-sso-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Veritas Enterprise Vault.cloud SSO テスト ユーザーの作成](#create-veritas-enterprise-vaultcloud-sso-test-user)** - Veritas Enterprise Vault.cloud SSO で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Veritas Enterprise Vault.cloud SSO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>` という形式で URL を入力します。

    b. **[識別子]** ボックスに、データセンターに応じていずれかの URL を入力します。

    | データセンター| URL |
    |----------|----|
    | 北米| `https://auth.lax.archivecloud.net` |
    | ヨーロッパ | `https://auth.ams.archivecloud.net` |
    | アジア太平洋| `https://auth.syd.archivecloud.net`|

    c. **[応答 URL]** ボックスに、データセンターに応じていずれかの URL を入力します。

    | データセンター| URL |
    |----------|----|
    | 北米| `https://auth.lax.archivecloud.net` |
    | ヨーロッパ | `https://auth.ams.archivecloud.net` |
    | アジア太平洋| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Veritas Enterprise Vault.cloud SSO クライアント サポート チーム](https://www.veritas.com/support/.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Veritas Enterprise Vault.cloud SSO のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Veritas Enterprise Vault.cloud SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Veritas Enterprise Vault.cloud SSO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>Veritas Enterprise Vault.cloud SSO の SSO の構成

**Veritas Enterprise Vault.cloud SSO** 側にシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** および Azure portal からコピーした適切な URL を [Veritas Enterprise Vault.cloud SSO サポート チーム](https://www.veritas.com/support/.html)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Veritas Enterprise Vault.cloud SSO テスト ユーザーの作成

このセクションでは、Veritas Enterprise Vault.cloud SSO で Britta Simon というユーザーを作成します。 [Veritas Enterprise Vault.cloud SSO サポート チーム](https://www.veritas.com/support/.html)と連携して、Veritas Enterprise Vault.cloud SSO プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Veritas Enterprise Vault.cloud SSO のサインオン URL にリダイレクトされます。 

* Veritas Enterprise Vault.cloud SSO のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Veritas Enterprise Vault.cloud SSO] タイルをクリックすると、Veritas Enterprise Vault.cloud SSO のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Veritas Enterprise Vault.cloud SSO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。
