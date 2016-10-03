<properties
	pageTitle="チュートリアル: Azure Active Directory と Cezanne HR Software の統合 | Microsoft Azure"
	description="Azure Active Directory と Cezanne HR Software の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="09/15/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Cezanne HR Software の統合

このチュートリアルの目的は、Cezanne HR Software と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Cezanne HR Software と Azure AD の統合には、次の利点があります。

- Cezanne HR Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Cezanne HR Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Cezanne HR Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Cezanne HR Software でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Cezanne HR Software の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Cezanne HR Software の追加
Azure AD への Cezanne HR Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cezanne HR Software を追加する必要があります。

**ギャラリーから Cezanne HR Software を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
	
	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。
	
	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**Cezanne HR Software**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. 結果ウィンドウで **[Cezanne HR Software]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![ギャラリーでアプリを選択する](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Cezanne HR Software で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Cezanne HR Software ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Cezanne HR Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Cezanne HR Software の **[Username (ユーザー名)]** の値として割り当てます。

Cezanne HR Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Cezanne HR Software のテスト ユーザーの作成](#creating-a-cezanne-hr-software-test-user)** - Cezanne HR Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Cezanne HR Software アプリケーションでシングル サインオンを構成します。

**Cezanne HR Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Cezanne HR Software** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
	 
	![Configure Single Sign-On][6]

2. **[ユーザーの Cezanne HR Software へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
    
	![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

	a.**[サインオン URL]** ボックスに、`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>` という形式で URL を入力します。

    b.**[識別子]** ボックスに、「`https://w3.cezanneondemand.com/CezanneOnDemand/`」と入力します。

	c.**[応答 URL]** ボックスに、`https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp` の形式で URL を入力します。

	d.**[次へ]** をクリックします。

	> [AZURE.NOTE] 実際のサインオン URL、識別子、応答 URL で値を更新する必要があることに注意してください。これらの値を取得するには、Cezanne HR Software サポート チーム (<mailto:info@cezannehr.com>) に連絡してください。

4. **[Cezanne HR Software でのシングル サインオンの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。

5. 別の Web ブラウザーのウィンドウで、管理者として Cezanne HR Software テナントにサインオンします。

6. 左側のナビゲーション ウィンドウで、**[System Setup (システム設定)]** をクリックします。**[Security Settings (セキュリティの設定)]** に移動します。次に、**[Single Sign-On Configuration (シングル サインオンの構成)]** に移動します。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. **[Allow users to log in using the following Single Sign-On (SSO) Service (ユーザーに次のシングル サインオン (SSO) サービスを使用したログインを許可する)]** パネルで **[SAML 2.0]** チェック ボックスをオンにし、その横にある **[Advanced Configuration (詳細設定)]** オプションを選択します。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. **[Add New (新規追加)]** ボタンをクリックします。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. **[SAML 2.0 IDENTITY PROVIDERS (SAML 2.0 ID プロバイダー)]** セクションで、次の手順に従います。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

	a.**[Display Name (表示名)]** に ID プロバイダー名を入力します。

	b.**[Entity Identifier (エンティティ識別子)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[エンティティ ID]** の値を入力します。

	c.**[SAML Binding (SAML バインディング)]** を "POST" に変更します。

	d.**[Security Token Service Endpoint (セキュリティ トークン サービス エンドポイント)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[シングル サインオン サービス URL]** の値を入力します。

	e.**[User ID Attribute Name (ユーザー ID 属性名)]** に、「http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name」と入力します。

	f.**アップロード** アイコンをクリックして、Azure AD からダウンロードした証明書をアップロードします。

	g.**[OK]** ボタンをクリックします。

10. **[保存]** ボタンをクリックします。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
    
	![Azure AD Single Sign-On][10]

12. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
    
	![Azure AD Single Sign-On][11]



### Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[Last Name]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### Cezanne HR Software のテスト ユーザーの作成

Azure AD ユーザーが Cezanne HR Software にログインできるようにするには、ユーザーを Cezanne HR Software にプロビジョニングする必要があります。Cezanne HR Software の場合、プロビジョニングは手動で行います。

####ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  Cezanne HR Software 企業サイトに管理者としてログインします。

2.  左側のナビゲーション ウィンドウで、**[System Setup (システム設定)]** をクリックします。**[ユーザーの管理]** に移動します。**[Add New User (新しいユーザーの追加)]** に移動します。

    ![新しいユーザー](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "新しいユーザー")

3.  **[Person Details (個人の詳細)]** セクションで、次の手順を実行します。

    ![新しいユーザー](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "新しいユーザー")

	a.**[Internal User (内部ユーザー)]** をオフに設定します。

	b.**[名]** ボックスに「**Britta**」と入力します。

    c.**[Last Name]** ボックスに「**Simon**」と入力します。

	d.**[E-mail (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

4.  **[Account Information (アカウント情報)]** セクションで、次の手順を実行します。

    ![新しいユーザー](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "新しいユーザー")

	a.**[Username (ユーザー名)]** ボックスに、Britta Simon の電子メール アドレスを入力します。

	b.**[Password (パスワード)]** ボックスに、Britta Simon アカウントのパスワードを入力します。

	c.**[Security Role (セキュリティ ロール)]** で **[HR Professional (人事担当者)]** を選択します。

	d. **[OK]** をクリックします。

5. **[Single Sign-On (シングル サインオン)]** タブに移動し、**[SAML 2.0 Identifiers (SAML 2.0 識別子)]** 領域で **[Add New (新規追加)]** を選択します。

	![User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "User")

6. **[Identity Provider (ID プロバイダー)]** でお使いの ID プロバイダーを選択し、**[User Identifier (ユーザー識別子)]** ボックスに Britta Simon アカウントの電子メール アドレスを入力します。

	![User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "User")
	
7. **[保存]** ボタンをクリックします。

	![User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "User")


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Cezanne HR Software へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。
	
![ユーザーの割り当て][200]

**Cezanne HR Software に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
    
	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Cezanne HR Software]** を選択します。
    
	![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. 上部のメニューで **[ユーザー]** をクリックします。
    
	![ユーザーの割り当て][203]

4. ユーザーの一覧で **[Britta Simon]** を選択します。

5. 下部にあるツール バーで **[割り当て]** をクリックします。
    
	![ユーザーの割り当て][205]

### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。
 
アクセス パネルで [Cezanne HR Software] タイルをクリックすると、自動的に Cezanne HR Software アプリケーションにサインオンします。

## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->