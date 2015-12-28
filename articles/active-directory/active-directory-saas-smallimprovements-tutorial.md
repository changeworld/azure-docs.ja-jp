<properties
	pageTitle="チュートリアル: Azure Active Directory と Small Improvements の統合 | Microsoft Azure"
	description="Azure Active Directory と Small Improvements の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="12/14/2015"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Small Improvements の統合

このチュートリアルの目的は、 Azure Active Directory (Azure AD) と Small Improvements を統合する方法を示すことです。<br>Azure AD と Small Improvements の統合には、次の利点があります。

- Small Improvements にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Small Improvements にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Azure AD と Small Improvements の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Small Improvements でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Small Improvements を追加する
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーから Small Improvements を追加する
Azure AD への Small Improvements の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Small Improvements を追加する必要があります。

**ギャラリーから Small Improvements を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 <br><br> ![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]<br>
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]<br>
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4]<br>
6. 検索ボックスに、「**Small Improvements**」と入力します。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_01.png)<br>
7. 結果ウィンドウで **[Small Improvements]** を選び、**[完了]** をクリックしてアプリケーションを追加します。<br><br>

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Small Improvements で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Small Improvements ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Small Improvements の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンク関係は、Azure AD の **[ユーザー名]** の値を、Small Improvements の **[Username]** の値として割り当てることで確立されます。

Small Improvements で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Small Improvements のテスト ユーザーの作成](#creating-a-small-improvements-test-user)** - Small Improvements で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Small Improvements アプリケーションでシングル サインオンを構成することです。



**Small Improvements で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Small Improvements** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。<br><br>![シングル サインオンの構成][6]<br>

2. **[ユーザーの Small Improvements へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br>![シングル サインオンの構成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_03.png)<br>

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![シングル サインオンの構成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_04.png) <br>

    > [AZURE.NOTE]アカウントのドメイン名を構成するには、Small Improvements サポート チーム ([Support@small-improvements.com](mailto:support@small-improvements.com)) に連絡してください。これは、シングル サインオンが動作するために必要です。


    a.**[サインオン URL]** ボックスに、ユーザーが Small Improvements アプリケーションへのサインオンに使用する URL を入力します。b.**[次へ]** をクリックします。


4. **[Small Improvements でのシングル サインオンの構成]** ページで、次の手順を実行します。<br><br>![シングル サインオンの構成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_05.png) <br>

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


1. 別の Web ブラウザー ウィンドウで、管理者として Small Improvements 企業サイトにサインオンします。

1. メイン ダッシュ ボード ページで、左側の **[管理]** ボタンをクリックします。<br><br>![シングル サインオンの構成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png)<br>


1. **[統合]** セクションで、**[SAML SSO]** ボタンをクリックします。<br><br>![シングル サインオンの構成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png)<br>


1. [SSO セットアップ] ページで、次の手順に従います。<br><br>![シングル サインオンの構成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png)<br>

    a.Azure クラシック ポータルで、**[Small Improvements でのシングル サインオンの構成]** ダイアログ ページの **[SAML SSO URL]** の値をコピーし、**[HTTP エンドポイント]** テキスト ボックスに貼り付けます。

    b.ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[x509 証明書]** ボックスに貼り付けます。

    c.SSO およびログイン フォーム認証オプションをユーザーが使用できるようにするには、**[ログイン/パスワードによるアクセスも有効にする]** オプションをオンにします。
  
	d.**[SAML プロンプト]** ボックスに、SSO ログイン ボタンの名前として適切な値を入力します。

	e.**[保存]** をクリックします。


6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。 <br><br>![Azure AD のシングル サインオン][10]<br>

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![Azure AD のシングル サインオン][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Azure AD ユーザーの作成][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 <br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_09.png) <br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) <br>

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) <br>

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_05.png) <br>

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_06.png) <br>

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_07.png) <br>

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_08.png) <br>

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### Small Improvements テスト ユーザーの作成

このセクションの目的は、Small Improvements で Britta Simon というユーザーを作成することです。この機能は、「[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)」で既に有効にしています。


**Small Improvements で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 管理者として Small Improvements 企業サイトにサインオンします。

1. ホーム ページの左側のメニューに移動し、**[管理]** をクリックします。

1. [ユーザー管理] セクションで、**[ユーザー ディレクトリ]** ボタンをクリックします。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png)<br>

1. **[チーム メンバーの追加]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png)<br>

1. 対応するテキスト ボックスに、プロビジョニングする有効なユーザーの **[名]**、**[姓]**、**[電子メール アドレス]**を入力します。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png)<br>

1. **[通知電子メールを送信する]** ボックスに、個人のメッセージを入力することもできます。通知を送信しない場合は、このチェック ボックスをオフにします。

1. **[ユーザーの作成]** をクリックします。

### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Small Improvements へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200]<br>

**Small Improvements に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。 <br><br>![ユーザーの割り当て][201] <br>

2. アプリケーションの一覧で、**[Small Improvements]** を選択します。 <br><br>![シングル サインオンの構成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_50.png) <br>

1. 上部のメニューで **[ユーザー]** をクリックします。<br><br>![ユーザーの割り当て][203] <br>

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br> アクセス パネルで [Small Improvements] タイルをクリックすると、自動的に Small Improvements アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1217_2015-->