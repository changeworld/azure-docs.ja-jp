---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Jamf Pro の統合 | Microsoft Docs
description: Azure Active Directory と Jamf Pro の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305424"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Jamf Pro の統合

このチュートリアルでは、Jamf Pro と Azure Active Directory (Azure AD) を統合する方法について説明します。 Jamf Pro を Azure AD に統合すると、次のことができます。

* Jamf Pro にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Jamf Pro に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Jamf Pro シングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Jamf Pro では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-jamf-pro-from-the-gallery"></a>ギャラリーからの Jamf Pro の追加

Azure AD への Jamf Pro の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Jamf Pro を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Jamf Pro**」と入力します。
1. 結果のパネルから **[Jamf Pro]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Jamf Pro の Azure AD シングル サインオンの構成とテスト

**B. Simon** というテスト ユーザーを使用して、Jamf Pro に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Jamf Pro の関連ユーザーとの間にリンク関係を確立する必要があります。

Jamf Pro で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Jamf Pro の SSO の構成](#configure-jamf-pro-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Jamf Pro のテスト ユーザーの作成](#create-jamf-pro-test-user)** - Jamf Pro で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Jamf Pro** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/SSO` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<subdomain>.jamfcloud.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 実際の識別子の値を Jamf Pro ポータルの **[シングル サインオン]** セクションから取得します。これについては、このチュートリアルで後ほど説明します。 実際の**サブドメイン**の値を識別子の値から抽出し、サインオン URL と応答 URL 内のその**サブドメイン**情報を使用できます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

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

このセクションでは、B.Simon に Jamf Pro へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Jamf Pro]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-jamf-pro-sso"></a>Jamf Pro の SSO を構成する

1. Jamf Pro 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして**マイアプリによるセキュリティで保護されたサインイン拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[setup Jamf Pro]\(Jamf Pro の設定)** をクリックすると、Jamf Pro アプリケーションに移動します。 そこから、管理者資格情報を提供して Jamf Pro にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Jamf Pro を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Jamf Pro 企業サイトにサインインして、次の手順を実行します。

4. ページの右上隅の**設定アイコン**をクリックします。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure1.png)

5. **[シングル サインオン]** をクリックします。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure2.png)

6. **[Single sign-on]\(シングル サインオン\)** ページで、次の手順を実行します。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **[Enable Single Sign-On Authentication]\(シングル サインオン認証を有効にする\)** チェック ボックスをオンにします。

    b. **[IDENTITY PROVIDER]\(ID プロバイダー\)** ドロップダウンから **[Other]\(その他\)** を選択します。

    c. **[OTHER PROVIDER]\(その他のプロバイダー\)** ボックスに、 **[Azure AD]** を入力します。

    d. **[ENTITY ID]\(エンティティ ID\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションにある **[Identifier (Entity ID)]\(識別子 (エンティティ ID)\)** ボックスに貼り付けます。

    > [!NOTE]
    > ここに `<SUBDOMAIN>` という部分があります。この値を使用して、Azure portal の **[基本的な SAML 構成]** セクションにおけるサインオン URL と応答 URL を完成させる必要があります。

    e. **[IDENTITY PROVIDER METADATA SOURCE]\(ID プロバイダーのメタデータ ソース\)** ドロップダウンのオプションとして **[Metadata URL]\(メタデータ URL\)** を選択し、次のテキストボックスに、Azure portal からコピーした**アプリのフェデレーション メタデータ URL** の値を貼り付けます。

7. 同じページで下にスクロールして **[ユーザー マッピング]** セクションを表示し、次の手順を実行します。 

    ![Jamf Pro シングル](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. **[IDENTITY PROVIDER USER MAPPING]\(ID プロバイダー ユーザー マッピング\)** で **[NameID]** オプションを選択します。 既定では、この設定は **[NameID]** に設定されていますが、カスタム属性を定義することもできます。

    b. **[JAMF PRO USER MAPPING]\(JAMF PRO ユーザー マッピング\)** の **[Email]\(電子メール\)** を選択します。 Jamf Pro は、IdP によって送信された SAML 属性を次の方法でマップします: ユーザー別およびグループ別。 ユーザーが Jamf Pro にアクセスしようとすると、既定では、Jamf Pro がユーザーに関する情報を ID プロバイダーから取得し、それを Jamf Pro のユーザー アカウントと照合します。 受信したユーザー アカウントが Jamf Pro 内に存在しない場合は、グループ名のマッチングが発生します。

    c. **[IDENTITY PROVIDER GROUP ATTRIBUTE NAME]\(ID プロバイダー グループ属性名\)** ボックスに値 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` を貼り付けます。

    d. **[Allow users to bypass the Single Sign-On authentication]\(ユーザーにシングル サインオン認証のバイパスを許可する\)** を選択すると、認証のための ID プロバイダーのサインイン ページにユーザーはリダイレクトされなくなり、Jamf Pro に直接サインインできるようになります。 ユーザーが ID プロバイダーを経由して Jamf Pro にアクセスしようとした場合は、IdP によって開始される SSO 認証と承認が発生します。

    e. **[Save]** をクリックします。

### <a name="create-jamf-pro-test-user"></a>Jamf Pro テスト ユーザーの作成

Azure AD ユーザーが Jamf Pro にサインインできるようにするには、ユーザーを Jamf Pro にプロビジョニングする必要があります。 Jamf Pro の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Jamf Pro の企業サイトに管理者としてサインインします。

2. ページの右上隅の**設定アイコン**をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/configure1.png)

3. **[Jamf Pro User Accounts & Groups]\(Jamf Pro ユーザー アカウントとグループ\)** をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user1.png)

4. **[新規]** をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user2.png)

5. **[Create Standard Account]\(標準アカウントの作成\)** を選択します。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user3.png)

6. **[新しいアカウント]** ダイアログで、次の手順に従います。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user4.png)

    a. **[ユーザー名]** テキストボックスに、フル ネームの「BrittaSimon」を入力します。

    b. **[ACCESS LEVEL]\(アクセス レベル\)** 、 **[PRIVILEGE SET]\(特権セット\)** 、および **[ACCESS STATUS]\(アクセスの状態\)** に対して組織に従って適切なオプションを選択します。

    c. **[FULL NAME]\(フル ネーム\)** テキストボックスに、フル ネームの「Britta Simon」を入力します。

    d. **[メール アドレス]** テキストボックスに、Britta Simon アカウントのメール アドレスを入力します。

    e. **[パスワード]** テキストボックスに、そのユーザーのパスワードを入力します。

    f. **[VERIFY PASSWORD]\(パスワードの確認\)** テキストボックスに、そのユーザーのパスワードを入力します。

    g. **[Save]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Jamf Pro] タイルをクリックすると、SSO を設定した Jamf Pro に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Jamf を試す](https://aad.portal.azure.com/)

