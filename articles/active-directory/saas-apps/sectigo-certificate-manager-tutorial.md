---
title: チュートリアル:Azure Active Directory と Sectigo Certificate Manager の統合 | Microsoft Docs
description: Azure Active Directory と Sectigo Certificate Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 15-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a847ea34182994eb6465905e042bfa0218993491
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999163"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>チュートリアル:Azure Active Directory と Sectigo Certificate Manager の統合

このチュートリアルでは、Sectigo Certificate Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。
Sectigo Certificate Manager と Azure AD を統合すると、次の利点があります。

* Sectigo Certificate Manager にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Sectigo Certificate Manager に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Sectigo Certificate Manager と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Sectigo Certificate Manager でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Sectigo Certificate Manager では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-sectigo-certificate-manager-from-the-gallery"></a>ギャラリーから Sectigo Certificate Manager を追加する

Azure AD への Sectigo Certificate Manager の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Sectigo Certificate Manager を追加する必要があります。

**ギャラリーから Sectigo Certificate Manager を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 結果ボックスに「**Sectigo Certificate Manager**」と入力し、結果パネルで **[Sectigo Certificate Manager]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Sectigo Certificate Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Sectigo Certificate Manager で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Sectigo Certificate Manager 内の関連ユーザー間にリンク関係が確立されている必要があります。

Sectigo Certificate Manager で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Sectigo Certificate Manager シングル サインオンの構成](#configure-sectigo-certificate-manager-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Sectigo Certificate Manager のテスト ユーザーの作成](#create-sectigo-certificate-manager-test-user)** - Sectigo Certificate Manager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Sectigo Certificate Manager で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Sectigo Certificate Manager** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![Sectigo Certificate Manager ドメインと URL のシングル サインオン情報](common/idp-relay.png)

    a. **[識別子]** ボックスに、次のいずれかの URL を入力します。
    
    | |
    |--|
    | `https://cert-manager.com/shibboleth` |
    | `https://hard.cert-manager.com/shibboleth` |

    b. **[応答 URL]** ボックスに、次のいずれかの URL を入力します。

    | |
    |--|
    | `https://cert-manager.com/Shibboleth.sso/SAML2/POST` |
    | `https://hard.cert-manager.com/Shibboleth.sso/SAML2/POST` |

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** ボックスに、次のいずれかの URL を入力します。
    
    | |
    |--|
    | `https://cert-manager.com/customer/SSLSupport/idp` |
    | `https://hard.cert-manager.com/customer/SSLSupport/idp` |

5.  **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![Sectigo Certificate Manager ドメインと URL のシングル サインオン情報](common/both-signonurl.png)

    **[サインオン URL]** ボックスに、次のいずれかの URL を入力します。
    
    | |
    |--|
    | `https://cert-manager.com/Shibboleth.sso/Login`|
    | `https://hard.cert-manager.com/Shibboleth.sso/Login`|

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Sectigo Certificate Manager のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Sectigo Certificate Manager シングル サインオンの構成

**Sectigo Certificate Manager** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Base64)** と Azure portal からコピーした適切な URL を [Sectigo Certificate Manager サポート チーム](https://sectigo.com/support)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

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

このセクションでは、Britta Simon に Sectigo Certificate Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Sectigo Certificate Manager]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Sectigo Certificate Manager]** を選択します。

    ![アプリケーションの一覧の Sectigo Certificate Manager のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-sectigo-certificate-manager-test-user"></a>Sectigo Certificate Manager のテスト ユーザーの作成

このセクションでは、Sectigo Certificate Manager で Britta Simon というユーザーを作成します。  [Sectigo Certificate Manager サポート チーム](https://sectigo.com/support)と連携して、Sectigo Certificate Manager プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Sectigo Certificate Manager] タイルをクリックすると、SSO を設定した Sectigo Certificate Manager に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

