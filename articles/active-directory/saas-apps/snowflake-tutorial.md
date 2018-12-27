---
title: 'チュートリアル: Azure Active Directory と Snowflake の統合 | Microsoft Docs'
description: Azure Active Directory と Snowflake の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 6e1f76548e2433f9bc4b0b26b0894a92b7ec0aa0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092561"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>チュートリアル: Azure Active Directory と Snowflake の統合

このチュートリアルでは、Snowflake と Azure Active Directory (Azure AD) を統合する方法について説明します。

Snowflake と Azure AD の統合には、次の利点があります。

- Snowflake にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Snowflake にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Snowflake と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Snowflake でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Snowflake の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-snowflake-from-the-gallery"></a>ギャラリーからの Snowflake の追加

Azure AD への Snowflake の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Snowflake を追加する必要があります。

**ギャラリーから Snowflake を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Snowflake**」と入力し、結果パネルで **[Snowflake]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Snowflake](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Snowflake で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Snowflake ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Snowflake の関連ユーザーの間で、リンク関係が確立されている必要があります。

Snowflake で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Snowflake テスト ユーザーの作成](#creating-snowflake-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Snowflake で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Snowflake アプリケーションでシングル サインオンを構成します。

**Snowflake で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Snowflake** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[Snowflake のドメインと URL] のシングル サインオン情報](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    a. **[識別子]** ボックスに、`https://<SNOWFLAKE-URL>.snowflakecomputing.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login` のパターンを使用して URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Snowflake のドメインと URL] のシングル サインオン情報](./media/snowflake-tutorial/tutorial_snowflake_url2.png)

    **[サインオン URL]** ボックスに、`https://<SNOWFLAKE-URL>.snowflakecomputing.com` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。

6. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

7. **[Snowflake の設定]** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![Snowflake 構成](common/configuresection.png)

8. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Snowflake にログインします。

9. ページの右上で **[プロファイル]** をクリックして、**[Switch Role]\(ロールの切り替え\)** で **[ACCOUNTADMIN]** を選択します。

    > [!NOTE]
    > これは、右上の [ユーザー名] で選択したコンテキストとは別のものです。
    
    ![Snowflake 管理者 ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

10. **ダウンロードした Base 64 証明書**をメモ帳で開きます。 “-----BEGIN CERTIFICATE-----” と “-----END CERTIFICATE-----" の間の値をコピーし、下の **certificate** の横の引用符の間に貼り付けます。 **[ssoUrl]** に、Azure portal からコピーした**ログイン URL** の値を貼り付けます。 **[All Queries]\(すべてのクエリ\)** を選択し、**[実行]** をクリックします。

    ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

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

### <a name="creating-snowflake-test-user"></a>Snowflake テスト ユーザーの作成

Azure AD ユーザーが Snowflake にログインできるようにするには、そのユーザーを Snowflake にプロビジョニングする必要があります。 Snowflake では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Snowflake にログインします。

2. ページの右上で **[プロファイル]** をクリックして、**[Switch Role]\(ロールの切り替え\)** で **[ACCOUNTADMIN]** を選択します。  

    ![Snowflake 管理者 ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. 次に示すように、"Login name" がワークシートの Azure AD ユーザー名に設定されていることを確認して、次の SQL クエリを実行してユーザーを作成します。

    ![Snowflake adminsql ](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Snowflake へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Snowflake]** を選択します。

    ![Configure single sign-on](./media/snowflake-tutorial/tutorial_snowflake_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Snowflake のタイルをクリックすると、自動的に Snowflake アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。

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
