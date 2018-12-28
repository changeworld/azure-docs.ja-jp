---
title: 'チュートリアル: Azure Active Directory と Peakon の統合 | Microsoft Docs'
description: Azure Active Directory と Peakon の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: af3402aab6e4a3a1b0401d66d42e82e449552867
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2018
ms.locfileid: "51978121"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>チュートリアル: Azure Active Directory と Peakon の統合

このチュートリアルでは、Peakon と Azure Active Directory (Azure AD) を統合する方法について説明します。

Peakon と Azure AD の統合には、次の利点があります。

- Peakon にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Peakon にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Peakon と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Peakon へのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Peakon の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-peakon-from-the-gallery"></a>ギャラリーからの Peakon の追加

Azure AD への Peakon の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Peakon を追加する必要があります。

**ギャラリーから Peakon を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Peakon**」と入力し、結果パネルで **[Peakon]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Peakon](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Peakon で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Peakon ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Peakon の関連ユーザーの間で、リンク関係が確立されている必要があります。

Peakon で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Peakon テスト ユーザーの作成](#creating-a-peakon-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Peakon で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Peakonl アプリケーションでシングル サインオンを構成します。

**Peakon で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Peakon** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[Peakon のドメインと URL] のシングル サインオン情報](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. **[識別子]** ボックスに、`https://app.peakon.com/saml/<companyid>/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.peakon.com/saml/<companyid>/assert` のパターンを使用して URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Peakon のドメインと URL] のシングル サインオン情報](./media/peakon-tutorial/tutorial_peakon_url1.png)

    **[サインオン URL]** ボックスに、「`https://app.peakon.com/login`」と入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL に値を置き換えます。実際の値については後で説明します。

6. **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして **[証明書 (未加工)]** をダウンロードし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. **[Peakon の設定]** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![Peakon の構成](common/configuresection.png)

8. 別の Web ブラウザー ウィンドウで、管理者として Peakon にサインインします。

9. ページの左側にあるメニュー バーの  **[Configuration]\(構成\)** をクリックした後、 **[Integrations]\(統合\)** に移動します。

    ![構成](./media/peakon-tutorial/tutorial_peakon_config.png)

10. **[Integrations]\(統合\)** ページで、**[Single Sign-On]\(シングル サインオン\)** をクリックします。

    ![シングル](./media/peakon-tutorial/tutorial_peakon_single.png)

11. **[Single Sign-On]\(シングル サインオン\)** セクションで、**[Enable]\(有効化\)** をクリックします。

    ![有効化](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. **[Single sign-on for employees using SAML]\(シングル サインオンの設定\)** セクションで、次の手順に従います。

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. **[SSO Login URL]\(ログイン URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[SSO Logout URL]\(SSO ログアウト URL\)** テキスト ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    c. **[Choose a File]\(ファイルの選択\)** を選択して、Azure portal からダウンロードした証明書を [Certificate]\(証明書\) ボックスにアップロードします。

    d.  **アイコン** をクリックして **[Entity ID]\(エンティティ ID\)** をコピーし、Azure portal で  **[基本的な SAML 構成]**  セクションの  **[識別子]** ボックスに貼り付けます。

    e.  **アイコン** をクリックして **[Reply URL (ACS)]\(応答 URL (ACS)\)** をコピーし、Azure portal で  **[基本的な SAML 構成]**  セクションの  **[応答 URL]** ボックスに貼り付けます。

    f. **[保存]** をクリックします。

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

### <a name="creating-a-peakon-test-user"></a>Peakon テスト ユーザーの作成

Azure AD ユーザーが Peakon にサインインできるようにするには、ユーザーを Peakon にプロビジョニングする必要があります。  
Peakon の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Peakon 企業サイトに管理者としてサインインします。

2. ページの左側にあるメニュー バーの  **[Configuration]\(構成\)** をクリックした後、 **[Employees]\(従業員\)** に移動します。

    ![従業員](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. ページの右上の  **[Add employee]\(従業員の追加\)** をクリックします。

      ![従業員の追加](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. **[New employee]\(新しい従業員\)** ダイアログ ページで、次の手順に従います。

     ![新しい従業員](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. **[Name]\(名前\)** ボックスに、姓として「**Britta**」を、名として「**simon**」を入力します。

    b. **[Email]\(メール\)** ボックスに、メール アドレス (**Brittasimon@contoso.com** など) を入力します。

    c. **[Create employee]\(従業員の作成\)** をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Peakon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Peakon]** を選択します。

    ![Configure single sign-on](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Peakon] タイルをクリックすると、自動的に Peakon アプリケーションにサインオンします。
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
