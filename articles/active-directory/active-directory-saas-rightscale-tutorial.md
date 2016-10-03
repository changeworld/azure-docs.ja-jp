<properties
	pageTitle="チュートリアル: Azure Active Directory と RightScale の統合 | Microsoft Azure"
	description="Azure Active Directory と RightScale の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と RightScale の統合

このチュートリアルの目的は、RightScale と Azure Active Directory (Azure AD) を統合する方法を説明することです。RightScale と Azure AD の統合には、次の利点があります。

- RightScale にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に RightScale にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Azure AD と RightScale の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- RightScale でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの RightScale の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの RightScale の追加
Azure AD への RightScale の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RightScale を追加する必要があります。

**ギャラリーから RightScale を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
 
	![アプリケーション][4]

6. 検索ボックスに、「**RightScale**」と入力します。
 
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_01.png)

7. 結果ウィンドウで **[RightScale]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![ギャラリーでアプリを選択する](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_02.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、RightScale で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する RightScale ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと RightScale の関連ユーザーの間で、リンク関係が確立されている必要があります。


RightScale で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[RightScale のテスト ユーザーの作成](#creating-a-rightscale-test-user)** - RightScale で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、RightScale アプリケーションでシングル サインオンを構成することです。



**RightScale で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **RightScale** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

	![Configure Single Sign-On][6]

2. **[ユーザーの RightScale へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行して **[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_04.png)


    a.[応答 URL] ボックスに、`https://login.rightscale.com/login/saml2/consume` の形式で URL を入力します。

	b.**[次へ]** をクリックします。

4. **[アプリケーション設定の構成]** ダイアログ ページで、**SP 開始モード**でアプリケーションを構成する場合は、**[詳細設定を表示します (オプション)]** をクリックし、**[サインオン URL]** を入力して **[次へ]** をクリックします。
 
	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_06.png)

	a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。`https://login.rightscale.com/`

	b.**[次へ]** をクリックします。

5. **[RightScale シングル サインオン パラメーターの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターに Base-64 でエンコードされた証明書ファイルを保存します。

    b.**[次へ]** をクリックします。


5. アプリケーションに合わせて SSO を構成するには、管理者として RightScale テナントにサインオンする必要があります。

	a.上部にあるメニューの **[Settings]** タブをクリックし、**[Single Sign-On]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png)

	b.**[new]** ボタンをクリックして、**SAML ID プロバイダー**を追加します。

	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png)

	c.**[Display Name]** テキスト ボックスに会社名を入力します。
	
	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png)

	d.**[Allow RightScale-initiated SSO using a discovery hint]** を選択して、下のテキストボックスに**ドメイン名**を入力します。

	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)

	e.Azure AD から RightScale の **[SAML SSO Endpoint]** に SAML SSO URL をコピーします。

	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_005.png)
	
	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)

	f.Azure AD から RightScale の **[AML EntityID]** にエンティティ ID をコピーします。
	
	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_007.png)
	
	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)

	g.**[Browser]** ボタンをクリックして、手順 4 でダウンロードした証明書をアップロードします。
	
	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)

	h.[**Save**] をクリックします。
	


6. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

	![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
  
	![Azure AD Single Sign-On][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rightscale-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rightscale-tutorial/create_aaduser_05.png)

    a.**[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rightscale-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rightscale-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rightscale-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### RightScale のテスト ユーザーの作成

このセクションでは、RightScale で Britta Simon というユーザーを作成します。support@rightscale.com から RightScale サポート チームと連携し、RightScale プラットフォームにユーザーを追加してください。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に RightScale へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**RightScale に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[RightScale]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。アクセス パネルで [RightScale] タイルをクリックすると、自動的に RightScale アプリケーションにサインオンします。
	

## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->