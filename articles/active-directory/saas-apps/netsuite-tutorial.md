---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と NetSuite の統合 | Microsoft Docs
description: Azure Active Directory と NetSuite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438500"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と NetSuite の統合

このチュートリアルでは、NetSuite と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と NetSuite を統合すると、次のことができます。

* NetSuite にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して NetSuite に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* NetSuite でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* NetSuite では、**IDP** によって開始される SSO がサポートされます

* NetSuite では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

* NetSuite では、[自動化されたユーザー プロビジョニング](NetSuite-provisioning-tutorial.md)がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-netsuite-from-the-gallery"></a>ギャラリーからの NetSuite の追加

Azure AD への NetSuite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に NetSuite を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**NetSuite**」と入力します。
1. 結果のパネルから **[NetSuite]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>NetSuite の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、NetSuite に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと NetSuite の関連ユーザーとの間にリンク関係を確立する必要があります。

NetSuite での Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[NetSuite SSO の構成](#configure-netsuite-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[NetSuite のテスト ユーザーの作成](#create-netsuite-test-user)** - NetSuite で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **NetSuite** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[NetSuite クライアント サポート チーム](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. NetSuite アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

1. その他に、NetSuite アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 [ユーザー属性] ダイアログの [ユーザー要求] セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | 名前 | ソース属性 | 
    | ---------------| --------------- |
    | account  | `account id` |

    1. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    1. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    1. **[名前空間]** は空白のままにします。

    1. [ソース] として **[属性]** を選択します。

    1. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    1. **[OK]** をクリックします。

    1. **[Save]** をクリックします。

    >[!NOTE]
    >account 属性の値は、実際のものではありません。 この値は後で置き換えます。これについては、このチュートリアルで後ほど説明します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[NetSuite のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に NetSuite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[NetSuite]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-netsuite-sso"></a>NetSuite の SSO の構成

1. ブラウザーで新しいタブを開き、NetSuite の会社のサイトに管理者としてサインインします。

2. ページの上部にあるツール バーで、 **[セットアップ]** をクリックして、 **[会社]** に移動し、 **[機能の有効化]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setupsaml.png)

3. ページの中央にあるツール バーで、 **[SuiteCloud]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-suitecloud.png)

4. **[Manage Authentication]\(認証の管理)** セクションで、 **[SAML シングル サインオン]** を選択して、NetSuite での SAML シングル サインオン オプションを有効にします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-ticksaml.png)

5. ページの上部にあるツール バーで、 **[セットアップ]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

6. **[セットアップ タスク]** 一覧で、 **[統合]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-integration.png)

7. **[Manage Authentication]\(認証の管理\)** セクションで、 **[SAML シングル サインオン]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-saml.png)

8. **[SAML Setup]\(SAML セットアップ\)** ページの **[NetSuite の構成]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **[PRIMARY AUTHENTICATION METHOD]\(プライマリ認証方法\)** を選択します。

    b. **[SAMLV2 IDENTITY PROVIDER METADATA]\(SAMLV2 ID プロバイダー メタデータ\)** というラベルが付いたフィールドで、 **[UPLOAD IDP METADATA FILE]\(IDP メタデータ ファイルのアップロード\)** を選択します。 **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    c. **[送信]** をクリックします。

9. NetSuite で **[セットアップ]** をクリックし、 **[会社]** に移動して、上部の ナビゲーション メニューから **[会社情報]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-com.png)

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-account-id.png)

    b. 右側の列の **[会社情報]** ページで、**アカウント ID** をコピーします。

    c. NetSuite アカウントからコピーした**アカウント ID** を Azure AD の**属性値**フィールドに貼り付けます。 

10. ユーザーは NetSuite にシングル サインオンする前に、まず、NetSuite で適切なアクセス許可が割り当てられている必要があります。 次の手順に従って、アクセス許可を割り当てます。

    a. ナビゲーション メニューで、 **[セットアップ]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

    b. 左側のナビゲーション メニューで、 **[Users/Roles]** 、 **[Manage Roles]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. **[New Role]** をクリックします。

    d. 新しいロールの**名前**を入力します:

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-new-role.png)

    e. **[Save]** をクリックします。

    f. 上部のメニューで、 **[Permissions]** をクリックします。 **[Setup]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-sso.png)

    g. **[SAML シングル サインオン]** を選択して、 **[追加]** をクリックします。

    h. **[Save]** をクリックします。

    i. 上部のナビゲーション メニューで、 **[Setup]** 、 **[Setup Manager]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

    j. 左側のナビゲーション メニューで、 **[Users/Roles]** 、 **[Manage Users]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-manage-users.png)

    k. テスト ユーザーを選択します。 次に、 **[編集]** をクリックし、 **[アクセス]** タブに移動します。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-edit-user.png)

    l. [ロール] ダイアログで、作成した適切なロールを割り当てます。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-add-role.png)

    m. **[Save]** をクリックします。

### <a name="create-netsuite-test-user"></a>NetSuite のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを NetSuite に作成します。 NetSuite では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 NetSuite にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [NetSuite] タイルをクリックすると、SSO を設定した NetSuite に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で NetSuite を試す](https://aad.portal.azure.com/)

