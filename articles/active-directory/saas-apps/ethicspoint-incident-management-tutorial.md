---
title: 'チュートリアル: Azure Active Directory と EthicsPoint Incident Management (EPIM) の統合 | Microsoft Docs'
description: Azure Active Directory と EthicsPoint Incident Management (EPIM) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: 0b0f8a7e699cf7d505636ef02615adae99219862
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314256"
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>チュートリアル: Azure Active Directory と EthicsPoint Incident Management (EPIM) の統合

このチュートリアルでは、EthicsPoint Incident Management (EPIM) と Azure Active Directory (Azure AD) を統合する方法について説明します。 EthicsPoint Incident Management (EPIM) を Azure AD と統合すると、次のことが可能になります。

* EthicsPoint Incident Management (EPIM) にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントで自動的に EthicsPoint Incident Management (EPIM) にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* EthicsPoint Incident Management (EPIM) でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* EthicsPoint Incident Management (EPIM) では、**SP** Initiated SSO がサポートされます。

## <a name="add-ethicspoint-incident-management-epim-from-the-gallery"></a>ギャラリーからの EthicsPoint Incident Management (EPIM) の追加

Azure AD への EthicsPoint Incident Management (EPIM) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に EthicsPoint Incident Management (EPIM) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**EthicsPoint Incident Management (EPIM)** 」と入力します。
1. 結果パネルから **[EthicsPoint Incident Management (EPIM)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-ethicspoint-incident-management-epim"></a>EthicsPoint Incident Management (EPIM) の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、EthicsPoint Incident Management (EPIM) に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと EthicsPoint Incident Management (EPIM) の関連ユーザーとの間にリンク関係を確立する必要があります。

EthicsPoint Incident Management (EPIM) に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[EthicsPoint Incident Management (EPIM) SSO の構成](#configure-ethicspoint-incident-management-epim-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[EthicsPoint Incident Management (EPIM) のテスト ユーザーの作成](#create-ethicspoint-incident-management-epim-test-user)** - EthicsPoint Incident Management (EPIM) で B.Simon に対応するユーザーを作成し、Azure AD のユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **EthicsPoint Incident Management (EPIM)** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<COMPANY_NAME>.navexglobal.com/adfs/services/trust` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SERVER_NAME>.navexglobal.com/adfs/ls/` のパターンを使用して URL を入力します

     c. **[サインオン URL]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | [サインオン URL]|
    |---|
    |`https://<COMPANY_NAME>.navexglobal.com`|
    |`https://<COMPANY_NAME>.ethicspointvp.com`|
    |
  
    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 値を取得するには、[EthicsPoint Incident Management (EPIM) クライアント サポート チーム](https://www.navexglobal.com/company/contact-us)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[EthicsPoint Incident Management (EPIM) のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に EthicsPoint Incident Management (EPIM) へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[EthicsPoint Incident Management (EPIM)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-ethicspoint-incident-management-epim-sso"></a>EthicsPoint Incident Management (EPIM) SSO の構成

**EthicsPoint Incident Management (EPIM)** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [EthicsPoint Incident Management (EPIM) サポート チーム](https://www.navexglobal.com/company/contact-us)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-ethicspoint-incident-management-epim-test-user"></a>EthicsPoint Incident Management (EPIM) のテスト ユーザーの作成

このセクションでは、EthicsPoint Incident Management (EPIM) で Britta Simon というユーザーを作成します。 [EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us) サポート チームと連携して、EthicsPoint Incident Management (EPIM) プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる EthicsPoint Incident Management (EPIM) のサインオン URL にリダイレクトされます。 

* EthicsPoint Incident Management (EPIM) のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイアプリで [EthicsPoint Incident Management (EPIM)] タイルをクリックすると、EthicsPoint Incident Management (EPIM) のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

EthicsPoint Incident Management (EPIM) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
