---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Neustar UltraDNS の統合 | Microsoft Docs'
description: Azure Active Directory と Neustar UltraDNS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2021
ms.author: jeedes
ms.openlocfilehash: 36c27a7d4cb93c91813a6c1b9a0f8c81564ce18d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342245"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-neustar-ultradns"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Neustar UltraDNS の統合

このチュートリアルでは、Neustar UltraDNS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Neustar UltraDNS と Azure AD を統合すると、次のことができます。

* Neustar UltraDNS にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Neustar UltraDNS に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Neustar UltraDNS でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Neustar UltraDNS では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* Neustar UltraDNS では、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-neustar-ultradns-from-the-gallery"></a>ギャラリーから Neustar UltraDNS を追加する

Azure AD への Neustar UltraDNS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Neustar UltraDNS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Neustar UltraDNS**」と入力します。
1. 結果パネルで **[Neustar UltraDNS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-neustar-ultradns"></a>Neustar UltraDNS の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Neustar UltraDNS に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Neustar UltraDNS の関連ユーザーとの間にリンク関係を確立する必要があります。

Neustar UltraDNS に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Neustar UltraDNS の SSO の構成](#configure-neustar-ultradns-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Neustar UltraDNS のテスト ユーザーの作成](#create-neustar-ultradns-test-user)** - Neustar UltraDNS で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Neustar UltraDNS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.sso.security.neustar` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Neustar UltraDNS クライアント サポート チーム](mailto:IDMTeam@neustar.biz)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[保存]** をクリックします。

1. Neustar UltraDNS アプリケーションでは、特定の形式の SAML アサーションが想定されているため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Neustar UltraDNS アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性|
    | ---------------- | --------- |
    | givenname | 名 |
    | mail | メール アドレス |
    | sn | 姓 |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Neustar UltraDNS のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Neustar UltraDNS へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Neustar UltraDNS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-neustar-ultradns-sso"></a>Neustar UltraDNS の SSO の構成

**Neustar UltraDNS** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Neustar UltraDNS のサポート チーム](mailto:IDMTeam@neustar.biz)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-neustar-ultradns-test-user"></a>Neustar UltraDNS のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Neustar UltraDNS に作成します。 Neustar UltraDNS では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Neustar UltraDNS にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

SP Initiated:

* Azure portal で [このアプリケーションをテストします] をクリックします。 これにより、ログイン フローを開始できる Neustar UltraDNS のサインオン URL にリダイレクトされます。

* Neustar UltraDNS のサインオン URL に直接移動し、そこからログイン フローを開始します。

IDP Initiated:

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Neustar UltraDNS に自動的にサインインされます

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Neustar UltraDNS] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Neustar UltraDNS に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Neustar UltraDNS を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
