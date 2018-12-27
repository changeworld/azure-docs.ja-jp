---
title: 'チュートリアル: Azure Active Directory と Wizergos Productivity Software の統合 | Microsoft Docs'
description: Azure Active Directory と Wizergos Productivity Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: jeedes
ms.openlocfilehash: 696d0326530baadfffc6f757c2a25690422a12c7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441546"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>チュートリアル: Azure Active Directory と Wizergos Productivity Software の統合

このチュートリアルでは、Wizergos Productivity Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

Wizergos Productivity Software と Azure AD の統合には、次の利点があります。

- Wizergos Productivity Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Wizergos Productivity Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Wizergos Productivity Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Wizergos Productivity Software でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Wizergos Productivity Software の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>ギャラリーからの Wizergos Productivity Software の追加
Azure AD への Wizergos Productivity Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Wizergos Productivity Software を追加する必要があります。

**ギャラリーから Wizergos Productivity Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Wizergos Productivity Software**」と入力し、結果ウィンドウで **Wizergos Productivity Software** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Wizergos Productivity Software](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Wizergos Productivity Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Wizergos Productivity Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Wizergos Productivity Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

Wizergos Productivity Software で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

Wizergos Productivity Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Wizergos Productivity Software のテスト ユーザーの作成](#create-a-wizergos-productivity-software-test-user)** - Wizergos Productivity Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Wizergos Productivity Software アプリケーションでシングル サインオンを構成します。

**Wizergos Productivity Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Wizergos Productivity Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_samlbase.png)

1. **[Wizergos Productivity Software のドメインと URL]** セクションで、次の手順を実行します。

    ![[Wizergos Productivity Software のドメインと URL] のシングル サインオン情報](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_url.png)

    **[識別子]** ボックスに次の URL を入力します。`http://www.wizergos.net`

1. **[SAML 署名証明書]** セクションで、**[証明書]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/wizergosproductivitysoftware-tutorial/tutorial_general_400.png)

1. **[Wizergos Productivity Software 構成]** セクションで **[Wizergos Productivity Software の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Wizergos Productivity Software の構成](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_configure.png) 

1. 別の Web ブラウザーのウィンドウで、管理者として Wizergos Productivity Software テナントにサインオンします。

1. ハンバーガー メニューから **[Admin (管理者)]** を選択します。

    ![アプリ側でのシングル サインオンの構成](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

1. [Admin (管理者)] ページの左側にあるメニューで **[AUTHENTICATION (認証)]** を選択し、**[Azure AD]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

1. **[AUTHENTICATION (認証)]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. **[UPLOAD (アップロード)]** ボタンをクリックして、Azure AD からダウンロードした証明書をアップロードします。
    
    b. **[発行者の URL]** テキストボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
    
    c. **[シングル サインオン URL]** テキストボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
    
    d. **[シングル サインアウト URL]** テキストボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。
    
    e. **[保存]** ボタンをクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/wizergosproductivitysoftware-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/wizergosproductivitysoftware-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-wizergos-productivity-software-test-user"></a>Wizergos Productivity Software テスト ユーザーの作成

このセクションでは、Wizergos Productivity Software で Britta Simon というユーザーを作成します。 [Wizergos Productivity Software サポート チーム](mailTo:support@wizergos.com)と連携して、Wizergos Productivity Software プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Wizergos Productivity Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Wizergos Productivity Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Wizergos Productivity Software]** を選択します。

    ![アプリケーションの一覧の Wizergos Productivity Software のリンク](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Wizergos Productivity Software] タイルをクリックすると、自動的に Wizergos Productivity Software アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[100]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[202]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_202.png
[203]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_203.png

