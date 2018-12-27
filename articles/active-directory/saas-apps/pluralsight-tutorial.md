---
title: 'チュートリアル: Azure Active Directory と Pluralsight の統合 | Microsoft Docs'
description: Azure Active Directory と Pluralsight の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jeedes
ms.openlocfilehash: 81f7e6f7610eb855bd4df1eaf45c6d016befc133
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443616"
---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>チュートリアル: Azure Active Directory と Pluralsight の統合

このチュートリアルでは、Pluralsight と Azure Active Directory (Azure AD) を統合する方法について説明します。

Pluralsight と Azure AD の統合には、次の利点があります。

- Pluralsight にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に Pluralsight にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Pluralsight と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Pluralsight でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Pluralsight を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-pluralsight-from-the-gallery"></a>ギャラリーから Pluralsight を追加する
Azure AD への Pluralsight の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Pluralsight を追加する必要があります。

**ギャラリーから Pluralsight を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Pluralsight**」と入力し、結果ウィンドウで **Pluralsight** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Pluralsight](./media/pluralsight-tutorial/tutorial_pluralsight_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Pluralsight で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Pluralsight ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Pluralsight の関連ユーザーの間で、リンク関係が確立されている必要があります。

Pluralsight で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Pluralsight で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Pluralsight テスト ユーザーの作成](#create-a-pluralsight-test-user)** - Pluralsight で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Pluralsight アプリケーションでシングル サインオンを構成します。

**Pluralsight で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Pluralsight** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/pluralsight-tutorial/tutorial_pluralsight_samlbase.png)

1. **[Pluralsight のドメインと URL]** セクションで、次の手順を実行します。

    ![[Pluralsight のドメインと URL] のシングル サインオン情報](./media/pluralsight-tutorial/tutorial_pluralsight_url.png)

    a. **[サインオン URL]** ボックスに、`https://<instancename>.pluralsight.com/sso/<companyname>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに次の URL を入力します。`www.pluralsight.com`

    c. **[応答 URL]** ボックスに、`https://<instancename>.pluralsight.com/sp/ACS.saml2` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Pluralsight クライアント サポート チーム](mailto:support@pluralsight.com)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/pluralsight-tutorial/tutorial_pluralsight_certificate.png) 

1. このセクションの目的は、Azure Portal で Azure AD のシングル サインオンを有効にすることと、Pluralsight アプリケーションで SSO を構成することです。

    Pluralsight アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットはその例です。

    ![Configure single sign-on](./media/pluralsight-tutorial/tutorial_pluralsight_attribute.png)

    >[!NOTE]
    >**"一意の ID"** 属性を追加し、EmployeeID などの組織に合った適切な値を設定することもできます。 これは必須の属性ではありませんが、一意のユーザーを識別するために追加できます。 

1. 必要な **SAML トークン属性**を追加するには、以下の表の各行について、次の手順を実行します。
   
   | 属性名 | 属性値 |
   | ---| --- |
   | 名 |User.givenname |
   | 姓 |User.surname |
   | 電子メール |User.mail |
   
   a. **[ユーザー属性の追加]** をクリックして **[ユーザー属性の追加]** ダイアログを開きます。
    
     ![Configure single sign-on](./media/pluralsight-tutorial/tutorial_pluralsight_addattribute.png)
  
   b. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
  
   c. **[属性値]** リストから、その行に対して表示される属性値を選択します。
  
   d. **[OK]** をクリックします。    

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/pluralsight-tutorial/tutorial_general_400.png)

1. アプリケーション用に構成された SSO を入手するには、[Pluralsight のプロフェッショナル サービス](mailTo:professionalservices@pluralsight.com) チームに連絡し、ダウンロードしたメタデータ ファイルを提供します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/pluralsight-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/pluralsight-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/pluralsight-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/pluralsight-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-pluralsight-test-user"></a>Pluralsight テスト ユーザーの作成

このセクションの目的は、Pluralsight で Britta Simon というユーザーを作成することです。 [Pluralsight クライアント サポート チーム](mailto:support@pluralsight.com)と連携し、Pluralsight アカウントにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Pluralsight へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザー ロールを割り当てる][200] 

**Pluralsight に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Pluralsight]** を選択します。

    ![アプリケーションの一覧の [Pluralsight] のリンク](./media/pluralsight-tutorial/tutorial_pluralsight_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Pluralsight] タイルをクリックすると、自動的に Pluralsight アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/pluralsight-tutorial/tutorial_general_04.png

[100]: ./media/pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/pluralsight-tutorial/tutorial_general_201.png
[202]: ./media/pluralsight-tutorial/tutorial_general_202.png
[203]: ./media/pluralsight-tutorial/tutorial_general_203.png

