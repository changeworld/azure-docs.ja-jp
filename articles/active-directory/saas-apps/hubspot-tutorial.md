---
title: チュートリアル:Azure Active Directory と HubSpot の統合 | Microsoft Docs
description: Azure Active Directory と HubSpot の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adcd0f094d584e770f1a3f4938ee677ba58a21a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995695"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>チュートリアル:Azure Active Directory と HubSpot の統合

このチュートリアルでは、HubSpot と Azure Active Directory (Azure AD) を統合する方法について説明します。
HubSpot と Azure AD の統合には、次の利点があります。

* HubSpot にアクセスできるユーザーを Azure AD 上で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して HubSpot に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

HubSpot と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* HubSpot でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* HubSpot では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-hubspot-from-the-gallery"></a>ギャラリーからの HubSpot の追加

Azure AD への HubSpot の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HubSpot を追加する必要があります。

**ギャラリーから HubSpot を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 結果ボックスに「**HubSpot**」と入力し、結果パネルで **[HubSpot]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの HubSpot](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、HubSpot で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと HubSpot 内の関連ユーザー間にリンク関係が確立されている必要があります。

HubSpot で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[HubSpot シングル サインオンの構成](#configure-hubspot-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[HubSpot のテスト ユーザーの作成](#create-hubspot-test-user)** - HubSpot で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

HubSpot で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **HubSpot** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[HubSpot のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL に値を置き換えます。実際の値については後で説明します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[HubSpot のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに、URL として「`https://app.hubspot.com/login`」と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[HubSpot のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-hubspot-single-sign-on"></a>HubSpot のシングル サインオンの構成

1. ブラウザーで新しいタブを開き、HubSpot の管理者アカウントにサインインします。

2. ページの右上隅の**設定アイコン**をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/config1.png)

3. **[Account Defaults] (アカウントの既定値)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/config2.png)

4. **[セキュリティ]** セクションまで下へスクロールし、**[Set up] (セットアップ)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/config3.png)

5. **[Set up single sign-on]\(シングル サインオンの設定\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/hubspot-tutorial/config4.png)

    a. **[コピー]** ボタンをクリックして、**[Audience URl(Service Provider Entity ID)] (対象ユーザー URI(サービス プロバイダー エンティティ ID))** 値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[Identifier] (識別子)** テキスト ボックスに貼り付けます。

    b. **[コピー]** ボタンをクリックして、**[Sign on URl,ACS,Recipient, or Redirect] (サインオン URI、ACS、受信者、またはリダイレクト)** 値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[Reply URL] (応答 URL)** テキスト ボックスに貼り付けます。

    c. **[Identity Provider Identifier or Issuer URL] (ID プロバイダーの識別子または発行者 URL)** テキストボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    d. **[ID プロバイダー のシングル サインオン URL]** テキストボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    e. ダウンロードした **証明書 (Base64)**  ファイルをメモ帳で開きます。 その内容をクリップボードにコピーし、 **[X.509 証明書]** テキストボックスに貼り付けます。

    f. **[確認]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に HubSpot へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[HubSpot]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[HubSpot]** を選択します。

    ![アプリケーションの一覧の HubSpot のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-hubspot-test-user"></a>HubSpot のテスト ユーザーの作成

Azure AD ユーザーが HubSpot にサインインできるようにするには、そのユーザーを HubSpot にプロビジョニングする必要があります。 HubSpot の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **HubSpot** 企業サイトに管理者としてサインインします。

2. ページの右上隅の**設定アイコン**をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/config1.png)

3. **[Users & Teams]\(ユーザーとチーム)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/user1.png)

4. **[Create user]\(ユーザーの作成\)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/user2.png)

5. **[Add email addess(es)]\(メール アドレスの追加\)** ボックスにユーザーのメール アドレスを `brittasimon\@contoso.com` のように入力し、**[Next]\(次へ\)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/user3.png)

6. **[ユーザーの作成]** セクションで、個々のタブをすべて移動し、ユーザーの適切なオプションやアクセス許可を選択して **[Next]\(次へ)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/user4.png)

7. **[送信]** をクリックしてユーザーに招待を送信します。

    ![Configure single sign-on](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > ユーザーは、招待状を受け取った後にアクティブ化されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [HubSpot] タイルをクリックすると、SSO を設定した HubSpot に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

