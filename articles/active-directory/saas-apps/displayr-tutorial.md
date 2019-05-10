---
title: チュートリアル:Azure Active Directory と Displayr の統合 | Microsoft Docs
description: Azure Active Directory と Displayr の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9694790ea02bc778bf3b9db212e61fabb90a28a8
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441433"
---
# <a name="tutorial-azure-active-directory-integration-with-displayr"></a>チュートリアル:Azure Active Directory と Displayr の統合

このチュートリアルでは、Displayr と Azure Active Directory (Azure AD) を統合する方法について説明します。
Displayr と Azure AD の統合には、次の利点があります。

* Displayr にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Displayr に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Displayr と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Displayr でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Displayr では、**SP** によって開始される SSO がサポートされます

## <a name="adding-displayr-from-the-gallery"></a>ギャラリーからの Displayr の追加

Azure AD への Displayr の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Displayr を追加する必要があります。

**ギャラリーから Displayr を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Displayr**」と入力し、結果ウィンドウで **[Displayr]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Displayr](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Displayr で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Displayr 内の関連ユーザー間にリンク関係が確立されている必要があります。

Displayr で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Displayr シングル サインオンの構成](#configure-displayr-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Displayr のテスト ユーザーの作成](#create-displayr-test-user)** - Displayr で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Displayr で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Displayr** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Displayr のドメインと URL] のシングル サインオン情報](common/sp-intiated.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.displayr.com/Login`」と入力します。

5. **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (未加工)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. Displayr アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]**  アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. その他に、Displayr アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。  **[グループ要求 (プレビュー)]**  ダイアログの  **[ユーザー属性とクレーム]**  セクションで、次の手順を実行します。

    a. **[Groups returned in claim]\(要求で返されるグループ\)** の横にある**ペン**をクリックします。

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. ラジオ ボタンのリストから **[すべてのグループ]** を選択します。

    c. **[グループ ID]** の **[ソース属性]** を選択します。

    d. **[グループ要求の名前をカスタマイズする]** をオンにします。

    e. **[グループをロール要求として生成する]** をオンにします。

    f. **[Save]** をクリックします。

8. **[Set up Displayr]\(Displayr の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-displayr-single-sign-on"></a>Displayr のシングル サインオンを構成する

1. 別の Web ブラウザー ウィンドウで、Displayr に管理者としてサインインします。

2. **[設定]** をクリックし、**[アカウント]** に移動します。

    ![構成](./media/displayr-tutorial/config01.png)

3. 上部のメニューから **[設定]** に切り替え、ページを下へスクロールして **[Configure Single Sign On (SAML)]\(シングル サインオンの構成 (SAML)\)** をクリックします。

    ![構成](./media/displayr-tutorial/config02.png)

4. **[Single sign-on (SAML)]\(シングル サインオン (SAML)\)** ページで、次の手順に従います。

    ![構成](./media/displayr-tutorial/config03.png)

    a. **[Enable Single Sign On (SAML)]\(シングル サインオン (SAML) を有効にする\)** ボックスをオンにします。

    b. **[Issuer]\(発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[ログイン URL]** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[ログアウト URL]** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    e. 証明書 (未加工) をメモ帳で開き、その内容をコピーして **[証明書]** ボックスに貼り付けます。

    f. **グループ マッピング**は省略可能です。

    g. **[Save]** をクリックします。  

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

このセクションでは、Britta Simon に Displayr へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Displayr]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Displayr]** を選択します。

    ![アプリケーションの一覧の [Displayr] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** をクリックします。

### <a name="create-displayr-test-user"></a>Displayr テスト ユーザーを作成する

Azure AD ユーザーが Displayr にサインインできるようにするには、ユーザーを Displayr にプロビジョニングする必要があります。 Displayr では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Displayr にサインインします。

2. **[設定]** をクリックし、**[アカウント]** に移動します。

    ![Displayr の構成](./media/displayr-tutorial/config01.png)

3. 上部のメニューから **[設定]** に切り替えて、**[ユーザー]** セクションまでページを下にスクロールして、**[新しいユーザー]** をクリックします。

    ![Displayr の構成](./media/displayr-tutorial/config07.png)

4. **[新しいユーザー]** ページで、次の手順を実行します。

    ![Displayr の構成](./media/displayr-tutorial/config06.png)

    a. **[名前]** ボックスに、ユーザーの名前を入力します (例: **Brittasimon**)。

    b. **[電子メール]** ボックスに、ユーザーのメール アドレスを入力します (例: `Brittasimon@contoso.com`)。

    c. 適切な**グループ メンバーシップ**を選択します。

    d. **[Save]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Displayr] タイルをクリックすると、SSO を設定した Displayr に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

