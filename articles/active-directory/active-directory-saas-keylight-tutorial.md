<properties
	pageTitle="チュートリアル: Azure Active Directory と Keylight の統合 | Microsoft Azure"
	description="Azure Active Directory と Keylight の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="04/18/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Keylight の統合

このチュートリアルでは、Keylight と Azure Active Directory (Azure AD) を統合する方法について説明します。

Keylight と Azure AD の統合には、次の利点があります。

- Keylight にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Keylight にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Keylight と Azure AD の統合を構成するには、次のものが必要です。

- Azure サブスクリプション
- Keylight でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Keylight の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Keylight の追加
Azure AD への Keylight の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Keylight を追加する必要があります。

**ギャラリーから Keylight を追加するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**Keylight**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. 結果ウィンドウで **[Keylight]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Keylight で Azure AD のシングル サインオンを構成し、テストします。

Keylight で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Keylight のテスト ユーザーの作成](#creating-a-keylight-test-user)** - Keylight で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、Keylight アプリケーションでシングル サインオンを構成します。


**Keylight で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **Keylight** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

	![Configure Single Sign-On][6]


2. **[ユーザーの Keylight へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
 
	![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png)


    a.[サインオン URL] ボックスに、ユーザーが Keylight アプリケーションへのサインオンに使用する URL を **"https://<企業名>.keylightgrc.com/Login.aspx?saml=1"** の形式で入力します。


4. **[Keylight でのシングル サインオンの構成]** ページで、次の手順に従います。
 
	![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


5. Keylight で SSO を有効にするには、次の手順に従います。
 
    a.管理者として Keylight アカウントにサインオンします。

    b.上部のメニューで **[人]** をクリックして **[Keylight セットアップ]** を選択します。
       
	![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/401.png)

    c.左側のツリー ビューで **[SAML]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/402.png)

    d.**[SAML 設定]** ダイアログ ボックスで **[編集]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/404.png)
  

5. **[SAML 設定の編集]** ダイアログ ページで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/405.png)

    a.**[SAML 認証]** を **[アクティブ]** に設定します。


    b.Azure AD クラシック ポータルで、**[SAML SSO URL]** 値をコピーし、**[ID プロバイダー ログイン URL]** ボックスに貼り付けます。

    c.Azure AD クラシック ポータルで、**[シングル サインアウト サービス URL]** 値をコピーし、**[ID プロバイダー ログアウト URL]** ボックスに貼り付けます。

    d.**[ファイルの選択]** をクリックし、ダウンロードした Keylight 証明書を選択して、**[開く]** をクリックして証明書をアップロードします。


    e.**[SAML ユーザー ID の場所]** を **[Subject ステートメントの NameIdentifier 要素]** に設定します。
   
    f.**Keylight サービス プロバイダーを **https://&lt;Company Name&gt;.keylightgrc.com** の形式で指定します。

    g.**[ユーザーの自動プロビジョニング]** を **[アクティブ]** に設定します。

    h.**[アカウント タイプの自動プロビジョニング]** を **[すべてのユーザー]** に設定します。

    i.**[セキュリティ ロールの自動プロビジョニング]** として**[SAML を使用する標準ユーザー]** を選択します。
   
    j.**[セキュリティ構成の自動プロビジョニング]** として**[標準ユーザー構成]** を選択します。
   
    k.[電子メール属性] テキストボックスに、「****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。

    l.**[名属性]** テキストボックスに、「****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**」と入力します。

    m.**[姓属性]** テキストボックスに、「****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**」と入力します。

    n.**[保存]** をクリックします。
   
  
   
  
6. Azure ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

	![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

	![Azure AD Single Sign-On][11]




### Azure AD のテスト ユーザーの作成
このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

ユーザーの一覧で **[Britta Simon]** を選択します。

![Azure AD ユーザーの作成][20]



**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png)


2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png)


4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### Keylight テスト ユーザーの作成

このセクションでは、Keylight で Britta Simon というユーザーを作成します。Keylight では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。ユーザーがまだ存在しない場合は、Keylight へのアクセス時に新しいユーザーが作成されます。

> [AZURE.NOTE] ユーザーを手動で作成する必要がある場合は、Keylight のサポート チームにお問い合わせください。


### Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Keylight へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200]

**Keylight に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Keylight]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Keylight] タイルをクリックすると、自動的に Keylight アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0420_2016-->