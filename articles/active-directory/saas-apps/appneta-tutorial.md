---
title: 'チュートリアル: Azure Active Directory と AppNeta Performance Monitor の統合 | Microsoft Docs'
description: Azure Active Directory と AppNeta Performance Monitor の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: ccedc0288e313df2639862a14078d8cad9951286
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054551"
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>チュートリアル: Azure Active Directory と AppNeta Performance Monitor の統合

このチュートリアルでは、AppNeta Performance Monitor と Azure Active Directory (Azure AD) を統合する方法について説明します。

AppNeta Performance Monitor と Azure AD の統合には、次の利点があります。

- AppNeta Performance Monitor にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に AppNeta Performance Monitor にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と AppNeta Performance Monitor の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- AppNeta Performance Monitor でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの AppNeta Performance Monitor の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>ギャラリーからの AppNeta Performance Monitor の追加
Azure AD への AppNeta Performance Monitor の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AppNeta Performance Monitor を追加する必要があります。

**ギャラリーから AppNeta Performance Monitor を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. [検索] ボックスに「**AppNeta Performance Monitor**」と入力し、結果パネルから **[AppNeta Performance Monitor]** を選択して、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の AppNeta Performance Monitor](./media/appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、AppNeta Performance Monitor で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する AppNeta Performance Monitor ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと AppNeta Performance Monitor の関連ユーザーの間で、リンク関係が確立されている必要があります。

AppNeta Performance Monitor で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[AppNeta Performance Monitor のテスト ユーザーの作成](#create-an-appneta-performance-monitor-test-user)** - AppNeta Performance Monitor で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、AppNeta Performance Monitor アプリケーションでシングル サインオンを構成します。

**AppNeta Performance Monitor で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **AppNeta Performance Monitor** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/appneta-tutorial/tutorial_appneta_samlbase.png)

3. **AppNeta Performance Monitor and URLs(AppNeta Performance Monitor のドメインと URL)** セクションで、次の手順を実行します。

    ![AppNeta Performance Monitor and URLs(AppNeta Performance Monitor のドメインと URL) のシングル サインオン情報](./media/appneta-tutorial/tutorial_appneta_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.pm.appneta.com` のパターンを使用して URL を入力します。

    b. **[識別子]** テキストボックスに、値として「`PingConnect`」を入力します。

    > [!NOTE] 
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 [AppNeta Performance Monitor のクライアント サポート チーム](mailto:support@appneta.com)に問い合わせて、この値を入手します。 

5. AppNeta Performance Monitor アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、SAML トークン属性の構成に、カスタム属性マッピングを追加する必要があります。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。

    ![Configure single sign-on](./media/appneta-tutorial/attribute.png)

6. **[Single sign-on]\(シングル サインオン\)** ダイアログの **[User Attributes]\(ユーザー属性\)** セクションで、上記の図に示すように SAML トークン属性を構成し、次の手順を実行します。
           
    | 属性名 | 属性値 |
    | ---------------| ----------------|
    | firstName| User.givenname|
    | lastName| User.surname|
    | email| user.userprincipalname|
    | name| user.userprincipalname|
    | groups   | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user.jobtitle|

    > [!NOTE]
    > "groups"は、Azure AD で 'Role' にマップされている Appneta のセキュリティ グループを指しています。 Azure AD でカスタム ロールを作成する方法を説明している[この](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)ドキュメントを参照してください。
        
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/appneta-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/appneta-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. 名前空間は空白のままにします。
    
    e. **[OK]** をクリックします。  

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/appneta-tutorial/tutorial_appneta_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/appneta-tutorial/tutorial_general_400.png)

6. **AppNeta Performance Monitor** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [AppNeta Performance Monitor サポート チーム](mailto:support@appneta.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/appneta-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/appneta-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/appneta-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/appneta-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>AppNeta Performance Monitor のテスト ユーザーの作成

このセクションの目的は、AppNeta Performance Monitor で Britta Simon というユーザーを作成することです。 AppNeta Performance Monitor では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 AppNeta Performance Monitor にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、[AppNeta Performance Monitor サポート チーム](mailto:support@appneta.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、AppNeta Performance Monitor へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**AppNeta Performance Monitor に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[AppNeta Performance Monitor]** を選択します。

    ![アプリケーションの一覧の [AppNeta Performance Monitor] リンク](./media/appneta-tutorial/tutorial_appneta_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで AppNeta Performance Monitor のタイルをクリックすると、AppNeta Performance Monitor アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appneta-tutorial/tutorial_general_01.png
[2]: ./media/appneta-tutorial/tutorial_general_02.png
[3]: ./media/appneta-tutorial/tutorial_general_03.png
[4]: ./media/appneta-tutorial/tutorial_general_04.png

[100]: ./media/appneta-tutorial/tutorial_general_100.png

[200]: ./media/appneta-tutorial/tutorial_general_200.png
[201]: ./media/appneta-tutorial/tutorial_general_201.png
[202]: ./media/appneta-tutorial/tutorial_general_202.png
[203]: ./media/appneta-tutorial/tutorial_general_203.png

