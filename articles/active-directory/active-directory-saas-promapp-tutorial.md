<properties
	pageTitle="チュートリアル: Azure Active Directory と Promapp の統合 | Microsoft Azure"
	description="Azure Active Directory と Promapp の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="07/07/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Promapp の統合

このチュートリアルの目的は、Promapp と Azure Active Directory (Azure AD) を統合する方法を説明することです。Promapp と Azure AD の統合には、次の利点があります。

- Promapp にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Promapp にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Promapp と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Promapp でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Promapp を追加する
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーから Promapp を追加する
Azure AD への Promapp の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Promapp を追加する必要があります。

**ギャラリーから Promapp を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに「**Promapp**」と入力します。

	![アプリケーション][5]

7. 結果ウィンドウで **[Promapp]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![アプリケーション][500]

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Promapp で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Promapp ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Promapp の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係は、Azure AD の **[ユーザー名]** の値を、Promapp の **[Username]** の値として割り当てることで確立されます。
 
Promapp で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Promapp のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Promapp で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Promapp アプリケーションでシングル サインオンを構成することです。

**Promapp で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure AD クラシック ポータルの **Promapp** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

	![Configure Single Sign-On][6]

2. **[ユーザーの Promapp へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Azure AD Single Sign-On][7]

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

	![Azure AD のシングル サインオン][8]
 
     a.**[サインオン URL]** ボックスに、ユーザーが Promapp サイトへのサインオンに使用する URL を入力します (例: *https://companyname.promapp.com/instancename*)。


     b.**[次へ]** をクリックします。
 
4. **[Promapp でのシングル サインオンの構成]** ページで、次の手順を実行します。

	![Azure AD のシングル サインオン][9]

    a.[証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


6. Promapp 企業サイトに管理者としてサインオンします。

6. 上部のメニューで **[管理者]** をクリックします。

	![Azure AD のシングル サインオン][12]

6. **[構成]** をクリックします。

	![Azure AD のシングル サインオン][13]



4. **[Security]** ダイアログで、次の手順を実行します。

	![Azure AD のシングル サインオン][14]

    a.Azure クラシック ポータルの **[Promapp でのシングル サインオンの構成]** ダイアログで、**[リモート ログイン URL]** の値をコピーして、**[SSO-Login URL]** ボックスに貼り付け、**[Save]** をクリックします。

    b.**[SSO - Single Sign-on Mode]** として **[Optional]** を選択し、**[Save]** をクリックします。

    c.ダウンロードした証明書をメモ帳で開き、最初の行 (*-----BEGIN CERTIFICATE-----*) と最後の行 (*-----END CERTIFICATE-----*) を除く証明書の内容をコピーし、**[SSO-x.509 Certificate]** に貼り付け、**[Save]** をクリックします。




6. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] \(シングル サインオンの構成の確認) を選択し、**[次へ]** をクリックします。

	![Azure AD のシングル サインオン][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

	![Azure AD Single Sign-On][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png)
 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順を実行します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png)
 
    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png)
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順を実行します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png)
  
    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。

  
 
### Promapp のテスト ユーザーの作成

Promapp アプリケーションでは、ジャストインタイム プロビジョニングがサポートされています。そのため、アクセス パネルを使用してアプリケーションにアクセスを試みると、必要に応じてユーザー アカウントが自動的に作成されます。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Promapp へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**Promapp に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Promapp]** を選択します。

	![ユーザーの割り当て][202]

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。アクセス パネルで [Promapp] タイルをクリックすると、自動的に Promapp アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png

<!---HONumber=AcomDC_0713_2016-->