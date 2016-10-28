<properties
	pageTitle="チュートリアル: Azure Active Directory と ImageRelay の統合 | Microsoft Azure"
	description="Azure Active Directory と ImageRelay の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="08/15/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と ImageRelay の統合

このチュートリアルの目的は、ImageRelay と Azure Active Directory (Azure AD) を統合する方法を説明することです。ImageRelay と Azure AD の統合には、次の利点があります。

- ImageRelay にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に ImageRelay にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Azure AD と ImageRelay の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ImageRelay でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ImageRelay の追加

2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの ImageRelay の追加
Azure AD への ImageRelay の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ImageRelay を追加する必要があります。

**ギャラリーから ImageRelay を追加するには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに「**ImageRelay**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)

7. 結果ウィンドウで **[ImageRelay]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、ImageRelay で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD で ImageRelay の関連ユーザーを表すユーザー アカウントが必要です。言い換えると、Azure AD ユーザーと ImageRelay の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係は、Azure AD の **[ユーザー名]** の値を、ImageRelay の **[Username]** の値として割り当てることで確立されます。

ImageRelay で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD テスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
4. **[ImageRelay のテスト ユーザーの作成](#creating-a-userecho-test-user)** - ImageRelay で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、ImageRelay アプリケーションでシングル サインオンを構成することです。


**ImageRelay で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **ImageRelay** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

     ![Configure Single Sign-On][6]

2. **[ユーザーの ImageRelay へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

     ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png)

    a.**[サインオン URL]** ボックスに、ユーザーが ImageRelay アプリケーションへのサインオンに使用する URL を入力します (例: *https://fabrikam.ImageRelay.com/*)。

    b.**[次へ]** をクリックします。

4. **[ImageRelay でのシングル サインオンの構成]** ページで、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。

5. 別のブラウザー ウィンドウで、管理者として ImageRelay 企業サイトにサインインします。

1. 上部にあるツール バーで **[Users & Permissions (ユーザーとアクセス許可)]** ワークロードをクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png)

1. **[Create New Permission (新しいアクセス許可を作成)]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

1. **[Single Sign On Settings (シングル サインオンの設定)]** ワークロードで、**[This Group can only sign-in via Single Sign On (このグループはシングル サインオンによるサインインのみ可能)]** チェック ボックスをオンにして **[Save (保存)]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png)

1. **[Account Settings (アカウントの設定)]** に移動します。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png)

1. **[Single Sign On Settings (シングル サインオンの設定)]** ワークロードに移動します。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

1. **[SAML Settings (SAML の設定)]** ダイアログで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)

	a.Azure クラシック ポータルで **[シングル サインオン サービス URL]** の値をコピーし、**[Login URL (ログイン URL)]** ボックスに貼り付けます。


	b.Azure クラシック ポータルで **[シングル サインアウト サービス URL]** をコピーし、**[Logout URL (ログアウト URL)]** ボックスに貼り付けます。

	c.**[Name Id Format (名前 ID 形式)]** として **[urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress]** を選択します。

	
    d.**[Binding Options for Requests from the Service Provider (Image Relay) (サービス プロバイダー (Image Relay) からの要求のバインド オプション)]** として **[POST Binding (POST バインド)]** を選択します。
   

	e.**[x.509 Certificate (x.509 証明書)]** の下にある **[Update Certificate (証明書の更新)]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f.ダウンロードした証明書をメモ帳で開き、その内容をコピーして、[x.509 Certificate (x.509 証明書)] ボックスに貼り付けます。
  
	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g.**[Just-In-Time User Provisioning (Just-In-Time ユーザー プロビジョニング)]** セクションで、**[Enable Just-In-Time User Provisioning (Just-In-Time ユーザー プロビジョニングを有効にする)]** チェック ボックスをオンにします。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h.シングル サインオンによるサインインのみを許可するアクセス許可グループを選択します (**[SSO Basic]** など)。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i.[**Save**] をクリックします。

6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

    ![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

    ![Azure AD Single Sign-On][11]


### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### ImageRelay テスト ユーザーの作成

このセクションの目的は、ImageRelay で Britta Simon というユーザーを作成することです。

**ImageRelay で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. ImageRelay 企業サイトに管理者としてログインします。

1. **[Users & Permissions (ユーザーとアクセス許可)]** に移動して **[Create SSO User (SSO ユーザーを作成)]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png)

1. プロビジョニングするユーザーの **[Email (電子メール)]**、**[First Name (名)]**、**[Last Name (姓)]**、**[Company (会社名)]** を入力し、シングル サインオンのみでサインインできるアクセス許可グループ ([SSO Basic] など) を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png)

1. **[作成]** をクリックします。

### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に ImageRelay へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**ImageRelay に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[ImageRelay]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]


### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。アクセス パネルで [ImageRelay] タイルをクリックすると、自動的に ImageRelay アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->