<properties
	pageTitle="チュートリアル: Azure Active Directory と CloudPassage の統合 | Microsoft Azure"
	description="Azure Active Directory と CloudPassage の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="01/26/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と CloudPassage の統合

このチュートリアルの目的は、CloudPassage と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>CloudPassage と Azure AD の統合には、次の利点があります。

- CloudPassage にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に CloudPassage にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。 
- 

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

CloudPassage と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- CloudPassage でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD のテスト環境がない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの CloudPassage の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの CloudPassage の追加
Azure AD への CloudPassage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に CloudPassage を追加する必要があります。

### ギャラリーから CloudPassage を追加するには、次の手順に従います。

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 <br><br> ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br>![アプリケーション][4]
6. 検索ボックスに、「**CloudPassage**」と入力します。<br><br>![アプリケーション][5]
7. 結果ウィンドウで **[CloudPassage]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![アプリケーション][6]



##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーを使用して、CloudPassage との Azure AD シングル サインオンを構成してテストする方法を示すことです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する CloudPassage ユーザーが Azure AD で認識される必要があります。言い換えると、Azure AD ユーザーと CloudPassage の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンク関係は、Azure AD の **[ユーザー名]** の値を、CloudPassage の **[Username]** の値として割り当てることで確立されます。
 
CloudPassage での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD テスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
4. **[CloudPassage のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - CloudPassage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD クラシック ポータルで Azure AD シングル サインオンを有効にし、CloudPassage アプリケーションでのシングル サインオンを構成することです。<br> CloudPassage アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。次のスクリーンショットにこの例を示します。<br><br>![Configure Single Sign-On][21]

**CloudPassage との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure AD クラシック ポータルの **CloudPassage** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして **[シングル サインオンの構成]** ダイアログを開きます。<br><br> ![Configure Single Sign-On][7]

2. **[ユーザーの CloudPassage へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Configure Single Sign-On][8]

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![Configure App Settings][9]
 
     3\.1.**[サインオン URL]** ボックスに、ユーザーが CloudPassage アプリケーションへのサインオンに使用する URL を入力します (例: **https://portal.cloudpassage.com/saml/init/accountid*))。

     3\.2.[応答 URL] ボックスに、AssertionConsumerService URL を入力します (例: **https://portal.cloudpassage.com/saml/consume/accountid*))。<br> この属性の値は、CloudPassage ポータルの **[Single Sign-on Settings]** セクションで **[SSO Setup documentation]** をクリックすることで取得できます。<br><br>![Configure Single Sign-On][10]

     3\.3**[次へ]** をクリックします。



4. **[CloudPassage でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。<br><br>![Configure Single Sign-On][11]

5. 別の Web ブラウザーのウィンドウで、管理者として CloudPassage 企業サイトにサインオンします。

6. 上部のメニューの **[Settings]** をクリックし、次に **[Site Administration]** をクリックします。 <br><br> ![Configure Single Sign-On][12]

7. **[Authentication Settings]** タブをクリックします。<br><br> ![Configure Single Sign-On][13]


8. **[Single Sign-On Settings]** セクションで、次の手順に従います。<br><br>![Configure Single Sign-On][14]


     8\.1.Azure クラシック ポータルの **[CloudPassage でのシングル サインオンの構成]** ダイアログ ページで **[発行者の URL]** の値をコピーし、それを **[SAML issuer URL]** テキスト ボックスに貼り付けます。

     8\.2.Azure クラシック ポータルの **[CloudPassage でのシングル サインオンの構成]** ダイアログ ページで **[サービス プロバイダー (SP) によって開始されたエンドポイント]** の値をコピーし、**[SAML endpoint URL]** テキスト ボックスに貼り付けます。

     8\.3.Azure クラシック ポータルの **[CloudPassage でのシングル サインオンの構成]** ダイアログ ページで **[ログアウト URL]** の値をコピーし、**[Logout landing page]** テキスト ボックスに貼り付けます。

     8\.4.ダウンロードした証明書から **Base-64** でエンコードされたファイルを作成します。
          
      >[AZURE.TIP] 詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

     8\.5.base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーし、**[X.509 Certificate]** テキスト ボックスに貼り付けます。

     8\.6**[保存]** をクリックします。


9. Azure AD クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。 <br><br> ![Configure Single Sign-On][15]


10. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![Configure Single Sign-On][16]



11. 上部のメニューの **[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。<br><br>![Configure Single Sign-On][17]

12. 必要なユーザー属性を追加するために、次の表の各行に対して、次の手順を実行します。<br>

| 属性名 | 属性値 |
| --- | --- |
| firstname | User.givenname |
| lastname | User.surname |
| email | User.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. 下部にあるツールバーの **[変更の適用]** をクリックします。<br><br>![Configure Single Sign-On][20]



### Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。<br><br> [ユーザー] の一覧で **[Britta Simon]** を選択します。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. **[ユーザー名]** テキスト ボックスに「**BrittaSimon**」と入力します。
  3. [次へ] をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   


  
 
### CloudPassage テスト ユーザーの作成

このセクションの目的は、CloudPassage で Britta Simon というユーザーを作成することです。

#### CloudPassage で Britta Simon というユーザーを作成するには、次の手順に従います。

1.	**CloudPassage** 企業サイトに管理者としてサインオンします。 

2.	上部にあるツールバーの **[Settings]** をクリックし、次に **[Site Administration]** をクリックします。<br>![CloudPassage テスト ユーザーの作成][22]

3.	**[Users]** タブをクリックし、次に **[Add New User]** をクリックします。<br>![CloudPassage テスト ユーザーの作成][23]
	
4.	**[Add New User]** セクションで、次の手順を実行します。<br>![CloudPassage テスト ユーザーの作成][24]

     4\.1.**[First Name]** テキスト ボックスに「Britta」と入力します。

     4\.2.**[Last Name]** テキスト ボックスに「Simon」と入力します。

     4\.3.**[Username]** テキスト ボックス、**[Email]** テキスト ボックス、**[Retype Email]** テキスト ボックスに、Britta の Azure AD でのユーザー名を入力します。

     4\.4.**[Access Type]** として、**[Enable Halo Portal Access]** を選択します。

     4\.5.**[追加]** をクリックします。










### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に CloudPassage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][30]

**Britta Simon を CloudPassage に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br> <br><br>![ユーザーの割り当て][26]
2. アプリケーションの一覧で **[CloudPassage]** を選択します。<br><br>![ユーザーの割り当て][27]
1. 上部のメニューの **[ユーザー]** をクリックします。<br> <br><br>![ユーザーの割り当て][25]
1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][29]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD シングル サインオン構成をテストすることです。<br> アクセス パネルで [CloudPassage] タイルをクリックすると、自動的に CloudPassage アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=AcomDC_0128_2016-->