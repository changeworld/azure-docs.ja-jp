<properties
	pageTitle="チュートリアル: Azure Active Directory と 23 Video の統合 | Microsoft Azure"
	description="Azure Active Directory と 23 Video の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="10/23/2015"
	ms.author="markusvi"/>


# チュートリアル: Azure Active Directory と 23 Video の統合

このチュートリアルの目的は、23 Video と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>23 Video と Azure AD の統合には、次の利点があります。

- 23 Video にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に 23 Video にサインオン (シングル サインオン) できるようにします。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

23 Video と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 23 Video でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの 23 Video の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの 23 Video の追加
Azure AD への 23 Video の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に 23 Video を追加する必要があります。

**ギャラリーから 23 Video を追加するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br> ![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]<br>
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]<br>
5. **[実行する内容]** ダイアログ ボックスで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4]<br>
6. 検索ボックスに、「**23 Video**」と入力します。<br><br> ![アプリケーション][5]<br>
7. 結果ウィンドウで **[23 Video]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![アプリケーション][25]<br>

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、23 Video で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する 23 Video ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと 23 Video の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンク関係は、Azure AD の **[ユーザー名]** の値を、23 Video の **[Username]** の値として割り当てることで確立されます。
 
23 Video で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[ 23 Video のテスト ユーザーの作成](#creating-a-23-video-test-user)** - 23 Video で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、23 Video アプリケーションでシングル サインオンを構成することです。<br>

**23 Video で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **23 Video** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。<br><br> ![シングル サインオンの構成][6] <br>

2. **[ユーザーの 23 Video へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Azure AD Single Sign-On][7] <br>

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD Single Sign-On][8] <br>
 
     a.**[応答 URL]** ボックスに、ユーザーが 23 Video サイトへのサインオンに使用する URL を入力します (例: **https://britta-simon.23Video.com/saml/login*))。

     >[AZURE.NOTE]SAML 2.0 を使用した Active Directory の統合は、23 Video のすべてのユーザーが使用できます。関連するメタデータが必要な場合は、サポート ([support@23company.com](mailto:support@23company.com)) にお問い合わせください。

     b.**[次へ]** をクリックします。
 
4. **[23 Video でのシングル サインオンの構成]** ページで、次の手順を実行します。<br><br>![Azure AD Single Sign-On][9] <br>

    a.[証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。

    b.23 Video サポート チーム ([support@23company.com](mailto:support@23company.com)) に連絡して、ダウンロードした証明書、**発行者の URL**、**シングル サインオン サービス URL**、**シングル サインアウト URL** を提供し、23 Video アプリの SSO のセットアップを依頼します。

    c.**[次へ]** をクリックします。


6. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![Azure AD Single Sign-On][10]<br>

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![Azure AD Single Sign-On][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Create Azure AD User][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png) <br> 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. ユーザーの一覧を表示するには、上部のメニューで **[ユーザー]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) <br>
 
4. **[ユーザーの追加]** ダイアログを開くには、下部にあるツール バーで **[ユーザーの追加]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) <br>

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png) <br>

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png) <br>
 
    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** テキスト ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) <br>
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png) <br>
  
    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。

  
 
### 23 Video テスト ユーザーの作成

このセクションの目的は、23 Video で Britta Simon というユーザーを作成することです。

**23 Video で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 23 Video 企業サイトに管理者としてサインオンします。

1. **[Settings]** に移動します。


2. **[Users]** セクションで **[Configure]** をクリックします。<br><br>![ユーザーの割り当て][400]<br>

3. **[Add a new user]** をクリックします。<br><br>![ユーザーの割り当て][401]<br>

4. **[Invite someone to join this site]** セクションで、次の手順を実行します。<br><br>![ユーザーの割り当て][402]<br>

    a.**[E-mail addresses]** ボックスに、Azure AD の Britta Simon の電子メール アドレスを入力します。

    b.**[Add the user]** をクリックします。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に 23 Video へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200] <br>

**23 Video に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルでアプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br> <br>![ユーザーの割り当て][201]<br>
2. アプリケーションの一覧で **[23 Video]** を選択します。<br><br>![ユーザーの割り当て][202] <br>
1. 上部のメニューで **[ユーザー]** をクリックします。<br> <br>![ユーザーの割り当て][203]<br>
1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br> アクセス パネルで [23 Video] タイルをクリックすると、自動的に 23 Video アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png

<!---HONumber=Nov15_HO1-->