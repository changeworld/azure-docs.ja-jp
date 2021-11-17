---
title: 'チュートリアル: Azure AD SSO と Perforce Helix Core - Helix Authentication Service の統合'
description: Azure Active Directory と Perforce Helix Core - Helix Authentication Service の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 6917af20d95e4c9fcaa3b1f1e3528bd64e4d0736
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287554"
---
# <a name="tutorial-azure-ad-sso-integration-with-perforce-helix-core---helix-authentication-service"></a>チュートリアル: Azure AD SSO と Perforce Helix Core - Helix Authentication Service の統合

このチュートリアルでは、Perforce Helix Core - Helix Authentication Service と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Perforce Helix Core - Helix Authentication Service を統合すると、次のことができます。

* Perforce Helix Core - Helix Authentication Service にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Perforce Helix Core- Helix Authentication Service に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Perforce Helix Core - Helix Authentication Service でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Perforce Helix Core - Helix Authentication Service では、**SP** Initiated SSO がサポートされます。

## <a name="add-perforce-helix-core---helix-authentication-service-from-the-gallery"></a>ギャラリーからの Perforce Helix Core - Helix Authentication Service の追加

Azure AD への Perforce Helix Core - Helix Authentication Service の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Perforce Helix Core - Helix Authentication Service を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Perforce Helix Core - Helix Authentication Service**」と入力します。
1. 結果のパネルから **[Perforce Helix Core - Helix Authentication Service]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-perforce-helix-core---helix-authentication-service"></a>Perforce Helix Core - Helix Authentication Service の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Perforce Helix Core - Helix Authentication Service に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Perforce Helix Core - Helix Authentication Service の関連ユーザーとの間にリンク関係を確立する必要があります。

Perforce Helix Core - Helix Authentication Service で Azure AD の SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Perforce Helix Core - Helix Authentication Service SSO の構成](#configure-perforce-helix-core---helix-authentication-service-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Perforce Helix Core - Helix Authentication Service テスト ユーザーの作成](#create-perforce-helix-core---helix-authentication-service-test-user)** - Perforce Helix Core- Helix Authentication Service で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Perforce Helix Core - Helix Authentication Service** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/saml`

    b. **[応答 URL]** ボックスに、`https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/saml/sso` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 これらの値を取得するには、[Perforce Helix Core - Helix Authentication Service クライアント サポート チーム](mailto:support@perforce.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に Perforce Helix Core - Helix Authentication Service へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Perforce Helix Core - Helix Authentication Service]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-perforce-helix-core---helix-authentication-service-sso"></a>Perforce Helix Core - Helix Authentication Service SSO の構成

**Perforce Helix Core - Helix Authentication Service** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [Perforce Helix Core - Helix Authentication Service サポート チーム](mailto:support@perforce.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-perforce-helix-core---helix-authentication-service-test-user"></a>Perforce Helix Core - Helix Authentication Service テスト ユーザーの作成

このセクションでは、Perforce Helix Core - Helix Authentication Service で Britta Simon というユーザーを作成します。 [Perforce Helix Core - Helix Authentication Service サポート チーム](mailto:support@perforce.com)と連携して、Perforce Helix Core - Helix Authentication Service プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Perforce Helix Core - Helix Authentication Service のサインオン URL にリダイレクトされます。 

* Perforce Helix Core - Helix Authentication Service のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリ で [Perforce Helix Core - Helix Authentication Service] タイルをクリックすると、Perforce Helix Core - Helix Authentication Service のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Perforce Helix Core - Helix Authentication Service を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
