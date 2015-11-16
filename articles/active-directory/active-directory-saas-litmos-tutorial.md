<properties
	pageTitle="チュートリアル: Azure Active Directory と Litmos の統合 | Microsoft Azure"
	description="Azure Active Directory と Litmos の間でシングル サインオンを構成する方法について説明します。"
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


# チュートリアル: Azure Active Directory と Litmos の統合

このチュートリアルの目的は、Litmos と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>Litmos と Azure AD の統合には、次の利点があります。

- Litmos にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に Litmos にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Litmos と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Litmos でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Litmos の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Litmos の追加
Azure AD への Litmos の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Litmos を追加する必要があります。

**ギャラリーから Litmos を追加するには、次の手順に従います。**

1. **Azure ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br> ![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くために、ディレクトリ ビューで、上部のメニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]<br>
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]<br>
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4]<br>
6. 検索ボックスに、「**Litmos**」と入力します。<br><br> ![アプリケーション][5]<br>
7. 結果ウィンドウで **[Litmos]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![アプリケーション][500]<br>


##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Litmos で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Litmos ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Litmos の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンク関係は、Azure AD の **[ユーザー名]** の値を、Litmos の **[Username]** の値として割り当てることで確立されます。
 
Litmos で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Litmos のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Litmos で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、Litmos アプリケーションでシングル サインオンを構成することです。<br> この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、[バイナリの証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)に関するビデオを参照してください。

構成の一環として、Litmos アプリケーションの **SAML トークン属性**をカスタマイズする必要があります。<br><br> ![Azure AD Single Sign-On][17] <br>

**Litmos で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **Litmos** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。<br><br> ![Configure Single Sign-On][6] <br>

2. **[ユーザーの Litmos へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Azure AD Single Sign-On][7] <br>


1. Litmos 企業サイト (例: **https://azureapptest.litmos.com/account/Login*) に管理者としてサインオンします。<br><br> ![Azure AD Single Sign-On][21] <br>


1. 左側にあるナビゲーション バーで、**[Accounts]** をクリックします。<br><br> ![Azure AD Single Sign-On][22] <br>


1. **[Integrations]** タブをクリックします。<br><br> ![Azure AD Single Sign-On][23] <br>


1. **[Integrations]** タブで、下へスクロールして **[3rd Party Integrations]** を表示し、**[SAML 2.0]** タブをクリックします。<br><br> ![Azure AD Single Sign-On][24] <br>

1. **[The SAML endoiint for litmos is:]** の下の値をコピーします。<br><br> ![Azure AD Single Sign-On][26] <br>


3. Azure ポータルの **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行します。<br><br>![Azure AD Single Sign-On][8] <br>
 
    a.**[発行者]** ボックスに、ユーザーが Litmos アプリケーションへのサインオンに使用する URL (例: **https://azureapptest.litmos.com/account/Login*)) を入力します。
     
    b.**[応答 URL]** ボックスに、前の手順で Litmos アプリケーションからコピーした値を貼り付けます。

    c.**[次へ]** をクリックします。
 
4. **[Litmos でのシングル サインオンの構成]** ページで、次の手順を実行します。<br><br>![Azure AD Single Sign-On][2] <br>

    a.[証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。


1. **Litmos** アプリケーションで、次の手順を実行します。<br><br>![Azure AD Single Sign-On][25] <br>

    a.**[Enable SAML]** をクリックします。

    b.ダウンロードした証明書から **Base-64 でエンコードされた**ファイルを作成します。

    >[AZURE.TIP]詳細については、[バイナリの証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)に関するビデオを参照してください。

    c.Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[SAML X.509 Certificate]** ボックスに貼り付けます。

    d.**[変更を保存]** をクリックします。


6. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![Azure AD Single Sign-On][10]<br>

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![Azure AD Single Sign-On][11]


20. 上部のメニューで **[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。<br><br>![Configure Single Sign-On][12]<br>


24. **[ユーザー属性の追加]** ダイアログで、次の手順を実行します。<br><br>![Configure Single Sign-On][14]<br>

    | 属性名 | 属性値 |
    | ---            | ---             |
    | 電子メール | User.mail |
    | FirstName | User.givenname |
    | Lastname | User.surname |

    上の表のデータ行ごとに、次の手順を実行します。
   
    a.**[ユーザー属性の追加]** をクリックします。<br><br>![Configure Single Sign-On][15]<br>


    a.**[属性名]** ボックスに、その行に対して表示される**属性名**を入力します。

    b.その行に対して表示される**属性値**を選択します。

    c.**[完了]** をクリックして **[ユーザー属性の追加]** ダイアログ ボックスを閉じます。


25. **[変更の適用]** をクリックします。<br><br>![Configure Single Sign-On][16]<br>




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Create Azure AD User][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png) <br> 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. ユーザーの一覧を表示するために、上部のメニューの **[ユーザー]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) <br>
 
4. **[ユーザーの追加]** ダイアログを開くために、下部にあるツール バーの **[ユーザーの追加]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) <br>

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png) <br>

    a.**[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順を実行します。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) <br>
 
    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) <br>
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順を実行します。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) <br>
  
    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。

  
 
### Litmos のテスト ユーザーの作成

このセクションの目的は、Litmos で Britta Simon というユーザーを作成することです。<br> Litmos アプリケーションでは、ジャストインタイム プロビジョニングがサポートされています。そのため、アクセス パネルを使用してアプリケーションにアクセスを試みると、必要に応じてユーザー アカウントが自動的に作成されます。

**Litmos で Britta Simon というユーザーを作成するには、次の手順に従います。**


1. Litmos 企業サイト (例: **https://azureapptest.litmos.com/account/Login*) に管理者としてサインオンします。<br><br> ![Azure AD Single Sign-On][21] <br>


1. 左側にあるナビゲーション バーで、**[Accounts]** をクリックします。<br><br> ![Azure AD Single Sign-On][22] <br>


1. **[Integrations]** タブをクリックします。<br><br> ![Azure AD Single Sign-On][23] <br>


1. **[Integrations]** タブで、下へスクロールして **[3rd Party Integrations]** を表示し、**[SAML 2.0]** タブをクリックします。<br><br> ![Azure AD Single Sign-On][24] <br>

1. **[Autogenerate Users:]** を選択します。<br><br> ![Azure AD Single Sign-On][27] <br>


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Litmos へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200] <br>

**Litmos に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルで、アプリケーション ビューを開くために、ディレクトリ ビューで、上部のメニューの **[アプリケーション]** をクリックします。<br><br>![ユーザーの割り当て][201] <br>
2. アプリケーションの一覧で **[Litmos]** を選択します。<br><br>![ユーザーの割り当て][202] <br>
1. 上部のメニューの **[ユーザー]** をクリックします。<br><br>![ユーザーの割り当て][203] <br>
1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーの **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD シングル サインオン構成をテストすることです。<br> アクセス パネルで Litmos のタイルをクリックすると、自動的に Litmos アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png

<!---HONumber=Nov15_HO2-->