<properties
	pageTitle="チュートリアル: Azure Active Directory と UserEcho の統合 | Microsoft Azure"
	description="Azure Active Directory と UserEcho の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と UserEcho の統合

このチュートリアルの目的は、UserEcho と Azure Active Directory (Azure AD) を統合する方法を説明することです。UserEcho と Azure AD の統合には、次の利点があります。

- UserEcho にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に UserEcho にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Azure AD と UserEcho の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- UserEcho でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの UserEcho の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの UserEcho の追加
Azure AD への UserEcho の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に UserEcho を追加する必要があります。

**ギャラリーから UserEcho を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**UserEcho**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)

7. 結果ウィンドウで **[UserEcho]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、UserEcho で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する UserEcho ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと UserEcho の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係は、Azure AD の **[ユーザー名]** の値を、UserEcho の **[Username]** の値として割り当てることで確立されます。
 
UserEcho で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[UserEcho のテスト ユーザーの作成](#creating-a-userecho-test-user)** - UserEcho で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、UserEcho アプリケーションでシングル サインオンを構成することです。




**UserEcho で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **UserEcho** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

	![Configure Single Sign-On][6]

2. **[ユーザーの UserEcho へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png)

    a.**[サインオン URL]** ボックスに、ユーザーが UserEcho アプリケーションへのサインオンに使用する URL (例: *https://fabrikam.UserEcho.com/*) を入力します。

    b.**[次へ]** をクリックします。
 
 
4. **[UserEcho でのシングル サインオンの構成]** ページで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


1. 別の Web ブラウザー ウィンドウで、管理者として UserEcho 企業サイトにサインオンします。

1. 上部にあるツール バーで、ユーザー名をクリックして、メニューを展開し、**[Setup (セットアップ)]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

1. **[Integrations (統合)]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png)


1. **[Website (Web サイト)]**、**[Single sign-on (SAML2) (シングル サインオン (SAML2))]** の順にクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png)


1. **[Single sign-on (SAML2) (シングル サインオン (SAML2))]** ページで、次の手順に従います。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)

    a.**[SAML-enabled]** で **[Yes]** を選択します。

    b.Azure クラシック ポータルで、[UserEcho でのシングル サインオンの構成] ダイアログ ページの **[シングル サインオン サービス URL]** の値をコピーし、**[SAML SSO URL]** ボックスに貼り付けます。

    c.Azure クラシック ポータルで、[UserEcho でのシングル サインオンの構成] ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[Remote logout URL (リモート ログアウト URL)]** ボックスに貼り付けます。

    d.ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[X.509 Certificate]** ボックスに貼り付けます。

    e.[**Save**] をクリックします。


6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

	![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

	![Azure AD Single Sign-On][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png)
 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png)
 
    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png)
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順を実行します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png)
  
    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。

  
 
### UserEcho のテスト ユーザーの作成

このセクションの目的は、UserEcho で Britta Simon というユーザーを作成することです。

**UserEcho で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. UserEcho 企業サイトに管理者としてサインオンします。

1. 上部にあるツール バーで、ユーザー名をクリックして、メニューを展開し、**[Setup (セットアップ)]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

1. **[Users (ユーザー)]** をクリックして、**[Users (ユーザー)]** セクションを展開します。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

1. **[ユーザー]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

1. **[Invite a new user (新しいユーザーを招待)]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)


1. **[Invite a new user (新しいユーザーを招待)]** ダイアログで、次の手順に従います。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a.**[Name]** ボックスに「**Britta Simon**」と入力します。

    b.**[Email]** ボックスに、Britta の Azure クラシックポータルの電子メール アドレスを入力します。

    c.**[招待]** をクリックします。

Britta に招待状が送信され、UserEcho の使用を開始できるようになります。



### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に UserEcho へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**UserEcho に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[UserEcho]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。アクセス パネルで UserEcho のタイルをクリックすると、自動的に UserEcho アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->