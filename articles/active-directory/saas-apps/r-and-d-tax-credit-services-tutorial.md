---
title: 'チュートリアル:Azure AD SSO と R および D の税クレジット サービスの統合: 10 週間での実装'
description: Azure Active Directory と R および D の税クレジット サービスの間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2021
ms.author: jeedes
ms.openlocfilehash: f7f6e25b1c276fdda310f394a9b939ea7f860cfa
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000581"
---
# <a name="tutorial-azure-ad-sso-integration-with-r-and-d-tax-credit-services--10-wk-implementation"></a>チュートリアル:Azure AD SSO と R および D の税クレジット サービスの統合: 10 週間での実装

このチュートリアルでは、R および D の税クレジット サービスと Azure Active Directory (Azure AD) を統合する方法について説明します。 R および D の税クレジット サービスを Azure AD と統合すると、次のことができます。

* R および D の税クレジット サービスにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して R および D の税クレジット サービスに自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* R および D の税クレジット サービスでのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* R および D の税クレジット サービスでは **SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-r-and-d-tax-credit-services-from-the-gallery"></a>R および D の税クレジット サービスをギャラリーから追加する

Azure AD への R および D の税クレジット サービスの統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に R および D の税クレジット サービスを追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに **[R および D の税クレジット サービス]** と入力します。
1. 結果パネルから **[R および D の税クレジット サービス]** を選択してそのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-r-and-d-tax-credit-services"></a>R および D の税クレジット サービスに対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、R および D の税クレジット サービスに対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと R および D の税クレジット サービスの関連ユーザーの間で、リンク関係を確立する必要があります。

R および D の税クレジット サービスに対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[R および D の税クレジット サービスの SSO の構成](#configure-r-and-d-tax-credit-services-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[R および D の税クレジット サービスのテスト ユーザーの作成](#create-r-and-d-tax-credit-services-test-user)** - R および D の税クレジット サービスで B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **R および D の税クレジット サービス** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、`urn:pwcid:saml:sp:p` という値を入力します。

    b. **[応答 URL]** ボックスに、URL として「`https://login.pwc.com/openam/PWCIAuthConsumer/metaAlias/pwc/sp3`」と入力します。

    c. **[サインオン URL]** ボックスに、URL として「`https://researchcreditsolution.pwc.com/`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[R および D の税クレジット サービスのセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に R および D の税クレジット サービスへのアクセスを許可して、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[R および D の税クレジット サービス]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-r-and-d-tax-credit-services-sso"></a>R および D の税クレジット サービスの SSO の構成

**R および D の税クレジット サービス** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [R および D の税クレジット サービス サポート チーム](https://www.pwc.com/us/en/services/tax/specialized-tax/research-development-credit.html)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-r-and-d-tax-credit-services-test-user"></a>R および D の税クレジット サービスのテスト ユーザーの作成

このセクションでは、R および D の税クレジット サービスで Britta Simon というユーザーを作成します。 [R および D の税クレジット サービス サポート チーム](https://www.pwc.com/us/en/services/tax/specialized-tax/research-development-credit.html)と連携して、R および D の税クレジット サービス プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる R および D の税クレジット サービスのサインオン URL にリダイレクトされます。 

* R および D の税クレジット サービスのサインオン URL に移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [R および D の税クレジット サービス] タイルをクリックすると、R および D の税クレジット サービスのサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

R および D の税クレジット サービスを構成すると、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。