---
title: 'チュートリアル: Azure Active Directory と FiscalNote の統合 | Microsoft Docs'
description: Azure Active Directory と FiscalNote の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 2dfc450fe53c543c1d5119cd9c6954aadaa3b3ff
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421425"
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>チュートリアル: Azure Active Directory と FiscalNote の統合

このチュートリアルでは、FiscalNote と Azure Active Directory (Azure AD) を統合する方法について説明します。

FiscalNote と Azure AD の統合には、次の利点があります。

- FiscalNote にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に FiscalNote にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

FiscalNote と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- FiscalNote シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの FiscalNote の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-fiscalnote-from-the-gallery"></a>ギャラリーからの FiscalNote の追加
Azure AD への FiscalNote の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FiscalNote を追加する必要があります。

**ギャラリーから FiscalNote を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**FiscalNote**」と入力し、結果ウィンドウで **[FiscalNote]** を選び、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リストの FiscalNote](./media/fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、FiscalNote で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する FiscalNote ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと FiscalNote の関連ユーザーの間で、リンク関係が確立されている必要があります。

FiscalNote で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[FiscalNote テスト ユーザーの作成](#create-a-fiscalnote-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを FiscalNote で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、FiscalNote アプリケーションでシングル サインオンを構成します。

**FiscalNote で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **FiscalNote** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

1. **[FiscalNote のドメインと URL]** セクションで、次の手順を実行します。

    ![[FiscalNote のドメインと URL] のシングル サインオン情報](./media/fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. **[サインオン URL]** ボックスに、`https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`urn:auth0:fiscalnote:<CONNECTIONNAME>` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[FiscalNote クライアント サポート チーム](mailto:support@fiscalnote.com)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、**[Certificate(Raw)] \(証明書 (Raw))** をクリックし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

1. FiscalNote アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。

    ![Configure single sign-on](./media/fiscalnote-tutorial/tutorial_attribute.png)

1. **[Single sign-on]\(シングル サインオン\)** ダイアログの **[User Attributes]\(ユーザー属性\)** セクションで、上記の図に示すように SAML トークン属性を構成し、次の手順を実行します。
           
    | 属性名 | 属性値 |
    | ---------------| ----------------|
    | name | user.userprincipalname|
    | givenName| User.givenname|
    | familyName| User.surname|
    | email| User.mail|
    
    a. 既存の属性を削除し、新しい属性を追加します。 **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/fiscalnote-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. 名前空間は空白のままにします。
    
    e. **[OK]** をクリックします。

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/fiscalnote-tutorial/tutorial_general_400.png)

1. **[FiscalNote Configuration]\(FiscalNote 構成\)** セクションで、**[Configure FiscalNote]\(FiscalNote を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![FiscalNote 構成](./media/fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

1. **FiscalNote** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (未加工)、サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を [FiscalNote サポート チーム](mailto:support@fiscalnote.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/fiscalnote-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/fiscalnote-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/fiscalnote-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/fiscalnote-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-fiscalnote-test-user"></a>FiscalNote テスト ユーザーの作成

このセクションの目的は、FiscalNote で Britta Simon というユーザーを作成することです。 FiscalNote では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 まだ存在しない FiscalNote ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、[FiscalNote サポート チーム](mailto:support@fiscalnote.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に FiscalNote へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**FiscalNote に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[FiscalNote]** を選択します。

    ![アプリケーションの一覧の FiscalNote のリンク](./media/fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [FiscalNote] タイルをクリックすると、自動的に FiscalNote アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/fiscalnote-tutorial/tutorial_general_203.png

