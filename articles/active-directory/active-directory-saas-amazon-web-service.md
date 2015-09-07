<properties
	pageTitle="チュートリアル: Azure Active Directory と Amazon Web Service (AWS) の統合 | Microsoft Azure"
	description="Azure Active Directory と Amazon Web Service (AWS) の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="08/27/2015"
	ms.author="markvi"/>


# チュートリアル: Azure Active Directory と Amazon Web Service (AWS) の統合

このチュートリアルの目的は、Amazon Web Service (AWS) と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>Amazon Web Service (AWS) と Azure AD の統合には、次の利点があります。

- Amazon Web Service (AWS) にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に Amazon Web Service (AWS) にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Amazon Web Service (AWS) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Amazon Web Service (AWS) でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Amazon Web Service (AWS) の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Amazon Web Service (AWS) の追加
Azure AD への Amazon Web Service (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Service (AWS) を追加する必要があります。

### ギャラリーから Amazon Web Service (AWS) を追加するには、次の手順を実行します。

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くために、ディレクトリ ビューで、上部のメニューの **[アプリケーション]** をクリックします。<br><br>![アプリケーション][2]
4. ページの下部にある **[追加]** をクリックします。<br><br>![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br>![アプリケーション][4]
6. 検索ボックスに、「**Amazon Web Service (AWS)**」と入力します。<br><br>![アプリケーション][5]
7. 結果ウィンドウで **[Amazon Web Service (AWS)]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![アプリケーション][6]



##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーを使用して、Amazon Web Service (AWS) とのAzure AD シングル サインオンを構成してテストする方法を示すことです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Amazon Web Service (AWS) ユーザーが Azure AD で認識される必要があります。言い換えると、Azure AD ユーザーと Amazon Web Service (AWS) の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンク関係は、Azure AD の **[ユーザー名]** の値を、Amazon Web Service (AWS) の **[Username]** の値として割り当てることで確立されます。
 
Amazon Web Service (AWS) との Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD テスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
4. **[Amazon Web Service (AWS) テスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Amazon Web Service (AWS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### 単一の Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD シングル サインオンを有効にし、Amazon Web Service (AWS) アプリケーションでのシングル サインオンを構成することです。<br> Amazon Web Service (AWS) アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを **SAML トークン属性**の構成に追加する必要があります。次のスクリーンショットにこの例を示します。<br><br>![Configure Single Sign-On][27]

**Amazon Web Service (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure AD ポータルの **Amazon Web Service (AWS)** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして **[シングル サインオンの構成]** ダイアログを開きます。<br><br>![Configure Single Sign-On][7]

2. **[ユーザーの Amazon Web Service (AWS) へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Configure Single Sign-On][8]

3. **[アプリ設定の構成]** ページで、[次へ] をクリックします。<br><br>![Configure App Settings][9]
 
4. **[Amazon Web Service (AWS) でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。<br><br>![Configure Single Sign-On][10]

5. 別の Web ブラウザーのウィンドウで、管理者として Amazon Web Service (AWS) 企業サイトにサインオンします。

6. **[Console Home]** をクリックします。<br><br> ![Configure Single Sign-On][11]

7. **[Identity and Access Management]** をクリックします。<br><br> ![Configure Single Sign-On][12]

8. **[Identity Providers]** をクリックし、次に **[Create Provider]** をクリックします。<br><br> ![Configure Single Sign-On][13]

9. **[Configure Provider]** ダイアログ ページで、次の手順を実行します。<br><br> ![Configure Single Sign-On][14]

     9\.1.**[Provider Type]** として **[SAML]** を選択します。

     9\.2.**[Provider Name]** テキストボックスにプロバイダー名を入力します (例: *WAAD*)。

     9\.3.ダウンロードしたメタデータ ファイルをアップロードするには、**[Choose file]** をクリックします。

     9\.4.**[Next Step]** をクリックします。


10. **[Verify Provider Information]** ダイアログ ボックスで、**[Create]** をクリックします。<br><br> ![Configure Single Sign-On][15]

11. **[Roles]、[Create New Role]** の順に選択します。<br><br> ![Configure Single Sign-On][16]

12. **[Set Role Name]** ダイアログで、次の手順を実行します。<br><br> ![Configure Single Sign-On][17] 12.1.**[Role Name]** テキストボックスにロール名を入力します (例: *TestUser*)。

     12\.2.**[Next Step]** をクリックします。

13. **[Select Role Type]** ダイアログで、次の手順を実行します。<br><br> ![Configure Single Sign-On][18]

     13\.1.**[Role For Identity Provider Access]** を選択します。

     13\.2.**[Grant Web Single Sign-On (WebSSO) access to SAML providers]** セクションで、**[Select]** をクリックします。


14. [Establish Trust] ダイアログで、次の手順を実行します。<br><br> ![Configure Single Sign-On][19] 14.1.SAML プロバイダーとして、前に作成した SAML プロバイダーを選択します (例: *WAAD*)。



15. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![What is Azure AD Connect][20]

16. **[シングル サインオンの確認]** ページで、**[完了]** を選択して **[シングル サインオンの構成]** ダイアログを閉じます。<br><br>![What is Azure AD Connect][22]


17. 上部のメニューの **[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。<br><br>![Configure Single Sign-On][21]

18. **[ユーザー属性の追加]** をクリックします。<br><br>![Configure Single Sign-On][23]

19. [ユーザー属性の追加] ダイアログで、次の手順を実行します。<br><br> ![Configure Single Sign-On][24]

     19\.1.**[属性名]** テキストボックスに、**「**https://aws.amazon.com/SAML/Attributes/Role**」と入力します。

     19\.2.**[属性値]** テキストボックスに、「**arn:aws:iam::214510765665:role/Admin,arn:aws:iam::214510765665:saml-provider/WAAD**」と入力します。

     19\.3.**[完了]** をクリックして **[ユーザー属性の追加]** ダイアログボックスを閉じます。

20. **[ユーザー属性の追加]** をクリックします。<br><br> ![Configure Single Sign-On][23]


21. [ユーザー属性の追加] ダイアログで、次の手順を実行します。<br><br> ![Configure Single Sign-On][25]

     21\.1.**[属性名]** テキストボックスに、**「**https://aws.amazon.com/SAML/Attributes/RoleSessionName**」と入力します。

     21\.2.**[属性値]** の一覧で、**[user:mail]** を選択します。

     21\.3.**[完了]** をクリックして **[ユーザー属性の追加]** ダイアログボックスを閉じます。


22. **[変更の適用]** をクリックします。<br><br>![Configure Single Sign-On][26]





### Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。<br> [ユーザー] の一覧で、**[Britta Simon]** を選択します。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. ユーザーの一覧を表示するために、上部のメニューの **[ユーザー]** をクリックします。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)
 
4. **[ユーザーの追加]** ダイアログを開くために、下部にあるツール バーの **[ユーザーの追加]** をクリックします。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. **[ユーザー名]** テキスト ボックスに「**BrittaSimon**」と入力します。
  3. [次へ] をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順を実行します。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** の一覧で **[ユーザー]** を選択します。
  5. **[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順を実行します。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   
  
 
### Amazon Web Service (AWS) テスト ユーザーの作成

このセクションの目的は、Amazon Web Service (AWS) で Britta Simon というユーザーを作成することです。

### Amazon Web Service (AWS) で Britta Simon というユーザーを作成するには、次の手順に従います。

1. 管理者として **Amazon Web Service (AWS)** 企業サイトにログインします。

2. **[Console Home]** アイコンをクリックします。<br><br> ![Configure Single Sign-On][11]

3. [Identity and Access Management] をクリックします。<br><br>![Configure Single Sign-On][28]

4. Dashboard で、[Users] をクリックし、次に [Create New Users] をクリックします。<br><br> ![Configure Single Sign-On][29]

5. [Create User dialog] で、次の手順を実行します。<br><br> ![Configure Single Sign-On][30]

     5\.1.**[Enter User Names]** テキストボックスに、Brita Simon の Azure AD でのユーザー名を入力します。

     5\.2.**[作成]** をクリックします。




### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Amazon Web Service (AWS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][31]

**Britta Simon を CloudPassage に割り当てるには、次の手順を実行します。**

1. Azure ポータルで、アプリケーション ビューを開くために、ディレクトリ ビューで、トップ メニューの **[アプリケーション]** をクリックします。<br> <br><br>![ユーザーの割り当て][26]
2. アプリケーションの一覧で、**[Amazon Web Service (AWS)]** を選択します。<br><br>![ユーザーの割り当て][27]
1. 上部のメニューの **[ユーザー]** をクリックします。<br> <br><br>![ユーザーの割り当て][25]
1. [ユーザー] の一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーの **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][29]

### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD シングル サインオン構成をテストすることです。<br> アクセス パネルで [Amazon Web Service (AWS)] タイルをクリックすると、自動的に Amazon Web Service (AWS) アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png

<!---HONumber=August15_HO9-->