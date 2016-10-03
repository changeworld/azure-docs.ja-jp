<properties
	pageTitle="チュートリアル: Azure Active Directory と Beeline の統合 | Microsoft Azure"
	description="Azure Active Directory と Beeline の間でシングル サインオンを構成する方法について確認します。"
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


# チュートリアル: Azure Active Directory と Beeline の統合

このチュートリアルでは、Beeline と Azure Active Directory (Azure AD) を統合する方法について説明します。

Beeline と Azure AD の統合には、次の利点があります。

- Beeline にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Beeline にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Beeline と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Beeline でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Beeline の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Beeline の追加
Azure AD への Beeline の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Beeline を追加する必要があります。

**ギャラリーから Beeline を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**Beeline**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_01.png)

7. 結果ウィンドウで **[Beeline]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_06.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Beeline で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Beeline ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Beeline の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Beeline の **[Username]** の値として割り当てます。

Beeline で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Beeline のテスト ユーザーの作成](#creating-an-beeline-test-user)** - Beeline で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Beeline アプリケーションでシングル サインオンを構成します。

Beeline アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。アプリケーションにマップされる適切なユーザー ID を識別するには、まず Beeline チームと協力してください。また、このマッピングに使用する属性については、Beeline チームからのガイダンスに従ってください。ユーザー ID として **"NameIdentifier"** 属性を使用することをお勧めします。この属性の値は、アプリケーションの **[属性]** タブから管理できます。次のスクリーンショットはその例です。ここでは、nameidentifier 要求を **userprincipalname** 属性にマッピングしています。これは一意のユーザー ID となり、SAML 応答が成功するたびに Beeline アプリケーションに送信されます。

![Configure Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_07.png)


**Beeline で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Beeline** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

	 ![Configure Single Sign-On][6]

2. **[ユーザーの Beeline へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
 	
	![Configure Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

	![Configure Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_04.png)


    a.次のパターンを使用して、ユーザーが Beeline アプリケーションへのサインオンに使用する URL を **[識別子]** ボックスに入力します：`https://projects.beeline.net/<instance name>`

	b.[応答 URL] に、`https://projects.beeline.net/<instance name>/SSO_External.ashx` または `https://projects.beeline.net/<company name>/SSO_External.ashx` の形式で URL を入力します。


4. **[Beeline でのシングル サインオンの構成]** ページで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_05.png)

    a.**[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


5.  自分のアプリケーション向けに SSO を構成する場合は、Beeline のサポート チームに問い合わせると、SSO 構成のサポートを受けられます。ダウンロードしたメタデータ ファイルをメールに添付して送信する必要があること、またエンティティ ID とシングル サインアウト サービス URL を提供する必要があることに注意してください。
  
6. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
	
	![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
  	
	![Azure AD Single Sign-On][11]



### Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。


![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
	
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
	
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
 
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-beeline-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### Beeline テスト ユーザーの作成

このセクションでは、Beeline で Britta Simon というユーザーを作成します。Beeline アプリケーションでは、シングル サインオンを行う前に、すべてのユーザーをアプリケーションにプロビジョニングする必要があります。Beeline のカスタマー サポート担当者と連携し、すべてのユーザーをアプリケーションにプロビジョニングしてください。


> [AZURE.NOTE] ユーザーを手動で作成する必要がある場合、またはユーザーのバッチを作成する必要がある場合は、Beeline のサポート チームにお問い合わせください。


### Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Beeline へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200]

**Beeline に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Beeline]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]


### シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。アクセス パネルで [Beeline] タイルをクリックすると、自動的に Beeline アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->