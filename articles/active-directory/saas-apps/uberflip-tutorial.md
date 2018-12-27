---
title: 'チュートリアル: Azure Active Directory と Uberflip の統合 | Microsoft Docs'
description: Azure Active Directory と Uberflip の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: jeedes
ms.openlocfilehash: bb1f53895fcd91a9474302fcf8c9e0040fe91961
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42920212"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>チュートリアル: Azure Active Directory と Uberflip の統合

このチュートリアルでは、Uberflip と Azure Active Directory (Azure AD) を統合する方法について説明します。

Uberflip と Azure AD の統合には、次の利点があります。

- Uberflip にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Uberflip にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Uberflip と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオンが有効な Uberflip のサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Uberflip の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-uberflip-from-the-gallery"></a>ギャラリーからの Uberflip の追加

Azure AD への Uberflip の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Uberflip を追加する必要があります。

**ギャラリーから Uberflip を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Uberflip**」と入力し、結果ウィンドウで **[Uberflip]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Uberflip](./media/uberflip-tutorial/tutorial_uberflip_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Uberflip で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Uberflip ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Uberflip の関連ユーザーの間で、リンク関係が確立されている必要があります。

Uberflip で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Uberflip のテスト ユーザーの作成](#create-an-uberflip-test-user)** - Uberflip で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Uberflip アプリケーションでシングル サインオンを構成します。

**Uberflip で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Uberflip** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/uberflip-tutorial/tutorial_uberflip_samlbase.png)

3. **[Uberflip のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Uberflip のドメインと URL] のシングル サインオン情報](./media/uberflip-tutorial/tutorial_uberflip_url1.png)

    **[応答 URL]** ボックスに、`https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Uberflip クライアント サポート チーム](mailto:support@uberflip.com)にお問い合わせください。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Uberflip のドメインと URL] のシングル サインオン情報](./media/uberflip-tutorial/tutorial_uberflip_url2.png)

    **[サインオン URL]** ボックスに、URL として「`https://app.uberflip.com/users/login`」を入力します。

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/uberflip-tutorial/tutorial_uberflip_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/uberflip-tutorial/tutorial_general_400.png)

7. **Uberflip** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [Uberflip サポート チーム](mailto:support@uberflip.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/uberflip-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/uberflip-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/uberflip-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/uberflip-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-an-uberflip-test-user"></a>Uberflip テスト ユーザーを作成する

このセクションの目的は、Uberflip で Britta Simon というユーザーを作成することです。 Uberflip では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Uberflip ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Uberflip サポート チーム](mailto:support@uberflip.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Uberflip へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Uberflip に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Uberflip]** を選択します。

    ![アプリケーションの一覧の [Uberflip] リンク](./media/uberflip-tutorial/tutorial_uberflip_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Uberflip のタイルをクリックすると、自動的に Uberflip アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/uberflip-tutorial/tutorial_general_01.png
[2]: ./media/uberflip-tutorial/tutorial_general_02.png
[3]: ./media/uberflip-tutorial/tutorial_general_03.png
[4]: ./media/uberflip-tutorial/tutorial_general_04.png

[100]: ./media/uberflip-tutorial/tutorial_general_100.png

[200]: ./media/uberflip-tutorial/tutorial_general_200.png
[201]: ./media/uberflip-tutorial/tutorial_general_201.png
[202]: ./media/uberflip-tutorial/tutorial_general_202.png
[203]: ./media/uberflip-tutorial/tutorial_general_203.png