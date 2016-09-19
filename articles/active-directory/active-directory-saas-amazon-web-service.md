<properties
	pageTitle="チュートリアル: Azure Active Directory と Amazon Web Service (AWS) の統合 | Microsoft Azure"
	description="Azure Active Directory で Amazon Web Services (AWS) を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
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
	ms.date="09/01/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Amazon Web Service (AWS) の統合

このチュートリアルの目的は、Amazon Web Service (AWS) と Azure Active Directory (Azure AD) を統合する方法を説明することです。Amazon Web Service (AWS) と Azure AD の統合には、次の利点があります。

- Amazon Web Service (AWS) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Amazon Web Service (AWS) にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Amazon Web Service (AWS) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Amazon Web Service (AWS) でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Amazon Web Service (AWS) の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Amazon Web Service (AWS) の追加
Azure AD への Amazon Web Service (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Service (AWS) を追加する必要があります。

### ギャラリーから Amazon Web Service (AWS) を追加するには、次の手順を実行します。

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。
   
	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
	![アプリケーション][4]

6. 検索ボックスに、「**Amazon Web Service (AWS)**」と入力します。
   
	![アプリケーション][5]

7. 結果ウィンドウで **[Amazon Web Service (AWS)]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![アプリケーション][6]



##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーを使用して、Amazon Web Service (AWS) とのAzure AD シングル サインオンを構成してテストする方法を示すことです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Amazon Web Service (AWS) ユーザーが Azure AD で認識される必要があります。言い換えると、Azure AD ユーザーと Amazon Web Service (AWS) の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係は、Azure AD の **[ユーザー名]** の値を、Amazon Web Service (AWS) の **[Username]** の値として割り当てることで確立されます。
 
Amazon Web Service (AWS) との Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Amazon Web Service (AWS) テスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Amazon Web Service (AWS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### 単一の Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD シングル サインオンを有効にし、Amazon Web Service (AWS) アプリケーションでシングル サインオンを構成することです。Amazon Web Service (AWS) アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを **SAML トークン属性**の構成に追加する必要があります。次のスクリーンショットはその例です。


![Configure Single Sign-On][27]

**Amazon Web Service (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure クラシック ポータルの **Amazon Web Service (AWS)** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

	![Configure Single Sign-On][7]

2. **[ユーザーの Amazon Web Service (AWS) へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Configure Single Sign-On][8]

3. **[アプリケーション設定の構成]** ページで、[次へ] をクリックします。

	![Configure App Settings][9]
 
4. **[Amazon Web Service (AWS) でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。

	![Configure Single Sign-On][10]

5. 別の Web ブラウザーのウィンドウで、管理者として Amazon Web Service (AWS) 企業サイトにサインオンします。

6. **[Console Home]** をクリックします。

	![Configure Single Sign-On][11]

7. **[Identity and Access Management]** をクリックします。

	![Configure Single Sign-On][12]

8. **[Identity Providers]**、**[Create Provider]** の順にクリックします。

	![Configure Single Sign-On][13]

9. **[Configure Provider]** ダイアログ ページで、次の手順を実行します。

	![Configure Single Sign-On][14]

     a.**[Provider Type]** として **[SAML]** を選択します。

     b.**[Provider Name]** ボックスにプロバイダー名を入力します (例: *WAAD*)。

     c.**[Choose File]** をクリックして、ダウンロードしたメタデータ ファイルをアップロードします。

     d.**[Next Step]** をクリックします。


10. **[Verify Provider Information]** ダイアログ ボックスで、**[Create]** をクリックします。

	![Configure Single Sign-On][15]

11. **[Roles]** をクリックしてから **[Create New Role]** をクリックします。

	![Configure Single Sign-On][16]

12. **[Set Role Name]** ダイアログで、次の手順を実行します。

	![Configure Single Sign-On][17]

	a.**[Role Name]** ボックスにロール名を入力します (例: *TestUser*)。

	b.**[Next Step]** をクリックします。

13. **[Select Role Type]** ダイアログで、次の手順を実行します。

	![Configure Single Sign-On][18]

    a.**[Role For Identity Provider Access]** を選択します。

    b.**[Grant Web Single Sign-On (WebSSO) access to SAML providers]** セクションで、**[Select]** をクリックします。


14. **Establish Trust** ダイアログで、次の手順を実行します。

	![Configure Single Sign-On][19]
     
     a.SAML プロバイダーとして、前に作成した SAML プロバイダーを選択します (例: *WAAD*)。

     b.**[Next Step]** をクリックします。


15. **[Verify Role Trust]** ダイアログで、**[Next Step]** をクリックします。

	![Configure Single Sign-On][32]


16. **[Attach Policy]** ダイアログで、**[Next Step]** をクリックします。

	![Configure Single Sign-On][33]


17. **[Review]** ダイアログで、次の手順を実行します。

	![Configure Single Sign-On][34]

     a.**Role の ARN 値** をコピーします。

     b.**Trusted Entities の ARN 値**をコピーします。

     c.**[Create Role]** をクリックします。

18. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

	![Azure AD Connect とは][20]

19. **[シングル サインオンの確認]** ページで **[完了]** を選択して、**[シングル サインオンの構成]** ダイアログを閉じます。

	![What is Azure AD Connect][22]


20. 上部のメニューで、**[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。

	![Configure Single Sign-On][21]

21. **[ユーザー属性の追加]** をクリックします。

	![Configure Single Sign-On][23]

22. [ユーザー属性の追加] ダイアログで、次の手順を実行します。

	![Configure Single Sign-On][24]

    a.**[属性名]** ボックスに、「**https://aws.amazon.com/SAML/Attributes/Role**」と入力します。

    b.**[属性値]** ボックスに、**[Role の ARN 値] と [Trusted Entity の ARN 値]** を入力します。

    >[AZURE.TIP] これらは、ロールの作成時に [Review] ダイアログでコピーした値です。

    c.**[完了]** をクリックして **[ユーザー属性の追加]** ダイアログボックスを閉じます。

23. **[ユーザー属性の追加]** をクリックします。

	![Configure Single Sign-On][23]


24. [ユーザー属性の追加] ダイアログで、次の手順を実行します。

	![Configure Single Sign-On][25]


     a.**[属性名]** ボックスに、「**https://aws.amazon.com/SAML/Attributes/RoleSessionName**」と入力します。

     b.**[属性値]** ボックスに「**user.userprincipalname**」と入力するか、ドロップダウン リストから選択します。
     
	![Configure Single Sign-On][35]
	

     c.**[完了]** をクリックして **[ユーザー属性の追加]** ダイアログボックスを閉じます。


25. **[変更の適用]** をクリックします。

	![Configure Single Sign-On][26]





### Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)
 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)

  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. **[ユーザー名]** テキスト ボックスに「**BrittaSimon**」と入力します。
  3. [次へ] をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)

	a.**[名]** ボックスに「**Britta**」と入力します。

	b.**[姓]** ボックスに「**Simon**」と入力します。
  
	c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

	d.**[ロール]** 一覧で **[ユーザー]** を選択します。
  
	e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)

	a.**[新しいパスワード]** の値を書き留めます。
  
	b.**[完了]** をクリックします。
  
 
### Amazon Web Service (AWS) テスト ユーザーの作成

このセクションの目的は、Amazon Web Service (AWS) で Britta Simon というユーザーを作成することです。

### Amazon Web Service (AWS) で Britta Simon というユーザーを作成するには、次の手順に従います。

1. 管理者として **Amazon Web Service (AWS)** 企業サイトにログインします。

2. **[Console Home (コンソール ホーム)]** アイコンをクリックします。

	![Configure Single Sign-On][11]

3. [Identity and Access Management] をクリックします。

	![Configure Single Sign-On][28]

4. Dashboard で、[Users]、[Create New Users] の順にクリックします。

	![Configure Single Sign-On][29]

5. [Create User] ダイアログで、次の手順に従います。

	![Configure Single Sign-On][30]

     a.**[Enter User Names]** ボックスに、Brita Simon の Azure AD でのユーザー名 (userprincipalname) を入力します。

     b.**[作成]** をクリックします。




### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Amazon Web Service (AWS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][31]

**Britta Simon を CloudPassage に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][26]

2. アプリケーションの一覧で、**[Amazon Web Service (AWS)]** を選択します。

	![ユーザーの割り当て][27]

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][25]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][29]

### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。アクセス パネルで [Amazon Web Service (AWS)] タイルをクリックすると、自動的に Amazon Web Service (AWS) アプリケーションにサインオンします。


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
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png

<!---HONumber=AcomDC_0907_2016-->