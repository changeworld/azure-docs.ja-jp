<properties
	pageTitle="チュートリアル: Azure Active Directory と PerformanceCentre の統合 | Microsoft Azure"
	description="Azure Active Directory と PerformanceCentre の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="10/30/2015"
	ms.author="markusvi"/>


# チュートリアル: Azure Active Directory と PerformanceCentre の統合

このチュートリアルの目的は、PerformanceCentre と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>PerformanceCentre と Azure AD の統合には、次の利点があります。

- PerformanceCentre にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に PerformanceCentre にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

PerformanceCentre と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- PerformanceCentre でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの PerformanceCentre の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの PerformanceCentre の追加
Azure AD への PerformanceCentre の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PerformanceCentre を追加する必要があります。

**ギャラリーから PerformanceCentre を追加するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br> ![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くために、ディレクトリ ビューで、上部のメニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]<br>
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]<br>
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4]<br>
6. 検索ボックスに、「**PerformanceCentre**」と入力します。<br><br> ![アプリケーション][5]<br>
7. 結果ウィンドウで **[PerformanceCentre]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![アプリケーション][500]<br>


##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、PerformanceCentre で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する PerformanceCentre ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと PerformanceCentre の関連ユーザーの間で、リンクの関係が確立されている必要があります。<br> このリンク関係は、Azure AD の **[ユーザー名]** の値を、PerformanceCentre の **[Username]** の値として割り当てることで確立されます。
 
PerformanceCentre で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[PerformanceCentre のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - PerformanceCentre で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、PerformanceCentre アプリケーションでシングル サインオンを構成することです。<br>

**PerformanceCentre で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **PerformanceCentre** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。<br><br> ![Configure Single Sign-On][6] <br>

2. **[ユーザーの PerformanceCentre へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Azure AD Single Sign-On][7] <br>

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD Single Sign-On][8] <br>
 
     a.**[サインオン URL]** ボックスに、ユーザーが PerformanceCentre サイトへのサインオンに使用する URL を入力します (例: **http://companyname.performancecentre.com/saml/SSO*))。
 
     b.**[次へ]** をクリックします。
 
4. **[PerformanceCentre でのシングル サインオンの構成]** ページで、次の手順を実行します。<br><br>![Azure AD Single Sign-On][9] <br>

    a.**[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。



1. **PerformanceCentre** 企業サイトに管理者としてサインオンします。

2. 左側にあるタブで、**[Configure]** をクリックします。<br><br>![Azure AD Single Sign-On][10]

2. 左側にあるタブで、**[Miscellaneous]**、**[Single Sign On]** の順にクリックします。<br><br>![Azure AD Single Sign-On][11]

2. **[Protocol]** で **[SAML]** を選択します。<br><br>![Azure AD Single Sign-On][12]

2. ダウンロードしたメタデータ ファイルをメモ帳で開き、ファイルの内容をコピーし、**[Identity Provider Metadata]** ボックスに貼り付け、**[Save]** をクリックします。<br><br>![Azure AD Single Sign-On][13]

2. **[Entity Base URL]** と **[Entity ID URL]** の値が正しいことを確認します。<br><br>![Azure AD Single Sign-On][14]


6. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![Azure AD Single Sign-On][15]<br>

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![Azure AD Single Sign-On][16]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Create Azure AD User][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png) <br> 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. ユーザーの一覧を表示するために、上部のメニューの **[ユーザー]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) <br>
 
4. **[ユーザーの追加]** ダイアログを開くために、下部にあるツール バーの **[ユーザーの追加]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) <br>

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png) <br>

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順を実行します。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) <br>
 
    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) <br>
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順を実行します。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) <br>
  
    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。

  
 
### PerformanceCentre のテスト ユーザーの作成

このセクションの目的は、PerformanceCentre で Britta Simon というユーザーを作成することです。

**PerformanceCentre で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. PerformanceCentre 企業サイトに管理者としてサインオンします。

2. 左側のメニューで、**[Interrelate]**、**[Create Participant]** の順にクリックします。<br><br>![ユーザーの作成][400]<br>

4. **[Interrelate - Create Participant]** ダイアログ ボックスで、次の手順を実行します。<br><br>![ユーザーの作成][401]<br>

    a.関連するテキスト ボックスに Britta Simon の必要な属性を入力します。
    
    > [AZURE.IMPORTANT]PerformanceCentre での Britta の User Name 属性は、Azure AD でのユーザー名と同じにする必要があります。


    b.**[Choose Role]** で **[Client Administrator]** を選択します。

    c.**[保存]** をクリックします。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に PerformanceCentre へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200] <br>

**PerformanceCentre に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルで、アプリケーション ビューを開くために、ディレクトリ ビューで、上部のメニューの **[アプリケーション]** をクリックします。<br><br>![ユーザーの割り当て][201] <br>
2. アプリケーションの一覧で **[PerformanceCentre]** を選択します。<br><br>![ユーザーの割り当て][202] <br>
1. 上部のメニューの **[ユーザー]** をクリックします。<br><br>![ユーザーの割り当て][203] <br>
1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーの **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD シングル サインオン構成をテストすることです。<br> アクセス パネルで PerformanceCentre のタイルをクリックすると、自動的に PerformanceCentre アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png

<!---HONumber=Nov15_HO2-->