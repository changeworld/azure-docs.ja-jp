---
title: 'チュートリアル: Azure Active Directory と Dossier の統合 | Microsoft Docs'
description: Azure Active Directory と Dossier の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a5fec92-9c01-4ced-99b2-a10e28fc028e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeedes
ms.openlocfilehash: 932a832d4717a788f2d9adfd98ce1ba0c4ca07a1
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507513"
---
# <a name="tutorial-azure-active-directory-integration-with-dossier"></a>チュートリアル: Azure Active Directory と Dossier の統合

このチュートリアルでは、Dossier と Azure Active Directory (Azure AD) を統合する方法について説明します。

Dossier と Azure AD の統合には、次の利点があります。

- Dossier にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Dossier にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Dossier と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Dossier でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Dossier の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-dossier-from-the-gallery"></a>ギャラリーからの Dossier の追加

Azure AD への Dossier の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Dossier を追加する必要があります。

**ギャラリーから Dossier を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Dossier**」と入力し、結果パネルで **[Dossier]** を選び、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Dossier](./media/dossier-tutorial/tutorial_dossier_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Dossier で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Dossier ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Dossier の関連ユーザーの間で、リンク関係が確立されている必要があります。

Dossier で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Dossier のテスト ユーザーの作成](#create-a-dossier-test-user)** - Dossier で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Dossier アプリケーションでシングル サインオンを構成します。

**Dossier で Azure AD シングル サインオンを構成するには、次の手順を行います。**

1. Azure portal の **Dossier** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/dossier-tutorial/tutorial_dossier_samlbase.png)

3. **[Dossier Domain and URLs]\(Dossier のドメインと URL\)** セクションで、次の手順を行います。

    ![[Dossier のドメインと URL] のシングル サインオン情報](./media/dossier-tutorial/tutorial_dossier_url1.png)

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    
    | | |
    |-|-|
    | `https://<SUBDOMAIN>.dossiersystems.com/azuresso/account/SignIn`|
    | `https://dossier.<CLIENTDOMAINNAME>/azuresso/account/SignIn`|
    | |

    b. **[識別子]** ボックスに、`Dossier/<CLIENTNAME>` の形式で URL を入力します。

    > [!NOTE]
    > 識別子の値は、`Dossier/<CLIENTNAME>` の形式かユーザー個人用に設定された値である必要があります。

    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    | | |
    |-|-|
    |  `https://<SUBDOMAIN>.dossiersystems.com/azuresso`|
    | `https://dossier.<CLIENTDOMAINNAME>/azuresso`|
    | |

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得する場合は、[Dossier クライアント サポート チーム](mailto:support@intellimedia.ca)にお問い合わせください。

4. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/dossier-tutorial/tutorial_dossier_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/dossier-tutorial/tutorial_general_400.png)

7. **Dossier** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [Dossier サポート チーム](mailto:support@intellimedia.ca)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/dossier-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/dossier-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/dossier-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/dossier-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-dossier-test-user"></a>Dossier のテスト ユーザーの作成

このセクションでは、Dossier で Britta Simon というユーザーを作成します。 [Dossier サポート チーム](mailto:support@intellimedia.ca)と連携して、Dossier プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Dossier へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Dossier に Britta Simon を割り当てるには、次の手順を行います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Dossier]** を選択します。

    ![アプリケーションの一覧の [Dossier] リンク](./media/dossier-tutorial/tutorial_dossier_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Dossier のタイルをクリックすると、Dossier アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dossier-tutorial/tutorial_general_01.png
[2]: ./media/dossier-tutorial/tutorial_general_02.png
[3]: ./media/dossier-tutorial/tutorial_general_03.png
[4]: ./media/dossier-tutorial/tutorial_general_04.png

[100]: ./media/dossier-tutorial/tutorial_general_100.png

[200]: ./media/dossier-tutorial/tutorial_general_200.png
[201]: ./media/dossier-tutorial/tutorial_general_201.png
[202]: ./media/dossier-tutorial/tutorial_general_202.png
[203]: ./media/dossier-tutorial/tutorial_general_203.png

