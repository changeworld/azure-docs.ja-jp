---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Exium の統合 | Microsoft Docs'
description: Azure Active Directory と Exium の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 01b3bbc0a4f0c7d53ed0a9ba2449cf5985b2ab86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608379"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Exium の統合

このチュートリアルでは、Exium と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Exium を統合すると、次のことができます。

* Exium にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Exium に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Exium のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Exium では、**SP** Initiated SSO がサポートされます。

## <a name="adding-exium-from-the-gallery"></a>ギャラリーからの Exium の追加

Azure AD への Exium の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Exium を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Exium**」と入力します。
1. 結果のパネルから **[Exium]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Exium の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Exium に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Exium の関連ユーザーとの間にリンク関係を確立する必要があります。

Exium に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Exium の SSO の構成](#configure-exium-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Exium のテスト ユーザーの作成](#create-exium-test-user)** - Exium で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Exium** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  **[応答 URL]** ボックスに、`https://subapi.exium.net/saml/<WORKSPACE_ID>/acs` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、URL として「`https://service.exium.net/sign-in`」と入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Exium クライアント サポート チーム](mailto:support@exium.net)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

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

このセクションでは、B.Simon に Exium へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Exium]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-exium-sso"></a>Exium の SSO の構成

1. Exium 企業サイトに管理者としてサインインします。

1. **管理コンソール** で **[Company Profile]\(会社のプロファイル\)** パネルを選択します。

    ![管理コンソールのスクリーンショット](./media/exium-tutorial/company-profile.png)

1. **[Profile]\(プロファイル\)** で **[SSO Settings]\(SSO の設定\)** をクリックし、それを **編集** します。

1. **[SSO Settings]\(SSO の設定\)** セクションで次の手順を実行します。

    ![[SSO Settings]\(SSO の設定\) のスクリーンショット](./media/exium-tutorial/update.png)

    a. **[SSO Type]\(SSO の種類\)** ボックスの一覧から **[AzureAD]** を選択します。

    b. **[アプリのフェデレーション メタデータ URL]** の値を **[SAML 2.0 IDP Metadata URL]\(SAML 2.0 IDP メタデータ URL\)** フィールドに貼り付けます。

    c. **[SAML 2.0 SSO URL]** の値をコピーし、その値を Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    d. **[SAML 2.0 SP Entity ID]\(SAML 2.0 SP エンティティ ID\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。  

    e. **[更新]** をクリックします。

### <a name="create-exium-test-user"></a>Exium のテスト ユーザーの作成

1. Exium 企業サイトに管理者としてサインインします。

1. **[User Management]\(ユーザー管理\) -> [Users]\(ユーザー\)** に移動し、 **[Add User]\(ユーザーの追加\)** をクリックします。

    ![テスト ユーザーの作成のスクリーンショット](./media/exium-tutorial/add-user.png)

1. 次のページで必要なフィールドを入力し、 **[Save]\(保存\)** をクリックします。

    ![テスト ユーザーの作成フィールドと保存ボタンのスクリーンショット](./media/exium-tutorial/add-user-2.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Exium のサインオン URL にリダイレクトされます。 

* Exium のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Exium] タイルをクリックすると、Exium のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Exium を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。


