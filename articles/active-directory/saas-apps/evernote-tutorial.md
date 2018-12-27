---
title: 'チュートリアル: Azure Active Directory と Evernote の統合 | Microsoft Docs'
description: Azure Active Directory と Evernote の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 7a9282f5418737b583e29d99893df3fc81f52955
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442634"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>チュートリアル: Azure Active Directory と Evernote の統合

このチュートリアルでは、Evernote と Azure Active Directory (Azure AD) を統合する方法について説明します。

Evernote と Azure AD の統合には、次の利点があります。

- Evernote にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Evernote にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Evernote の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Evernote でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Evernote の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-evernote-from-the-gallery"></a>ギャラリーからの Evernote の追加
Azure AD への Evernote の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Evernote を追加する必要があります。

**ギャラリーから Evernote を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Evernote**」と入力し、結果ウィンドウで **[Evernote]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Evernote](./media/evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Evernote で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Evernote ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Evernote の関連ユーザーの間で、リンク関係が確立されている必要があります。

Evernote で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Evernote で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Evernote のテスト ユーザーの作成](#create-an-evernote-test-user)** - Evernote で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Evernote アプリケーションでシングル サインオンを構成します。

**Evernote で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Evernote** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/evernote-tutorial/tutorial_evernote_samlbase.png)

1. アプリケーションを IDP 開始モードで構成する場合は、**[Evernote のドメインと URL]** セクションで次の手順を実行します。

    ![[Evernote のドメインと URL] のシングル サインオン情報](./media/evernote-tutorial/tutorial_evernote_url.png)

    **[識別子]** ボックスに次の URL を入力します。`https://www.evernote.com/saml2`

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Evernote のドメインと URL] のシングル サインオン情報](./media/evernote-tutorial/tutorial_evernote_url1.png)

    **[サインオン URL]** テキストボックスに、URL として「`https://www.evernote.com/Login.action`」と入力します。   

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/evernote-tutorial/tutorial_evernote_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/evernote-tutorial/tutorial_general_400.png)

1. **[Evernote Configuration (Evernote 構成)]** セクションで、**[Configure Evernote (Evernote を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Evernote のサービス構成](./media/evernote-tutorial/tutorial_evernote_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Evernote 企業サイトに管理者としてログインします。

1. **[管理コンソール]** に移動します。

    ![管理コンソール](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. **[管理コンソール]** から **[セキュリティ]** に移動し、**[シングル サインオン]** を選択します。

    ![SSO の設定](./media/evernote-tutorial/tutorial_evernote_sso.png)

1. 次の値を構成します。

    ![Certificate-Setting](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **SSO の有効化** - SSO は既定で有効になっています (SSO の要件を削除する場合は、**[シングル サインオンを無効にする]** をクリックします)

    b. Azure Portal からコピーした **SAML シングル サインオン サービスの URL**の値 を **[SAML HTTP Request URL]\(SAML HTTP 要求 URL\)** ボックスに貼り付けます。

    c. Azure AD からダウンロードした証明書をメモ帳で開き、"BEGIN CERTIFICATE" および "END CERTIFICATE" を含む内容をコピーして、**[X.509 証明書]** ボックスに貼り付けます。 

    d. **[変更の保存]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/evernote-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/evernote-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/evernote-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/evernote-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-evernote-test-user"></a>Evernote テスト ユーザーの作成

Azure AD ユーザーが Evernote にログインできるようにするには、そのユーザーを Evernote にプロビジョニングする必要があります。  
Evernote の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Evernote の企業サイトに管理者としてログインします。

1. **[管理コンソール]** をクリックします。

    ![管理コンソール](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. **[管理コンソール]** から **[ユーザーの追加]** に移動します。

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

1. **[メール]** テキストボックスの **[チーム メンバーを追加]** にユーザー アカウントのメール アドレスを入力し、**[招待する]** をクリックします。

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
1. 招待が送信された後、招待を承諾するメールが Azure Active Directory アカウント保有者に届きます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Evernote へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Evernote に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Evernote]** を選択します。

    ![アプリケーションの一覧の Evernote のリンク](./media/evernote-tutorial/tutorial_evernote_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Evernote のタイルをクリックすると、Evernote アプリケーションに自動的にサインオンします。 組織のアカウントとしてログインしますが、その後に個人用アカウントでログインする必要があります。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/evernote-tutorial/tutorial_general_01.png
[2]: ./media/evernote-tutorial/tutorial_general_02.png
[3]: ./media/evernote-tutorial/tutorial_general_03.png
[4]: ./media/evernote-tutorial/tutorial_general_04.png

[100]: ./media/evernote-tutorial/tutorial_general_100.png

[200]: ./media/evernote-tutorial/tutorial_general_200.png
[201]: ./media/evernote-tutorial/tutorial_general_201.png
[202]: ./media/evernote-tutorial/tutorial_general_202.png
[203]: ./media/evernote-tutorial/tutorial_general_203.png

