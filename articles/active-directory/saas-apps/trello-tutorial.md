---
title: 'チュートリアル: Azure Active Directory と Trello の統合 | Microsoft Docs'
description: Azure Active Directory と Trello の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 163d7faa99d362f400581c42974eeb4a466641d9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449164"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>チュートリアル: Azure Active Directory と Trello の統合

このチュートリアルでは、Trello と Azure Active Directory (Azure AD) を統合する方法について説明します。

Trello と Azure AD の統合には、次の利点があります:

- Trello にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Trello にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Trello と Azure AD の統合を構成するには、次のものが必要です:

- Azure AD サブスクリプション
- Trello でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Trello の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-trello-from-the-gallery"></a>ギャラリーからの Trello の追加
Azure AD への Trello の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Trello を追加する必要があります。

**ギャラリーから Trello を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Trello**」と入力し、結果パネルで **[Trello]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Trello](./media/trello-tutorial/tutorial_trello_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Trello で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Trello ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Trello の関連ユーザーの間で、リンク関係が確立されている必要があります。

Trello で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当てて、リンク関係を確立します。

Trello で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります:

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Trello テスト ユーザーの作成](#create-a-trello-test-user)** - Trello で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Trello アプリケーションでシングル サインオンを構成します。

>[!NOTE]
>Trello から **\<enterprise\>** スラグを取得する必要があります。 スラグ値を取得していない場合は、[Trello サポート チーム](mailto:support@trello.com)に問い合わせて、自社のスラグを取得してください。
    > 

**Trello で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Trello** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/trello-tutorial/tutorial_trello_samlbase.png)

1. **IDP 開始モード**でアプリケーションを構成する場合は、**[Trello のドメインと URL]** セクションで次の手順を実行します。

    ![[Trello のドメインと URL] のシングル サインオン情報](./media/trello-tutorial/tutorial_trello_url.png)
    
    a. **[識別子]** ボックスに、次の URL を入力します。`https://trello.com/auth/saml/metadata`
    
    b. **[応答 URL]** ボックスに、`https://trello.com/auth/saml/consume/<enterprise>` のパターンを使用して URL を入力します。

1. **SP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![[Trello のドメインと URL] のシングル サインオン情報](./media/trello-tutorial/tutorial_trello_url1.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[サインオン URL]** ボックスに、`https://trello.com/auth/saml/login/<enterprise>` のパターンを使用して URL を入力します。 

1. Trello アプリケーションは SAML アサーションを使用し、特定の属性を含みます。 このアプリケーションには、次の属性を構成します。 これらの属性の値は、アプリケーションの **[ユーザー属性]** から管理できます。 次のスクリーンショットはその例です。

    ![Configure single sign-on](./media/trello-tutorial/tutorial_trello_attribute.png)

1. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。
 
    | 属性名 | 属性値 |
    | --- | --- |
    | User.Email | User.mail |
    | User.FirstName | User.givenname |
    | User.LastName | User.surname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/trello-tutorial/tutorial_officespace_04.png)

    ![Configure single sign-on](./media/trello-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。 

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。 

1. **[SAML 署名証明書]** セクションで **[証明書 (Base64)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/trello-tutorial/tutorial_trello_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/trello-tutorial/tutorial_general_400.png)
    
1. **[Trello の構成]** セクションで、**[Trello を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Trello の構成](./media/trello-tutorial/tutorial_trello_configure.png) 

1. **Trello** 側にシングルサインオンを構成するには、[[Trello enterprise SSO configuration]\(Trello エンタープライズ SSO 構成\)](https://trello.com/sso-configuration) ページに移動し、ダウンロードした**証明書 (Base64)** および **SAML シングル サインオン サービス URL** を [Trello サポート チーム](mailto:support@trello.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/trello-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/trello-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/trello-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/trello-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-trello-test-user"></a>Trello テスト ユーザーを作成する

このセクションの目的は、Trello で Britta Simon というユーザーを作成することです。 Trello では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Trello ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Trello サポート チーム](mailto:support@trello.com)にお問い合わせください。


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Trello へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Trello に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Trello]** を選択します。

    ![アプリケーションの一覧の [Trello] リンク](./media/trello-tutorial/tutorial_trello_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Trello] タイルをクリックすると、自動的に Trello アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/trello-tutorial/tutorial_general_01.png
[2]: ./media/trello-tutorial/tutorial_general_02.png
[3]: ./media/trello-tutorial/tutorial_general_03.png
[4]: ./media/trello-tutorial/tutorial_general_04.png

[100]: ./media/trello-tutorial/tutorial_general_100.png

[200]: ./media/trello-tutorial/tutorial_general_200.png
[201]: ./media/trello-tutorial/tutorial_general_201.png
[202]: ./media/trello-tutorial/tutorial_general_202.png
[203]: ./media/trello-tutorial/tutorial_general_203.png

