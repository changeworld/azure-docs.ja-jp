<properties
	pageTitle="チュートリアル: Azure Active Directory と Skydesk Email の統合 | Microsoft Azure"
	description="Azure Active Directory と Skydesk Email の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="07/11/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Skydesk Email の統合

このチュートリアルの目的は、Skydesk Email と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Skydesk Email と Azure AD の統合には、次の利点があります。

- Skydesk Email にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Skydesk Email にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Azure AD と Skydesk Email の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Skydesk Email でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Skydesk Email の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Skydesk Email の追加
Azure AD への Skydesk Email の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Skydesk Email を追加する必要があります。

**ギャラリーから Skydesk Email を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**Skydesk Email**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)

7. 結果ウィンドウで **[Skydesk Email]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Skydesk Email で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Skydesk Email ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Skydesk Email の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Skydesk Email の **[Username]** の値として割り当てることで確立されます。

Skydesk Email で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Skydesk Email のテスト ユーザーの作成](#creating-a-Skydesk-Email-test-user)** - Skydesk Email で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Skydesk Email アプリケーションでシングル サインオンを構成することです。



**Skydesk Email で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Skydesk Email** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

	![Configure Single Sign-On][6]

2. **[ユーザーの Skydesk Email へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png)


3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
 
	![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png)


    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Skydesk Email アプリケーションへのサインオンに使用する URL を入力します。**“https://mail.skydesk.jp/portal/<company name>”**

    b.**[次へ]** をクリックします。


4. **[Skydesk Email でのシングル サインオンの構成]** ページで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


5. **Skydesk Email** で SSO を有効にするには、次の手順に従います。
 
    a.管理者として Skydesk Email アカウントにサインオンします。

    b.上部のメニューで、[設定] をクリックし、[組織] を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)

    c.左側のパネルで [ドメイン] をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d.[ドメインの追加] をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e.自分のドメイン名を入力し、ドメインを確認します。

    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f.左側のパネルで **[SAML 認証]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)

6. **[SAML 認証]** ダイアログ ページで、次の手順に従います。

    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)

	> [AZURE.NOTE] SAML ベースの認証を使用するには、**ドメインが検証済み**または**ポータル URL**がセットアップ済みである必要があります。一意の名前でポータル URL を設定できます。

	![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)


    a.Azure AD クラシック ポータルで、**[SAML SSO URL]** 値をコピーし、**[ログイン URL]** ボックスに貼り付けます。

    b.Azure AD クラシック ポータルで、**[シングル サインアウト サービス URL]** 値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。

    c.**[パスワード変更 URL]** は省略可能なので、空白のままにします。

    d.**[ファイルからキーを取得]** をクリックし、ダウンロードした Skydesk Email 証明書を選択して、**[開く]** をクリックして証明書をアップロードします。

    e.**[アルゴリズム]** として **[RSA]** を選択します。

    f.**[OK]** をクリックして変更を保存します。


7. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

	![Azure AD Single Sign-On][10]

8. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
  
	![Azure AD Single Sign-On][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
 
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### Skydesk Email テスト ユーザーの作成

このセクションでは、Skydesk Email で Britta Simon というユーザーを作成します。

a.左側のパネルで **[ユーザー アクセス]** をクリックし、自分のユーザー名を入力します。

![シングル サインオンの構成](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)


[AZURE.NOTE] ユーザーを一括で作成する必要がある場合は、Skydesk Email のサポート チームにお問い合わせください。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Skydesk Email へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**Skydesk Email に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
 
	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Skydesk Email]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Skydesk Email] タイルをクリックすると、自動的に Skydesk Email アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->