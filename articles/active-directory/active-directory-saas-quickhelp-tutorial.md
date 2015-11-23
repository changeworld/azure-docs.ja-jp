<properties
	pageTitle="チュートリアル: Azure Active Directory と QuickHelp の統合 | Microsoft Azure"
	description="Azure Active Directory と QuickHelp の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/06/2015"
	ms.author="markusvi"/>


# チュートリアル: Azure Active Directory と QuickHelp の統合

このチュートリアルの目的は、QuickHelp と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>QuickHelp と Azure AD の統合には、次の利点があります。

- QuickHelp にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に QuickHelp にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

QuickHelp と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- QuickHelp でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの QuickHelp の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの QuickHelp の追加
Azure AD への QuickHelp の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に QuickHelp を追加する必要があります。

**ギャラリーから QuickHelp を追加するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br>![アプリケーション][2]<br>
4. ページの下部にある **[追加]** をクリックします。<br><br>![アプリケーション][3]<br>
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br>![アプリケーション][4]<br>
6. 検索ボックスに、「**QuickHelp**」と入力します。<br><br>![アプリケーション][5]<br>
7. 結果ウィンドウで **[QuickHelp]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![アプリケーション][500]<br>


##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、QuickHelp で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。


QuickHelp で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[ QuickHelp のテスト ユーザーの作成](#creating-a-quickhelp-test-user)** - QuickHelp で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、QuickHelp アプリケーションでシングル サインオンを構成することです。<br>

**QuickHelp で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **[QuickHelp]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。<br><br>![シングル サインオンの構成][6]<br>

2. **[ユーザーの QuickHelp へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br>![Azure AD のシングル サインオン][7]<br>

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![アプリケーションの設定の構成][8]<br>
 
     a.**[サインオン URL]** ボックスに、ユーザーが QuickHelp サイトへのサインオンに使用する URL を入力します (例: *https://quickhelp.com/bsiazure/*))。

     >[AZURE.NOTE]サインオン URL の値がわからない場合は、QuickHelp サポート チームに問い合わせてください。

     b.**[次へ]** をクリックします。

 
4. **[QuickHelp でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターでメタデータ ファイルをローカルに保存します。<br><br>![What is Azure AD Connect][9]<br>



1. QuickHelp 企業サイトに管理者としてサインオンします。

2. 上部のメニューで **[管理者]** をクリックします。<br><br>![シングル サインオンの構成][21]<br>


1. **[QuickHelp の管理者]** メニューの **[設定]** をクリックします。<br><br>![シングル サインオンの構成][22]<br>

1. **[認証設定]** をクリックします。

1. **[認証設定]** ページで、次の手順に従います。<br><br>![シングル サインオンの構成][23]<br>

    a.**SSO 型** として **WSFederation** を選びます。

    b.ダウンロードした Azure メタデータ ファイルをアップロードするには、**[参照]** をクリックし、目的のファイルに移動し、**[メタデータのアップロード]** をクリックします。

    d.**[Email]** テキスト ボックスに「****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。

    e.**[名]** テキスト ボックスに、「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**」と入力します。

    f.**[姓]** テキスト ボックスに、「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**」と入力します。

    g.**[ 操作バー ]** で、**[保存]** をクリックします。







6. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![What is Azure AD Connect][10]<br>

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![What is Azure AD Connect][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Create Azure AD User][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png)<br> 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png)<br>
 
4. 下部にあるツールバーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログを開きます。 <br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png)<br>

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 <br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png)<br>

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) <br>
 
    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) <br>
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) <br>
  
    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。

  
 
### QuickHelp テスト ユーザーの作成

このセクションの目的は、QuickHelp で Britta Simon というユーザーを作成することです。シングル サインオンを機能させるには、Azure AD ユーザーに対応する QuickHelp ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと QuickHelp の関連ユーザーの間で、リンク関係が確立されている必要があります。

QuickHelp では、ジャストインタイム プロビジョニングがサポートされています。つまり、必要に応じて、ユーザー アカウントは、QuickHelp で自動的に作成され、Azure AD アカウントにリンクされます。

このセクションでは、ユーザー側で必要な操作はありません。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に QuickHelp へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200]<br>

**QuickHelp に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br> <br>![ユーザーの割り当て][201]<br>

2. アプリケーションの一覧で **[QuickHelp]** を選択します。<br><br>![ユーザーの割り当て][202]<br>

1. 上部のメニューで **[ユーザー]** をクリックします。<br><br>![ユーザーの割り当て][203] <br>

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br> アクセス パネルで [QuickHelp] タイルをクリックすると、自動的に QuickHelp アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png

<!---HONumber=Nov15_HO3-->