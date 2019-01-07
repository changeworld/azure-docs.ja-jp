---
title: 'チュートリアル: Azure Active Directory と Procore SSO の統合 | Microsoft Docs'
description: Azure Active Directory と Procore SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 12e7a37c1d2811b44dbf943b460b7d620770d45e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017527"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>チュートリアル: Azure Active Directory と Procore SSO の統合

このチュートリアルでは、Procore SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。

Procore SSO と Azure AD を統合すると、次の利点が得られます。

- Procore SSO にアクセス可能な Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Procore SSO へサインオンされる (シングル サインオン) ようにできます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Procore SSO と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Procore SSO でのシングル サインオン (SSO) が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Procore SSO の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-procore-sso-from-the-gallery"></a>ギャラリーからの Procore SSO の追加

Azure AD への Procore SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Procore SSO を追加する必要があります。

**ギャラリーから Procore SSO を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Procore SSO**」と入力し、結果パネルから **[Procore SSO]** を選択してから、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リスト内の Procore SSO](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーを基に、Procore SSO で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Procore SSO ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Procore SSO の関連ユーザーの間で、リンク関係が確立されている必要があります。

Procore SSO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Procore SSO のテスト ユーザーの作成](#creating-a-procore-sso-test-user)** - Procore SSO 内で Britta Simon に対応するユーザーを作成し、Azure AD 内の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にして、Procore SSO のアプリケーションでシングル サインオンを構成します。

**Procore SSO で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal 上の **Procore SSO** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. アプリは Azure と事前に統合済みであるため、**[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[Procore SSO のドメインと URL] のシングル サインオン情報](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**フェデレーション メタデータの XML** をダウンロードし、メタデータ ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. **[Procore SSO のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![Procore SSO の構成](common/configuresection.png)

7. **Procore SSO** 側のシングル サインオンを構成するために、Procore の企業サイトに管理者としてログインします。

8. [TOOLBOX] \(ツールボックス) ドロップ ダウンから **[Admin] \(管理)** をクリックして、SSO 設定ページを開きます。

    ![Configure single sign-on](./media/procoresso-tutorial/procore_tool_admin.png)

9. 以下の説明に従って、各ボックスに値を貼り付けます。

    ![Configure single sign-on](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. **[Single Sign On Issuer URL]\(シングル サインオン発行者 URL\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    b. **[SAML Sign On Target URL]\(SAML サインオン ターゲット URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. 次に、先ほど Azure portal からダウンロードした**フェデレーション メタデータの XML** を開いて、**X509Certificate** という名前のタグ内の証明書をコピーします。 コピーした値を、**[Single Sign On x509 Certificate] \(シングル サインオン x509 証明書)** ボックスに貼り付けます。

10. **[Save Changes] \(変更を保存)** をクリックします。

11. 上記の設定後、Procore へのログインで経由する**ドメイン名** (例: **contoso.com**) を [Procore のサポート チーム](https://support.procore.com/)に送信して、このドメインのフェデレーション SSO を有効化してもらう必要があります。

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
       たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-procore-sso-test-user"></a>Procore SSO のテスト ユーザーの作成

以下の手順に従って、Procore SSOc の側で Procore テスト ユーザーを作成します。

1. Procore 企業サイトに管理者としてログインします。  

2. [TOOLBOX] \(ツールボックス) ドロップ ダウンで **[Directory] \(ディレクトリ)** をクリックして、企業のディレクトリ ページを開きます。

    ![Configure single sign-on](./media/procoresso-tutorial/Procore_sso_directory.png)

3. **[Add a Person] \(ユーザーの追加)** オプションをクリックしてフォームを開き、次の手順を実行します。

    ![Configure single sign-on](./media/procoresso-tutorial/Procore_user_add.png)

    a. **[First Name] \(名)** テキストボックスに、ユーザーの名を入力します (この例では **Britta**).

    b. **[Last Name] \(姓)** テキストボックスに、ユーザーの姓を入力します (この例では **Simon**).

    c. **[Email Address] \(メール アドレス)** テキストボックスに、ユーザーのメール アドレスを入力します (この例では **BrittaSimon@contoso.com**)。

    d. **[Permission Template] \(アクセス許可テンプレート)** で **[Apply Permission Template Later] \(アクセス許可テンプレートは後で適用する)** を選択します。

    e. **Create** をクリックしてください。

4. 新しく追加された連絡先の詳細を確認して更新します。

    ![Configure single sign-on](./media/procoresso-tutorial/Procore_user_check.png)

5. メールによる招待状が必要な場合は **[Save and Send Invitiation] \(保存して招待状を送信)** を、直接保存する場合は **[Save] \(保存)** をクリックして、ユーザー登録を完了します。
    
    ![Configure single sign-on](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Procore SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Procore SSO]** を選択します。

    ![Configure single sign-on](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Procore SSO] タイルをクリックすると、自動的に Procore SSO アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
