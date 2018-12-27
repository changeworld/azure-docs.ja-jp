---
title: 'チュートリアル: Azure Active Directory と PlanGrid の統合 | Microsoft Docs'
description: Azure Active Directory と PlanGrid の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ba72432-9b49-4358-b756-14c982422be8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: b2225a48e78e8c609223510a32d3ed5c735ed3b6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046065"
---
# <a name="tutorial-azure-active-directory-integration-with-plangrid"></a>チュートリアル: Azure Active Directory と PlanGrid の統合

このチュートリアルでは、PlanGrid と Azure Active Directory (Azure AD) を統合する方法について説明します。

PlanGrid と Azure AD の統合には、次の利点があります。

- PlanGrid にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に PlanGrid にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

PlanGrid と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- PlanGrid のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの PlanGrid の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-plangrid-from-the-gallery"></a>ギャラリーからの PlanGrid の追加
Azure AD への PlanGrid の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PlanGrid を追加する必要があります。

**ギャラリーから PlanGrid を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**PlanGrid**」と入力し、結果パネルで **[PlanGrid]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の PlanGrid](./media/plangrid-tutorial/tutorial_plangrid_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、PlanGrid で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する PlanGrid ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと PlanGrid の関連ユーザーの間で、リンク関係が確立されている必要があります。

PlanGrid で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[PlanGrid のテスト ユーザーの作成](#create-a-plangrid-test-user)** - PlanGrid で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、PlanGrid アプリケーションでシングル サインオンを構成します。

**PlanGrid で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **PlanGrid** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/plangrid-tutorial/tutorial_plangrid_samlbase.png)

3. **[PlanGrid のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![[PlanGrid のドメインと URL] のシングル サインオン情報](./media/plangrid-tutorial/tutorial_plangrid_url1.png)

    **[識別子 (エンティティ ID)]** ボックスに `https://io.plangrid.com/sessions/saml/metadata` という URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[PlanGrid のドメインと URL] のシングル サインオン情報](./media/plangrid-tutorial/tutorial_plangrid_url2.png)

    **[サインオン URL]** ボックスに、URL として「`https://app.plangrid.com/login`」を入力します。

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/plangrid-tutorial/tutorial_plangrid_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/plangrid-tutorial/tutorial_general_400.png)
    
7. **PlanGrid** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [PlanGrid サポート チーム](mailto:help@plangrid.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/plangrid-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/plangrid-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/plangrid-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/plangrid-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-plangrid-test-user"></a>PlanGrid テスト ユーザーの作成

このセクションでは、PlanGrid で Britta Simon というユーザーを作成します。 [PlanGrid サポート チーム](mailto:help@plangrid.com)と連携し、PlanGrid プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に PlanGrid へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**PlanGrid に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[PlanGrid]** を選択します。

    ![アプリケーションの一覧の PlanGrid のリンク](./media/plangrid-tutorial/tutorial_plangrid_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [PlanGrid] タイルをクリックすると、PlanGrid アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/plangrid-tutorial/tutorial_general_01.png
[2]: ./media/plangrid-tutorial/tutorial_general_02.png
[3]: ./media/plangrid-tutorial/tutorial_general_03.png
[4]: ./media/plangrid-tutorial/tutorial_general_04.png

[100]: ./media/plangrid-tutorial/tutorial_general_100.png

[200]: ./media/plangrid-tutorial/tutorial_general_200.png
[201]: ./media/plangrid-tutorial/tutorial_general_201.png
[202]: ./media/plangrid-tutorial/tutorial_general_202.png
[203]: ./media/plangrid-tutorial/tutorial_general_203.png

