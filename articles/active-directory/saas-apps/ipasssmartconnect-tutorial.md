---
title: 'チュートリアル: Azure Active Directory と iPass SmartConnect の統合 | Microsoft Docs'
description: Azure Active Directory と iPass SmartConnect の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: ecfdd3fae1d394e3b57fcd325f44cad0d1a98534
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444896"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>チュートリアル: Azure Active Directory と iPass SmartConnect の統合

このチュートリアルでは、iPass SmartConnect と Azure Active Directory (Azure AD) を統合する方法について説明します。

iPass SmartConnect と Azure AD の統合には、次の利点があります。

- iPass SmartConnect にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に iPass SmartConnect にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

iPass SmartConnect と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- iPass SmartConnect でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから iPass SmartConnect を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>ギャラリーから iPass SmartConnect を追加する
Azure AD への iPass SmartConnect の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iPass SmartConnect を追加する必要があります。

**ギャラリーから iPass SmartConnect を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**iPass SmartConnect**」と入力し、結果パネルで **[iPass SmartConnect]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、iPass SmartConnect で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する iPass SmartConnect ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと iPass SmartConnect の関連ユーザーの間で、リンク関係が確立されている必要があります。

iPass SmartConnect で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[iPass SmartConnect テスト ユーザーの作成](#create-an-ipass-smartconnect-test-user)** - iPass SmartConnect で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、iPass SmartConnect アプリケーションでシングル サインオンを構成します。

**iPass SmartConnect で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **iPass SmartConnect** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. **[iPass SmartConnect のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、手順を実行する必要はありません。

    ![[iPass SmartConnect のドメインと URL] のシングル サインオン情報](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. アプリケーションを **SP** 開始モードで構成する場合は、[詳細な URL 設定の表示] チェックボックスをオンにして次の手順を実行します。

    ![[iPass SmartConnect のドメインと URL] のシングル サインオン情報](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    [サインオン URL] テキストボックスに、URL として「`https://om-activation.ipass.com/ClientActivation/ssolanding.go`」を入力します。

1. iPass SmartConnect アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。

    ![Configure single sign-on](./media/ipasssmartconnect-tutorial/attribute.png)

1. **[ユーザー属性]** セクションの **[その他のすべてのユーザー属性を表示および編集する]** をクリックし、属性を展開します。 表示される各属性について、次の手順を実行します。

    | 属性名 | 属性値 | 名前空間の値|
    | ---------------| --------------- |----------------|
    | firstName | User.givenname |   |
    | lastName | User.surname | |
    | email | user.userprincipalname | |
    | username | user.userprincipalname | |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. その行の名前空間値は空白のままにしてください。

    e. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. **iPass SmartConnect** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** と**ドメイン名**を [iPass SmartConnect サポート チーム](mailto:help@ipass.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-an-ipass-smartconnect-test-user"></a>iPass SmartConnect テスト ユーザーを作成する

このセクションでは、iPass SmartConnect で Britta Simon というユーザーを作成します。 [iPass SmartConnect サポート チーム](mailto:help@ipass.com)と協力して、iPass SmartConnect プラットフォームでホワイトリストに追加する必要のあるユーザーまたはドメインを追加します。 ドメインがチームによって追加された場合、ユーザーは iPass SmartConnect プラットフォームに自動的にプロビジョニングされます。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に iPass SmartConnect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**iPass SmartConnect に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[iPass SmartConnect]** を選択します。

    ![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

**SP によって開始されたフローでアプリケーションをテストするには、次の手順を実行します。**

a. Windows の iPass SmartConnect クライアントを[こちら](https://om-activation.ipass.com/ClientActivation/ssolanding.go)からダウンロードします。

![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/testing3.png)

b. クライアントをインストールし、起動します。

c. **[開始]** をクリックします。

![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Azure ユーザー名とドメインを入力します。 **[続行]** をクリックします。 これは Azure ログイン ページにリダイレクトされます。

![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/testing2.png) 

e. 認証に成功すると、クライアントのアクティブ化が開始されます。 クライアントがアクティブになります。

**IdP によって開始されたフローでアプリケーションをテストするには、次の手順を実行します。**

a. [https://myapps.microsoft.com](https://myapps.microsoft.com) にログインします。

b. iPass SmartConnect アプリをクリックします。

c. SSA ページが表示されます。**[Download App for Windows]\(Windows 向けアプリをダウンロードする\)** をクリックし、iPass SmartConnect クライアントをインストールします。

![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/testing4.png)

d. インストール後、最初に起動したクライアントは、利用規約に同意するとアクティブ化を自動的に開始します。

e. アクティブ化が開始しない場合、SSA ページの [アクティブ化] ボタンをクリックしてアクティブ化を開始してください。

f. クライアントがアクティブになります。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

