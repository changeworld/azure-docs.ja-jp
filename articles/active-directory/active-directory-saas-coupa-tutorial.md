---
title: "チュートリアル: Azure Active Directory と Coupa の統合 | Microsoft Docs"
description: "Azure Active Directory と Coupa の間にシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 30149f181d8b0ebdc1ae6820da5d561f3a942fa3
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>チュートリアル: Azure Active Directory と Coupa の統合

このチュートリアルでは、Coupa と Azure Active Directory (Azure AD) を統合する方法について説明します。

Coupa と Azure AD の統合には、次の利点があります。

- Coupa にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Coupa にサインオン (シングル サインオン) できるようにすることができます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Coupa と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Coupa でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Coupa の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-coupa-from-the-gallery"></a>ギャラリーからの Coupa の追加
Azure AD への Coupa の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Coupa を追加する必要があります。

**ギャラリーから Coupa を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Coupa**」と入力し、結果ウィンドウで **Coupa** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Coupa](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Coupa で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Coupa ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Coupa の関連ユーザーの間で、リンク関係が確立されている必要があります。

Coupa で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Coupa で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Coupa のテスト ユーザーの作成](#create-a-coupa-test-user)** - Coupa で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Coupa アプリケーションでシングル サインオンを構成します。

**Coupa で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Coupa** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_samlbase.png)

3. **[Coupa のドメインと URL]** セクションで、次の手順を実行します。

    ![[Coupa のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_url.png)

    a. **[サインオン URL]** ボックスに、`http://<companyname>.Coupa.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`<companyname>.coupahost.com` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<companyname>.coupahost.com/sp/ACS.saml2` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 この値を取得するには、[Coupa クライアント サポート チーム](https://success.coupa.com/Support/Contact_Us?)にお問い合わせください。 応答 URL 値はメタデータから取得します。これについてはチュートリアルの後半で説明しています。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-coupa-tutorial/tutorial_general_400.png)

6. Coupa 企業サイトに管理者としてサインオンします。

7. **[Setup]\>[Security Control]** の順に移動します。
   
   ![Security Controls](./media/active-directory-saas-coupa-tutorial/ic791900.png "Security Controls")

8. **[Coupa 資格情報を使用してログイン]** セクションで、次の手順を実行します。

    ![Coupa SP metadata](./media/active-directory-saas-coupa-tutorial/ic791901.png "Coupa SP metadata")
    
    a. **[SAML を使用してログイン]**を選択します。
    
    b. コンピューターに Coupa のメタデータ ファイルをダウンロードするには、 **[SP メタデータのダウンロードとインポート]**をクリックします。 メタデータを開き、**[AssertionConsumerService index/URL]\(AssertionConsumerService インデックス/URL\)** の値をコピーして、**[Coupa のドメインと URL]** セクションの **[応答 URL]** ボックスにこの値を貼り付けます。 
    
    c. **[参照]** をクリックして、Azure Portal からダウンロードしたメタデータをアップロードします。
    
    d. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-coupa-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-coupa-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-coupa-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-coupa-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-coupa-test-user"></a>Coupa テスト ユーザーの作成

Azure AD ユーザーが Coupa にログインできるようにするには、そのユーザーを Coupa にプロビジョニングする必要があります。  

* Coupa の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Coupa** 企業サイトに管理者としてログインします。

2. 上部のメニューで、**[設定]**、**[ユーザー]** の順にクリックします。
   
   ![ユーザー](./media/active-directory-saas-coupa-tutorial/ic791908.png "Users")

3. **Create** をクリックしてください。
   
   ![Create Users](./media/active-directory-saas-coupa-tutorial/ic791909.png "Create Users")

4. **[ユーザーを作成]** セクションで、次の手順を実行します。
   
   ![ユーザーの詳細](./media/active-directory-saas-coupa-tutorial/ic791910.png "User Details")
   
   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの属性として、**ログイン**、**名**、**姓**、**シングル サインオン ID**、**電子メール**を入力します。

   b. **Create** をクリックしてください。   
   
   >[!NOTE]
   >Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。 
   > 

>[!NOTE]
>Coupa から提供されている他の Coupa ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Coupa へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Coupa に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Coupa]**を選択します。

    ![アプリケーションの一覧の Coupa のリンク](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Coupa のタイルをクリックすると、Coupa アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_203.png

