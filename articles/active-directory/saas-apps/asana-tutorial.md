---
title: 'チュートリアル: Azure Active Directory と Asana の統合 | Microsoft Docs'
description: Azure Active Directory と Asana の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: f5ea7a330891d4befeb6388bbe7f37b2a4aa848f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438210"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>チュートリアル: Azure Active Directory と Asana の統合

このチュートリアルでは、Asana と Azure Active Directory (Azure AD) を統合する方法について説明します。

Asana と Azure AD の統合には、次の利点があります。

- Asana にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Asana に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Asana と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Asana でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Asana の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-asana-from-the-gallery"></a>ギャラリーからの Asana の追加
Azure AD への Asana の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Asana を追加する必要があります。

**ギャラリーから Asana を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Asana**」と入力し、結果ウィンドウで **[Asana]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Asana で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Asana ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Asana の関連ユーザーの間で、リンク関係が確立されている必要があります。

Asana で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Asana で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Asana のテスト ユーザーの作成](#create-an-asana-test-user)** - Asana で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Asana アプリケーションでシングル サインオンを構成します。

**Asana で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Asana** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/asana-tutorial/tutorial_asana_samlbase.png)

1. **[Asana のドメインと URL]** セクションで、次の手順を実行します。

    ![[Asana のドメインと URL] のシングル サインオン情報](./media/asana-tutorial/tutorial_asana_url.png)

    a. **[サインオン URL]** ボックスに、URL として「`https://app.asana.com/`」と入力します。

    b. **[識別子]** ボックスに、値として「`https://app.asana.com/`」と入力します。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/asana-tutorial/tutorial_asana_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/asana-tutorial/tutorial_general_400.png)

1. **[Asana Configuration (Asana 構成)]** セクションで、**[Configure Asana (Asana を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Asana 構成](./media/asana-tutorial/tutorial_asana_configure.png)

1. 別のブラウザー ウィンドウで、Asana アプリケーションにサインオンします。 Asana で SSO を構成するために、画面の右上隅にあるワークスペース名をクリックして、ワークスペースの設定にアクセスします。 その後、**[\<your workspace name\> Settings (<ワークスペース名> 設定)]** をクリックします。

    ![Asana SSO 設定](./media/asana-tutorial/tutorial_asana_09.png)

1. **[Organization settings (組織の設定)]** ウィンドウで **[Administration (管理)]** をクリックします。 その後、 **[Members must log in via SAML (メンバーは SAML でログインする必要がある)]** をクリックして SSO 構成を有効にします。 次の手順に従います。

    ![[組織の設定] のシングル サインオン構成](./media/asana-tutorial/tutorial_asana_10.png)  

     a. **[Sign-in page URL (サインイン ページ URL)]** ボックスに、**SAML シングル サインオン サービス URL** を貼り付けます。

     b. Azure Portal からダウンロードした証明書を右クリックし、メモ帳またはお好きなテキスト エディターを使って証明書ファイルを開きます。 証明書タイトルの内容を最初から最後までコピーし、**[X.509 Certificate]** (X.509 証明書) ボックスに貼り付けます。

1. **[Save]** をクリックします。 さらにサポートが必要な場合は、 [SSO の設定に関する Asana ガイド](https://asana.com/guide/help/premium/authentication#gl-saml) をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/asana-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/asana-tutorial/create_aaduser_02.png)

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/asana-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![[追加] ボタン](./media/asana-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="create-an-asana-test-user"></a>Asana テスト ユーザーの作成

このセクションの目的は、Asana で Britta Shimon というユーザーを作成することです。 Asana では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](asana-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合、次の手順を実行します。**

このセクションでは、Asana で Britta Simon というユーザーを作成します。

1. **Asana** の左側のパネルで、**[Teams (チーム)]** セクションに移動します。 プラス記号ボタンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/asana-tutorial/tutorial_asana_12.png)

1. 電子メール アドレス「 britta.simon@contoso.com 」をテキスト ボックスに入力し、 **[Invite (招待)]** を選択します。

1. **[Send Invite (招待の送信)]** をクリックします。 新しいユーザーの電子メール アカウントに電子メールが届きます。 新しいユーザーは、アカウントを作成、確認する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Asana へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Asana に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[Asana]** を選択します。

    ![アプリケーションの一覧の Asana のリンク](./media/asana-tutorial/tutorial_asana_app.png)

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、Azure AD のシングル サインオンをテストすることです。

Asana のログイン ページに移動します。 電子メール アドレスのテキスト ボックスに、電子メール アドレスとして「britta.simon@contoso.com」と入力します。 パスワード ボックスは空白のままにして **[Log In (ログイン)]** をクリックします。 Azure AD ログイン ページにリダイレクトされます。 Azure AD 資格情報をすべて入力します。 これで、Asana へのログインが完了しました。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/asana-tutorial/tutorial_general_01.png
[2]: ./media/asana-tutorial/tutorial_general_02.png
[3]: ./media/asana-tutorial/tutorial_general_03.png
[4]: ./media/asana-tutorial/tutorial_general_04.png

[100]: ./media/asana-tutorial/tutorial_general_100.png

[200]: ./media/asana-tutorial/tutorial_general_200.png
[201]: ./media/asana-tutorial/tutorial_general_201.png
[202]: ./media/asana-tutorial/tutorial_general_202.png
[203]: ./media/asana-tutorial/tutorial_general_203.png
[10]: ./media/asana-tutorial/tutorial_general_060.png
[11]: ./media/asana-tutorial/tutorial_general_070.png