---
title: 'チュートリアル: Azure Active Directory と Proxyclick の統合 | Microsoft Docs'
description: Azure Active Directory と Proxyclick の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.openlocfilehash: d93c5486d9c23558995742fc27e1222834cf4452
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446315"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>チュートリアル: Azure Active Directory と Proxyclick の統合

このチュートリアルでは、Proxyclick と Azure Active Directory (Azure AD) を統合する方法について説明します。

Proxyclick と Azure AD の統合には、次の利点があります。

- Proxyclick にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Proxyclick に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Proxyclick と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Proxyclick でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Proxyclick の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-proxyclick-from-the-gallery"></a>ギャラリーからの Proxyclick の追加
Azure AD への Proxyclick の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Proxyclick を追加する必要があります。

**ギャラリーから Proxyclick を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Proxyclick**」と入力し、結果パネルで **Proxyclick** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Proxyclick で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Proxyclick ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Proxyclick の関連ユーザーの間で、リンク関係が確立されている必要があります。

Proxyclick で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Proxyclick テスト ユーザーの作成](#create-a-proxyclick-test-user)** - Proxyclick で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Proxyclick アプリケーションでシングル サインオンを構成します。

**Proxyclick で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Proxyclick** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

1. **[Proxyclick のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Proxyclick のドメインと URL] のシングル サインオン情報](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. **[識別子]** ボックスに、`https://saml.proxyclick.com/init/<companyId>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://saml.proxyclick.com/consume/<companyId>` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Proxyclick のドメインと URL] のシングル サインオン情報](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    **[サインオン URL]** ボックスに、`https://saml.proxyclick.com/init/<companyId>` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL に値を置き換えます。実際の値については後で説明します。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/proxyclick-tutorial/tutorial_general_400.png)

1. **[Proxyclick 構成]** セクションで、**[Proxyclick の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Proxyclick 構成](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

1. 別の Web ブラウザー ウィンドウで、Proxyclick 企業サイトに管理者としてログインします。

1. **[Account & Settings]\(アカウントと設定\)** を選択します。

    ![Proxyclick 構成](./media/proxyclick-tutorial/configure1.png)

1. **[INTEGRATIONS]\(統合\)** まで下へスクロールし、**[SAML]** を選択します。

    ![Proxyclick 構成](./media/proxyclick-tutorial/configure2.png)

1. **[SAML]** セクションで、次の手順に従います。

    ![Proxyclick 構成](./media/proxyclick-tutorial/configure3.png)

    a. **[SAML Consumer URL]\(SAML コンシューマー URL\)** の値をコピーし、Azure portal の **[Proxyclick のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。

    b. **[SAML SSO Redirect URL]\(SAML SSO リダイレクト URL\)** の値をコピーし、Azure portal の **[Proxyclick のドメインと URL]** セクションの **[サインオン URL]** ボックスと **[識別子]** ボックスに貼り付けます。

    c. **[SAML Request Method]\(SAML 要求メソッド\)** で **[HTTP Redirect]\(HTTP リダイレクト\)** を選択します。

    d. **[Issuer]\(発行者\)** ボックスに、Azure portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    e. **[SAML 2.0 Endpoint URL]\(SAML 2.0 エンドポイント URL\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    f. Azure portal からダウンロードした証明書ファイルをメモ帳で開き、**[Certificate]\(証明書\)** ボックスに貼り付けます。

    g. **[変更を保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/proxyclick-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/proxyclick-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/proxyclick-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-proxyclick-test-user"></a>Proxyclick テスト ユーザーの作成

Azure AD ユーザーが Proxyclick にログインできるようにするには、ユーザーを Proxyclick にプロビジョニングする必要があります。 Proxyclick の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Proxyclick 企業サイトに管理者としてログインします。

1. 上部のナビゲーション バーから **[Colleagues]\(仕事仲間\)** をクリックします。

    ![従業員の追加](./media/proxyclick-tutorial/user1.png)

1. **[Add Colleague]\(仕事仲間の追加\)** をクリックします

    ![従業員の追加](./media/proxyclick-tutorial/user2.png)

1. **[Add a colleague]\(仕事仲間の追加\)** セクションで、次の手順に従います。

    ![従業員の追加](./media/proxyclick-tutorial/user3.png)

    a. **[メール]** ボックスに、ユーザーのメール アドレス (**brittasimon@contoso.com** など) を入力します。

    b. **[名]** ボックスに、ユーザーの名を入力します (この例では Britta)。

    c. **[姓]** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    d. **[ユーザーの追加]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Proxyclick へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Proxyclick に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Proxyclick]** を選択します。

    ![アプリケーションの一覧の Proxyclick のリンク](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Proxyclick] タイルをクリックすると、自動的に Proxyclick アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

