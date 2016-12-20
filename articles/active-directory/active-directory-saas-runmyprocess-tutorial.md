---
title: "チュートリアル: Azure Active Directory と RunMyProcess の統合 | Microsoft Docs"
description: "Azure Active Directory と RunMyProcess の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 223a0abfcd1ae2997e4d2facfd065cf8dc721668


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>チュートリアル: Azure Active Directory と RunMyProcess の統合
このチュートリアルの目的は、RunMyProcess と Azure Active Directory (Azure AD) を統合する方法を説明することです。

RunMyProcess と Azure AD の統合には、次の利点があります。

* RunMyProcess にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで RunMyProcess に自動的にサインオン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
RunMyProcess と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* RunMyProcess でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの RunMyProcess の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-runmyprocess-from-the-gallery"></a>ギャラリーからの RunMyProcess の追加
Azure AD への RunMyProcess の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RunMyProcess を追加する必要があります。

**ギャラリーから RunMyProcess を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**RunMyProcess**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. 結果ウィンドウで **[RunMyProcess]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーをベースに、RunMyProcess での Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する RunMyProcess ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと RunMyProcess の関連ユーザーの間でリンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を RunMyProcess の **[Username (ユーザー名)]** の値として割り当てます。

RunMyProcess で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[RunMyProcess のテスト ユーザーの作成](#creating-a-runmyprocess-test-user)** - RunMyProcess で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、RunMyProcess アプリケーションでシングル サインオンを構成します。

**RunMyProcess で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **RunMyProcess** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの RunMyProcess へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://live.runmyprocess.com/live/<tenant id>` のパターンを使用して URL を入力します。
   
    b. click **[次へ]**
   
   > [!NOTE]
   > 実際のサインオン URL で値を更新する必要があることに注意してください。 この値を取得するには、RunMyProcess サポート チーム (<mailto:support@runmyprocess.com>) に問い合わせてください。
   > 
   > 
4. **[RunMyProcess でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、ファイルをコンピューターに保存します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. 別の Web ブラウザーのウィンドウで、管理者として RunMyProcess テナントにサインオンします。
6. 左側のナビゲーション パネルで、**[Account (アカウント)]**、**[Configuration (構成)]** の順にクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. **[Authentication method (認証方法)]** セクションに移動し、次の手順に従います。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[Method]** として、**[SSO with Samlv2]** を選択します。
   
    b. **[SSO redirect (SSO リダイレクト)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[SAML SSO URL]** の値を入力します。
   
    c. **[Logout redirect (ログアウト リダイレクト)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[シングル サインアウト サービス URL]** の値を入力します。
   
    d. **[Name Id Format (名前 ID フォーマット)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[名前識別子形式]** の値を入力します。
   
    e. ダウンロードした証明書ファイルのコンテンツをコピーし、**[Certificate (証明書)]** ボックスに貼り付けます。 
   
    f. **[Save (保存)]** アイコンをクリックします。
8. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
9. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. ページの下部にある **[次へ]**」を参照してください。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png) 
   
   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
   b. **[姓]** ボックスに「**Simon**」と入力します。
   
   c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
   d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
   e. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある [完了]」を参照してください。   

### <a name="creating-a-runmyprocess-test-user"></a>RunMyProcess のテスト ユーザーの作成
Azure AD ユーザーが RunMyProcess にログインできるようにするには、ユーザーを RunMyProcess にプロビジョニングする必要があります。 RunMyProcess の場合、プロビジョニングは手動で行います。

#### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. RunMyProcess 企業サイトに管理者としてログインします。
2. 左側のナビゲーション パネルで **[Account (アカウント)]**、**[Users (ユーザー)]** の順にクリックし、**[New User (新しいユーザー)]** をクリックします。
   
   ![[新しいユーザー]](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "New User")
3. **[ユーザーの設定]** セクションで、次の手順に従います。
   
   ![プロファイル](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profile")
   
   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 プロビジョニングする有効な AAD アカウントの**名前**と**電子メール**を対応するボックスに入力します。
   
   b. **[IDE 言語]**、**[言語]**、**[プロファイル]** を選択します。
   
   c. **[アカウント作成の電子メールを自分に送信]**を選択します。
   
   d. [ **Save**] をクリックします。
   
   > [!NOTE]
   > 他の RunMyProcess ユーザー アカウントの作成ツールまたは RunMyProcess から提供されている API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に RunMyProcess へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**Britta Simon を RunMyProcess に割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[RunMyProcess]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. ツール バーで、**[割り当て]** をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [RunMyProcess] タイルをクリックすると、RunMyProcess アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


