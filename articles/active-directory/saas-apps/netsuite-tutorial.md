---
title: 'チュートリアル: Azure Active Directory と Netsuite の統合 | Microsoft Docs'
description: Azure Active Directory と Netsuite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2018
ms.author: jeedes
ms.openlocfilehash: 55ee7f6b496def5669160f5cfafed7bc6d7eab11
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219372"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>チュートリアル: Azure Active Directory と Netsuite の統合

このチュートリアルでは、Netsuite と Azure Active Directory (Azure AD) を統合する方法について説明します。

Netsuite と Azure AD の統合には、次の利点があります。

- Netsuite にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Netsuite にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Netsuite の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Netsuite でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Netsuite の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-netsuite-from-the-gallery"></a>ギャラリーからの Netsuite の追加
Azure AD への Netsuite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Netsuite を追加する必要があります。

**ギャラリーから Netsuite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
3. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[アプリケーション]][3]

4. 検索ボックスに、「**Netsuite**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/netsuite-tutorial/tutorial_netsuite_search.png)

5. 結果ウィンドウで **Netsuite** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Netsuite で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Netsuite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Netsuite の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Netsuite の **[Username]\(ユーザー名\)** の値として割り当てます。

Netsuite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Netsuite テスト ユーザーの作成](#creating-a-netsuite-test-user)** - Netsuite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Netsuite アプリケーションでシングル サインオンを構成します。

**Netsuite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Netsuite** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[Configure Single Sign-On]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[Configure Single Sign-On]](./media/netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. **[Netsuite のドメインと URL]** セクションで、次の手順に従います。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/tutorial_netsuite_url.png)

    **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。 `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の応答 URL で値を更新します。 これらの値を取得するには、[Netsuite サポート チーム](http://www.netsuite.com/portal/services/support.shtml)に連絡してください。
 
4. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/tutorial_general_400.png)

6. **[Netsuite 構成]** セクションで、**[Netsuite の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/tutorial_netsuite_configure.png) 

7. ブラウザーで新しいタブを開き、Netsuite の会社のサイトに管理者としてサインインします。

8. ページの上部にあるツール バーで、**[Setup]**、**[Setup Manager]** の順にクリックします。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-setup.png)

9. **[Setup Tasks]** 一覧で、**[Integration]** を選択します。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-integration.png)

10. **[Manage Authentication]** セクションで、**[SAML Single Sign-on]** をクリックします。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-saml.png)

11. **[SAML Setup]** ページで、次の手順を実行します。
   
    a. **[サインオンの構成]** の **[クイック リファレンス]** セクションから **SAML シングル サインオン サービス URL** の値をコピーし、Netsuite の **[Identity Provider Login Page]\(ID プロバイダー ログイン ページ\)** フィールドに貼り付けます。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-saml-setup.png)
  
    b. Netsuite で、**[Primary Authentication Method]** を選択します。

    c. **[SAMLV2 Identity Provider Metadata]** フィールドで、**[Upload IDP Metadata File]** を選択します。 **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-sso-setup.png)

    d. **[送信]** をクリックします。

12. Azure AD で **[その他のすべてのユーザー属性を表示および編集する]** チェック ボックスをオンにして、属性を追加します。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-attributes.png)

13. **[属性名]** フィールドに、「`account`」と入力します。 **[属性値]** フィールドに、Netsuite アカウント ID を入力します。 この値は定数であり、アカウントによって異なります。 アカウント ID を確認する方法を次に示します。

      ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-add-attribute.png)

    a. Netsuite で、上部のナビゲーション メニューから **[Setup]\(セットアップ\)** をクリックします。

    b. 左側のナビゲーション メニューの **[Setup Tasks]\(セットアップ タスク\)** セクションで、**[Integration]\(統合\)** セクションを選択し、**[Web Services Preferences]\(Web サービスの設定\)** をクリックします。

    c. Netsuite アカウント ID をコピーし、Azure AD の **[属性値]** フィールドに貼り付けます。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-account-id.png)

14. ユーザーは Netsuite にシングル サインオンする前に、まず、Netsuite で適切なアクセス許可が割り当てられている必要があります。 次の手順に従って、アクセス許可を割り当てます。

    a. 上部のナビゲーション メニューで、**[Setup]**、**[Setup Manager]** の順にクリックします。
      
      ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-setup.png)

    b. 左側のナビゲーション メニューで、**[Users/Roles]**、**[Manage Roles]** の順にクリックします。
      
      ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-manage-roles.png)

    c. **[New Role]** をクリックします。

    d. 新しいロールの**名前**を入力します:
      
      ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-new-role.png)

    e. **[Save]** をクリックします。

    f. 上部のメニューで、 **[Permissions]** をクリックします。 **[Setup]** をクリックします。
      
       ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-sso.png)

    g. **[Set Up SAML Single Sign-on]**、**[Add]** の順にクリックします。

    h. **[Save]** をクリックします。

    i. 上部のナビゲーション メニューで、**[Setup]**、**[Setup Manager]** の順にクリックします。
      
       ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-setup.png)

    j. 左側のナビゲーション メニューで、**[Users/Roles]**、**[Manage Users]** の順にクリックします。
      
       ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-manage-users.png)

    k. テスト ユーザーを選択します。 **[Edit]** をクリックします。
      
       ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-edit-user.png)

    l. [Roles] ダイアログ ボックスで、作成したロールを選択し、 **[Add]** をクリックします。
      
       ![[Configure Single Sign-On]](./media/netsuite-tutorial/ns-add-role.png)

    m. **[Save]** をクリックします。
    
### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/netsuite-tutorial/create_aaduser_01.png) 

2.  **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/netsuite-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/netsuite-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/netsuite-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。 

### <a name="creating-a-netsuite-test-user"></a>Netsuite テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Netsuite に作成します。 Netsuite では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。
このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Netsuite に存在しない場合は、Netsuite にアクセスしようとしたときに新しいユーザーが作成されます。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Netsuite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Netsuite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Netsuite]** を選択します。

    ![[Configure Single Sign-On]](./media/netsuite-tutorial/tutorial_netsuite_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

シングル サインオンの設定をテストするために、アクセス パネル ([https://myapps.microsoft.com](https://myapps.microsoft.com/)) を開き、テスト アカウントにサインインし、**[Netsuite]** をクリックします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/netsuite-tutorial/tutorial_general_01.png
[2]: ./media/netsuite-tutorial/tutorial_general_02.png
[3]: ./media/netsuite-tutorial/tutorial_general_03.png
[4]: ./media/netsuite-tutorial/tutorial_general_04.png

[100]: ./media/netsuite-tutorial/tutorial_general_100.png

[200]: ./media/netsuite-tutorial/tutorial_general_200.png
[201]: ./media/netsuite-tutorial/tutorial_general_201.png
[202]: ./media/netsuite-tutorial/tutorial_general_202.png
[203]: ./media/netsuite-tutorial/tutorial_general_203.png

