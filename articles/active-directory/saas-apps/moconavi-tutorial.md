---
title: 'チュートリアル: Azure Active Directory と moconavi の統合 | Microsoft Docs'
description: Azure Active Directory と moconavi の間のシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.openlocfilehash: 3467b823e6c91d34ebd48c7f8bc29558a79c59e5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229548"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>チュートリアル: Azure Active Directory と moconavi の統合

このチュートリアルでは、moconavi と Azure Active Directory (Azure AD) を統合する方法について説明します。

moconavi と Azure AD の統合には、次の利点があります。

- moconavi にアクセスできる ユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に moconavi にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

moconavi と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- moconavi でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの moconavi の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-moconavi-from-the-gallery"></a>ギャラリーからの moconavi の追加
Azure AD への moconavi の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に moconavi を追加する必要があります。

**ギャラリーから moconavi を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**moconavi**」と入力し、結果パネルで **[moconavi]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの moconavi](./media/moconavi-tutorial/tutorial_moconavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、moconavi で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する moconavi ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと moconavi の関連ユーザーの間で、リンク関係が確立されている必要があります。

moconavi で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[moconavi テスト ユーザーの作成](#create-a-moconavi-test-user)** - moconavi で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、moconavi アプリケーションでシングル サインオンを構成します。

**moconavi で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure portal の **moconavi** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/moconavi-tutorial/tutorial_moconavi_samlbase.png)

3. **[moconavi のドメインと URL]** セクションで、次の手順を実行します。

    ![[moconavi のドメインと URL] のシングル サインオン情報](./media/moconavi-tutorial/tutorial_moconavi_url.png)

    a. **[サインオン URL]** ボックスに、`https://<yourserverurl>/moconavi-saml2/saml/login` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<yourserverurl>/moconavi-saml2` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<yourserverurl>/moconavi-saml2/saml/SSO` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[moconavi クライアント サポート チーム](mailto:support@recomot.co.jp)に連絡してください。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/moconavi-tutorial/tutorial_moconavi_certificate.png)

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/moconavi-tutorial/tutorial_general_400.png)

6. **moconavi** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [moconavi サポート チーム](mailto:support@recomot.co.jp)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/moconavi-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/moconavi-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/moconavi-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/moconavi-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-moconavi-test-user"></a>moconavi テスト ユーザーの作成

このセクションでは、moconavi で Britta Simon というユーザーを作成します。 [moconavi サポート チーム](mailto:support@recomot.co.jp)と連携して、moconavi プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に moconavi へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**moconavi に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[moconavi]** を選択します。

    ![アプリケーションの一覧の [moconavi] リンク](./media/moconavi-tutorial/tutorial_moconavi_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

1. Microsoft Store から moconavi をインストールします。

2. moconavi を起動します。

3. **[Connect setting]\(接続設定\)** をクリックします。

    ![シングル サインオンのテスト](./media/moconavi-tutorial/testing1.png)

4. **[Connect to URL]\(URL への接続\)** ボックスに「`https://mcs-admin.moconavi.biz/gateway`」と入力し、**[Done]\(完了\)** をクリックします。

    ![シングル サインオンのテスト](./media/moconavi-tutorial/testing2.png)

5. 次のスクリーンショットで、次の手順を実行します。

    ![シングル サインオンのテスト](./media/moconavi-tutorial/testing3.png)

    a. **[Input Authentication Key]\(認証キーを入力\)**: **[Input Authentication Key]\(認証キーを入力\)** ボックスに「`azureAD`」と入力します。

    b. **[Input User ID]\(ユーザー ID を入力\)**: **[Input User ID]\(ユーザー ID を入力\)** ボックスに `your ad account` を入力します。

    c. **[LOGIN]\(ログイン\)** をクリックします。

6. **[Password]\(パスワード\)** ボックスに Azure AD のパスワードを入力し、**[Login]\(ログイン\)** をクリックします。

    ![シングル サインオンのテスト](./media/moconavi-tutorial/testing4.png)

7. メニューが表示されたら、Azure AD 認証は成功です。

    ![シングル サインオンのテスト](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/moconavi-tutorial/tutorial_general_01.png
[2]: ./media/moconavi-tutorial/tutorial_general_02.png
[3]: ./media/moconavi-tutorial/tutorial_general_03.png
[4]: ./media/moconavi-tutorial/tutorial_general_04.png

[100]: ./media/moconavi-tutorial/tutorial_general_100.png

[200]: ./media/moconavi-tutorial/tutorial_general_200.png
[201]: ./media/moconavi-tutorial/tutorial_general_201.png
[202]: ./media/moconavi-tutorial/tutorial_general_202.png
[203]: ./media/moconavi-tutorial/tutorial_general_203.png

