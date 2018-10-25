---
title: 'チュートリアル: Azure Active Directory と Jamf Pro の統合 | Microsoft Docs'
description: Azure Active Directory と Jamf Pro の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: d28e28a2c4f8144da16c4838f07c9b8bb5ce67f0
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268159"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>チュートリアル: Azure Active Directory と Jamf Pro の統合

このチュートリアルでは、Jamf Pro と Azure Active Directory (Azure AD) を統合する方法について説明します。

Jamf Pro と Azure AD の統合には、次の利点があります。

- Jamf Pro にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Jamf Pro にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Jamf Pro と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Jamf Pro シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Jamf Pro の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-jamf-pro-from-the-gallery"></a>ギャラリーからの Jamf Pro の追加

Azure AD への Jamf Pro の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Jamf Pro を追加する必要があります。

**ギャラリーから Jamf Pro を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/jamfprosamlconnector-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/jamfprosamlconnector-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/jamfprosamlconnector-tutorial/a_new_app.png)

4. 検索ボックスに「**Jamf Pro**」と入力し、結果パネルで **[Jamf Pro]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![image](./media/jamfprosamlconnector-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Jamf Pro で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Jamf Pro ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Jamf Pro の関連ユーザーの間で、リンク関係が確立されている必要があります。

Jamf Pro で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Jamf Pro テスト ユーザーの作成](#create-a-jamf-pro-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Jamf Pro で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Jamf Pro アプリケーションでシングル サインオンを構成します。

**Jamf Pro で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. [Azure portal](https://portal.azure.com/) の **Jamf Pro** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_select_sso.png)

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

      ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_ssso.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_sso.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/jamfprosamlconnector-tutorial/b1-domains_and_urlsedit.png)

5. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/SSO` のパターンを使用して URL を入力します。

    ![image](./media/jamfprosamlconnector-tutorial//b2-domains_and_urls.png)

    c. **[追加の URL を設定します]** をクリックします。

    d. **[サインオン URL]** ボックスに、`https://<subdomain>.jamfcloud.com` というパターンを使用して URL を入力します。

    ![image](./media/jamfprosamlconnector-tutorial//b4-domains_and_urls.png)

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 実際の識別子の値を Jamf Pro ポータルの **[シングル サインオン]** セクションから取得します。これについては、このチュートリアルで後ほど説明します。 実際の**サブドメイン**の値を識別子の値から抽出し、サインオン URL と応答 URL 内のその**サブドメイン**情報を使用できます。

6. **Set up Single Sign-On with SAML(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![image](./media/jamfprosamlconnector-tutorial/C2_certificate.png)

7. Jamf Pro 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして**マイアプリによるセキュリティで保護されたサインイン拡張機能**をインストールする必要があります。

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)
 
8. ブラウザーに拡張機能を追加した後、**[setup Jamf Pro]\(Jamf Pro の設定)** をクリックすると、Jamf Pro アプリケーションに移動します。 そこから、管理者資格情報を提供して Jamf Pro にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 9 ～ 12 が自動化されます。

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

9. Jamf Pro を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Jamf Pro 企業サイトにログインして、次の手順を実行します。

10. ページの右上隅の**設定アイコン**をクリックします。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure1.png)

11. **[シングル サインオン]** をクリックします。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure2.png)

12. **[Single sign-on]\(シングル サインオン\)** ページで、次の手順を実行します。

    ![Jamf Pro シングル](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. **[Jamf Pro Server]\(Jamf Pro サーバー\)** を選択してシングル サインオン アクセスを有効にします。

    b. **[Allow bypass for all users]\(すべてのユーザーにバイパスを許可する\)** を選択すると、ユーザーは認証のために ID プロバイダーのログイン ページへとリダイレクトされなくなり、Jamf Pro に直接ログインできるようになります。 ユーザーが ID プロバイダーを経由して Jamf Pro にアクセスしようとした場合は、IdP によって開始される SSO 認証と承認が発生します。

    c. **[USER MAPPING: SAML]\(ユーザー マッピング: SAML\)** の **[NameID]** オプションを選択します。 既定では、この設定は **[NameID]** に設定されていますが、カスタム属性を定義することもできます。

    d. **[USER MAPPING: JAMF PRO]\(ユーザー マッピング: JAMF PRO\)** の **[Email]\(電子メール\)** を選択します。 Jamf Pro は、IdP によって送信された SAML 属性を次の方法でマップします: ユーザー別およびグループ別。 ユーザーが Jamf Pro にアクセスしようとすると、既定では、Jamf Pro がユーザーに関する情報を ID プロバイダーから取得し、それを Jamf Pro のユーザー アカウントと照合します。 受信したユーザー アカウントが Jamf Pro 内に存在しない場合は、グループ名のマッチングが発生します。

    e. 値 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` を **[GROUP ATTRIBUTE NAME]\(グループ属性名\)** テキスト ボックスに貼り付けます。

13. 同じページで、**[シングル サインオン]** セクションの下の **[IDENTITY PROVIDER]\(ID プロバイダー\)** まで下にスクロールし、次の手順に従います。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **[IDENTITY PROVIDER]\(ID プロバイダー\)** ドロップダウン メニューから **[Other]\(その他\)** を選択します。

    b. **[OTHER PROVIDER]\(その他のプロバイダー\)** ボックスに、**[Azure AD]** を入力します。

    c. **[メタデータ URL]** を **[IDENTITY PROVIDER METADATA SOURCE]\(ID プロバイダーのメタデータ ソース\)** ドロップダウンからオプションとして選択し、次のテキストボックスに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    d. **エンティティ ID** の値をコピーし、Azure portal の **[Jamf Pro Domain and URLs]\(Jamf Pro のドメインと URL\)** セクションの **[識別子 (エンティティ ID)]** テキストボックスに貼り付けます。

    >[!NOTE]
    > ここで、隠されている値はサブドメインの部分です。この値を使用して、Azure portal 上で、**[Jamf Pro Domain and URLs]\(Jamf Pro ドメインおよび URL\)** セクションのサインオン URL と応答 URL を完了します。

    e. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/jamfprosamlconnector-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/jamfprosamlconnector-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![image](./media/jamfprosamlconnector-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、**brittasimon@yourcompanydomain.extension** と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="create-a-jamf-pro-test-user"></a>Jamf Pro テスト ユーザーの作成

Azure AD ユーザーが Jamf Pro にログインできるようにするには、ユーザーを Jamf Pro にプロビジョニングする必要があります。 Jamf Pro の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Jamf Pro の企業サイトに管理者としてログインします。

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

    b. **[ACCESS LEVEL]\(アクセス レベル\)**、**[PRIVILEGE SET]\(特権セット\)**、および **[ACCESS STATUS]\(アクセスの状態\)** に対して組織に従って適切なオプションを選択します。

    c. **[FULL NAME]\(フル ネーム\)** テキストボックスに、フル ネームの「Britta Simon」を入力します。

    d. **[メール アドレス]** テキストボックスに、Britta Simon アカウントのメール アドレスを入力します。

    e. **[パスワード]** テキストボックスに、そのユーザーのパスワードを入力します。

    f. **[VERIFY PASSWORD]\(パスワードの確認\)** テキストボックスに、そのユーザーのパスワードを入力します。

    g. **[Save]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Jamf Pro へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Jamf Pro]** を選択します。

    ![image](./media/jamfprosamlconnector-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[Jamf Pro]** を選択します。

    ![image](./media/jamfprosamlconnector-tutorial/d_all_proapplications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/jamfprosamlconnector-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/jamfprosamlconnector-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Jamf Pro のタイルをクリックすると、自動的に Jamf Pro アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
