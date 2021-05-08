---
title: チュートリアル:SAP Cloud for Customer と Azure Active Directory のシングル サインオン (SSO) 統合 | Microsoft Docs
description: Azure Active Directory と SAP Cloud for Customer の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 60381c7d8c452277b53e1af67ae7fc85349521c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735676"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>チュートリアル:SAP Cloud for Customer と Azure Active Directory のシングル サインオン (SSO) 統合

このチュートリアルでは、SAP Cloud for Customer と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Cloud for Customer と Azure AD を統合すると、次のことができます。

* SAP Cloud for Customer にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して SAP Cloud for Customer に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。


## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SAP Cloud for Customer でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SAP Cloud for Customer では、**SP** によって開始される SSO がサポートされます

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>ギャラリーから SAP Cloud for Customer を追加する

Azure AD への SAP Cloud for Customer の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Cloud for Customer を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SAP Cloud for Customer**」と入力します。
1. 結果のパネルから **[SAP Cloud for Customer]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>SAP Cloud for Customer の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP Cloud for Customer に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SAP Cloud for Customer の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Cloud for Customer に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SAP Cloud for Customer SSO の構成](#configure-sap-cloud-for-customer-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAP Cloud for Customer テスト ユーザーの作成](#create-sap-cloud-for-customer-test-user)** - SAP Cloud for Customer で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SAP Cloud for Customer** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<server name>.crm.ondemand.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[SAP Cloud for Customer クライアント サポート チーム](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. SAP Cloud for Customer アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![[編集] アイコンが選択されている [ユーザー属性] ダイアログを示すスクリーンショット。](common/edit-attribute.png)

1. **[ユーザー属性とクレーム]** ダイアログの **[ユーザー属性]** セクションで、次の手順を実行します。

    a. **[編集]** アイコンをクリックして、 **[ユーザー要求の管理]** ダイアログを開きます。

    ![[編集] アイコンが選択されている [ユーザー属性とクレーム] を示すスクリーンショット。](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. **ソース** として **[変換]** を選択します。

    c. **[変換]** の一覧で、**ExtractMailPrefix()** を選択します。

    d. **[パラメーター 1]** 一覧から、実装で使用するユーザー属性を選択します。
    たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、[user.extensionattribute2] を選択します。

    e. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[SAP Cloud for Customer のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に SAP Cloud for Customer へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAP Cloud for Customer]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sap-cloud-for-customer-sso"></a>SAP Cloud for Customer SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、SAP Cloud for Customer 企業サイトに管理者としてサインインします。

2. メニューの左側で **[Identity Providers]\(ID プロバイダー\)**  >  **[Corporate Identity Providers]\(企業 ID プロバイダー\)**  >  **[Add]\(追加\)** の順にクリックし、ポップアップで ID プロバイダー名 (**Azure AD** など) を追加します。 **[Save]\(保存\)** をクリックしてから、 **[SAML 2.0 Configuration]\(SAML 2.0 構成\)** をクリックします。

    ![[Add Identity Provider]\(ID プロバイダーの追加\) テキスト ボックスが強調表示され、[Save]\(保存\) ボタンが選択されている [Identity Providers]\(ID プロバイダー\) ページを示すスクリーンショット。](./media/sap-customer-cloud-tutorial/configure01.png)

3. **[SAML 2.0 Configuration]\(SAML 2.0 の構成\)** セクションで、次の手順を実行します。

    ![[Browse]\(参照\) ボタンが選択されている [S A M L 2.0 Configuration]\(S A M L 2.0 の構成\) を示すスクリーンショット。](./media/sap-customer-cloud-tutorial/configure02.png)

    a. **[Browse]\(参照\)** をクリックし、Azure portal からダウンロードしたフェデレーション メタデータ XML ファイルをアップロードします。

    b. XML ファイルが正常にアップロードされると、以下の値が自動的に設定されるので、 **[Save]\(保存\)** をクリックします。

### <a name="create-sap-cloud-for-customer-test-user"></a>SAP Cloud for Customer のテスト ユーザーの作成

Azure AD ユーザーが SAP Cloud for Customer にサインインできるようにするには、そのユーザーを SAP Cloud for Customer にプロビジョニングする必要があります。 SAP Cloud for Customer では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として SAP Cloud for Customer にサインインします。

2. メニューの左側で **[Users & Authorizations]\(ユーザーと承認\)**  >  **[User Management]\(ユーザー管理\)**  >  **[Add User]\(ユーザーの追加\)** の順にクリックします。

    ![[Add User]\(ユーザーの追加\) ボタンが選択されている [User Management]\(ユーザー管理\) ページを示すスクリーンショット。](./media/sap-customer-cloud-tutorial/configure03.png)

3. **[Add New User]\(新しいユーザーの追加\)** セクションで、次の手順を実行します。

    ![SAP 構成](./media/sap-customer-cloud-tutorial/configure04.png)

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **B**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    c. **[E-Mail]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: `B.Simon@contoso.com`)。

    d. **[Login Name]\(ログイン名\)** ボックスに、ユーザーの名前を入力します (例: **B.Simon**)。

    e. 要件に従って **[User Type]\(ユーザーの種類\)** を選択します。

    f. 要件に従って **[Account Activation]\(アカウントのアクティブ化\)** を選択します。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAP Cloud for Customer のサインオン URL にリダイレクトされます。 

* SAP Cloud for Customer のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SAP Cloud for Customer] タイルをクリックすると、SAP Cloud for Customer のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

SAP Cloud for Customer を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。