<properties
	pageTitle="チュートリアル: Azure Active Directory と Alcumus Info Exchange の統合 | Microsoft Azure"
	description="Azure Active Directory と Alcumus Info Exchange の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="12/18/2015"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Alcumus Info Exchange の統合

このチュートリアルの目的は、Alcumus Info Exchange と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>Alcumus Info Exchange と Azure AD の統合には、次の利点があります。

- Alcumus Info Exchange にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に Alcumus Info Exchange にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Alcumus Info Exchange と Azure AD の統合を構成するには、次のものが必要です。

- [Azure AD](http://azure.microsoft.com/) サブスクリプション
- [Alcumus Info Exchange](http://www.alcumusgroup.com/) でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Alcumus Info Exchange の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Alcumus Info Exchange の追加
Azure AD への Alcumus Info Exchange の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Alcumus Info Exchange を追加する必要があります。

**ギャラリーから Alcumus Info Exchange を追加するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br>![アプリケーション][2]<br>

4. ページの下部にある **[追加]** をクリックします。<br><br>![アプリケーション][3]<br>

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br>![アプリケーション][4]<br>

6. [検索] ボックスに、**「Alcumus Info Exchange」**と入力します。<br><br>![アプリケーション][5]<br>

7. 結果ウィンドウで **[Alcumus Info Exchange]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![アプリケーション][400]<br>



##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Alcumus Info Exchange で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Alcumus Info Exchange ユーザーが Azure AD で認識される必要があります。言い換えると、Azure AD ユーザーと Alcumus Info Exchange の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Alcumus Info Exchange の **[Username]** の値として割り当てます。
 
Alcumus Info Exchange で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Alcumus Info Exchange のテスト ユーザーの作成](#creating-a-alcumus-info-exchange-test-user)** - Alcumus Info Exchange で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にし、Alcumus Info Exchange アプリケーションでのシングル サインオンを構成することです。<br>

**Alcumus Info Exchange で Azure AD のシングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **[Alcumus Info Exchange]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。<br><br> ![シングル サインオンの構成][6]

2. **[ユーザーの Alcumus Info Exchange へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Azure AD のシングル サインオン][7]

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD Single Sign-On][8]<br>
 
     3\.1. **[応答 URL]** テキスト ボックスに、Alcumus Info Exchange サポート チームによってセットアップされたコンシューマー URL を入力します。

     >[AZURE.NOTE]適切な値がわからない場合は、Alcumus Info Exchange サポート チーム ([helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com)) にお問い合わせください。

     3\.2.**[次へ]** をクリックします。
 
4. **[Alcumus Info Exchange でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。<br><br>![What is Azure AD Connect][9]

5. Alcumus Info Exchange サポート チーム ([helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com)) に連絡してメタデータ ファイルを提供し、SSO を有効にする必要があることを伝えます。


6. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![What is Azure AD Connect][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![Azure AD Connect の概要][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Azure AD ユーザーの作成][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、[**Active Directory]** をクリックします。<br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png)
 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png)
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. **[ユーザー名]** テキスト ボックスに「**BrittaSimon**」と入力します。
  3. [次へ] をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png)
  1. **[名]** テキスト ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png)
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png)
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   

  
 
### Alcumus Info Exchange テスト ユーザーの作成

このセクションの目的は、Alcumus Info Exchange で Britta Simon というユーザーを作成することです。

**Alcumus Info Exchange で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Alcumus Info Exchange サポート チーム ([helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com)) に連絡します。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Alcumus Info Exchange へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200]

**Alcumus Info Exchange に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br> <br><br>![ユーザーの割り当て][201]
2. アプリケーションの一覧で、**[Alcumus Info Exchange]** を選択します。<br><br>![ユーザーの割り当て][202]
1. 上部のメニューで **[ユーザー]** をクリックします。<br> <br><br>![ユーザーの割り当て][203]
1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br> アクセス パネルで [Alcumus Info Exchange] タイルをクリックすると、自動的に Alcumus Info Exchange アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png

<!---HONumber=AcomDC_1223_2015-->