---
title: 'チュートリアル: Azure Active Directory と SciQuest Spend Director の統合 | Microsoft Docs'
description: Azure Active Directory と SciQuest Spend Director の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 0ddc8a42f4e0454061fa645b8c5d465e9e8dd9bf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445171"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>チュートリアル: Azure Active Directory と SciQuest Spend Director の統合

このチュートリアルでは、SciQuest Spend Director と Azure Active Directory (Azure AD) を統合する方法について説明します。

SciQuest Spend Director と Azure AD の統合には、次の利点があります。

- SciQuest Spend Director にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SciQuest Spend Director にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SciQuest Spend Director と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SciQuest Spend Director でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SciQuest Spend Director の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>ギャラリーからの SciQuest Spend Director の追加
Azure AD への SciQuest Spend Director の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SciQuest Spend Director を追加する必要があります。

**ギャラリーから SciQuest Spend Director を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**SciQuest Spend Director**」と入力し、結果パネルで **[SciQuest Spend Director]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの SciQuest Spend Director](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SciQuest Spend Director で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SciQuest Spend Director ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SciQuest Spend Director の関連ユーザーの間で、リンクの関係が確立されている必要があります。

SciQuest Spend Director で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

SciQuest Spend Director で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[SciQuest Spend Director のテスト ユーザーの作成](#create-a-sciquest-spend-director-test-user)** - SciQuest Spend Director で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SciQuest Spend Director アプリケーションでシングル サインオンを構成します。

**SciQuest Spend Director で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SciQuest Spend Director** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_samlbase.png)

1. **[SciQuest Spend Director のドメインと URL]** セクションで、次の手順を実行します。

    ![[SciQuest Spend Director のドメインと URL] のシングル サインオン情報](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.sciquest.com` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[SciQuest Spend Director クライアント サポート チーム](https://www.jaggaer.com/contact-us/)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/sciquest-spend-director-tutorial/tutorial_general_400.png)

1. **SciQuest Spend Director** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [SciQuest Spend Director サポート チーム](https://www.jaggaer.com/contact-us/)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/sciquest-spend-director-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/sciquest-spend-director-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/sciquest-spend-director-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/sciquest-spend-director-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-sciquest-spend-director-test-user"></a>SciQuest Spend Director のテスト ユーザーの作成

このセクションの目的は、SciQuest Spend Director で Britta Simon というユーザーを作成することです。

テスト アカウントを作成するには、[SciQuest Spend Director のサポート チーム](https://www.jaggaer.com/contact-us/)に連絡し、テスト アカウントの詳細を伝える必要があります。

または、SciQuest Spend Director でサポートされているシングル サインオン機能であるジャストインタイム プロビジョニングを使用することもできます。  
ジャストインタイム プロビジョニングが有効な場合、ユーザーが存在しなければ、シングル サインオンの試行中に SciQuest Spend Director によりユーザーが自動的に作成されます。 この機能により、対応するシングル サインオン ユーザーを手動で作成する必要がなくなります。

ジャストインタイム プロビジョニングを有効にするには、[SciQuest Spend Director のサポート チーム](https://www.jaggaer.com/contact-us/)に連絡する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SciQuest Spend Director へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SciQuest Spend Director に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SciQuest Spend Director]** を選択します。

    ![アプリケーションの一覧の SciQuest Spend Director のリンク](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで SciQuest Spend Director のタイルをクリックすると、自動的に SciQuest Spend Director アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/sciquest-spend-director-tutorial/tutorial_general_04.png

[100]: ./media/sciquest-spend-director-tutorial/tutorial_general_100.png

[200]: ./media/sciquest-spend-director-tutorial/tutorial_general_200.png
[201]: ./media/sciquest-spend-director-tutorial/tutorial_general_201.png
[202]: ./media/sciquest-spend-director-tutorial/tutorial_general_202.png
[203]: ./media/sciquest-spend-director-tutorial/tutorial_general_203.png

