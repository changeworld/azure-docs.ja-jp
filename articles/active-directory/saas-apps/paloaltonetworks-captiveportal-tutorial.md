---
title: チュートリアル:Azure Active Directory と Palo Alto Networks - Captive Portal の統合 | Microsoft Docs
description: Azure Active Directory と Palo Alto Networks - Captive Portal の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: eff08cc17f475e2b6ad6406e463de27371bbe5b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064731"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>チュートリアル:Azure Active Directory と Palo Alto Networks - Captive Portal の統合

このチュートリアルでは、Palo Alto Networks - Captive Portal と Azure Active Directory (Azure AD) を統合する方法について説明します。
Palo Alto Networks - Captive Portal と Azure AD の統合には、次のメリットがあります。

* Palo Alto Networks - Captive Portal にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Palo Alto Networks - Captive Portal に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Palo Alto Networks - Captive Portal の統合を構成するには、次のアイテムが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Palo Alto Networks - Captive Portal でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Palo Alto Networks - Captive Portal では、**IDP** によって開始される SSO がサポートされます

* Palo Alto Networks - Captive Portal では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>ギャラリーからの Palo Alto Networks - Captive Portal の追加

Azure AD への Palo Alto Networks - Captive Portal の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Palo Alto Networks - Captive Portal を追加する必要があります。

**ギャラリーから Palo Alto Networks - Captive Portal を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Palo Alto Networks - Captive Portal**」と入力し、結果ウィンドウで **[Palo Alto Networks - Captive Portal]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Palo Alto Networks - Captive Portal](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Palo Alto Networks - Captive Portal で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Palo Alto Networks - Captive Portal 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Palo Alto Networks - Captive Portal で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Palo Alto Networks - Captive Portal のシングル サインオンの構成](#configure-palo-alto-networks---captive-portal-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Palo Alto Networks - Captive Portal テスト ユーザーの作成](#create-palo-alto-networks---captive-portal-test-user)** - Palo Alto Networks - Captive Portal で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Palo Alto Networks - Captive Portal で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Palo Alto Networks - Captive Portal** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を実行します。

    ![[Palo Alto Networks - Captive Portal のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<Customer Firewall Hostname>/SAML20/SP` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<Customer Firewall Hostname>/SAML20/SP/ACS` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Palo Alto Networks - Captive Portal クライアント サポート チーム](https://support.paloaltonetworks.com/support)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="configure-palo-alto-networks---captive-portal-single-sign-on"></a>Palo Alto Networks - Captive Portal のシングル サインオンの構成

1. 別のブラウザー ウィンドウで Palo Alto サイトを管理者として開きます。

2. **[Device]\(デバイス\)** をクリックします。

    ![Palo Alto シングル サインオンの構成](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 左側のナビゲーション バーから **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、[Import]\(インポート\) をクリックしてメタデータ ファイルをインポートします。

    ![Palo Alto シングル サインオンの構成](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. [Import]\(インポート\) ウィンドウで次の操作を実行します。

    ![Palo Alto シングル サインオンの構成](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. **[Profile Name]\(プロファイル名\)** ボックスに名前 (「Azure AD Admin UI」など) を入力します。
    
    b. **[Identity Provider Metadata]\(ID プロバイダーのメタデータ\)** の **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードした metadata.xml ファイルを選択します。
    
    c. **[OK]**

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Palo Alto Networks - Captive Portal へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Palo Alto Networks - Captive Portal]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**Palo Alto Networks - Captive Portal**」と入力して選択します。

    ![アプリケーションの一覧の [Palo Alto Networks - Captive Portal] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-palo-alto-networks---captive-portal-test-user"></a>Palo Alto Networks - Captive Portal のテスト ユーザーの作成

このセクションでは、Palo Alto Networks - Captive Portal で Britta Simon というユーザーを作成します。 Palo Alto Networks - Captive Portal では、**Just-In-Time ユーザー プロビジョニング**がサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Palo Alto Networks - Captive Portal にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Palo Alto Networks - Captive Portal クライアント サポート チーム](https://support.paloaltonetworks.com/support)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

Captive Portal は、Windows VM 上のファイアウォールの内側に構成されます。 Captive Portal のシングル サインオンをテストするには、RDP を使用して Windows VM にログインします。 RDP セッション内でブラウザーを開いて任意の Web サイトにアクセスすると、自動的に SSO の URL が開いて認証情報が要求されます。 認証が完了すると、Web サイトに移動することができます。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

