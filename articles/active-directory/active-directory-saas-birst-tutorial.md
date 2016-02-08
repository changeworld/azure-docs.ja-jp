<properties
	pageTitle="チュートリアル: Azure Active Directory と Birst Agile Business Analytics の統合 | Microsoft Azure"
	description="Azure Active Directory と Birst Agile Business Analytics の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="01/22/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Birst Agile Business Analytics の統合

このチュートリアルの目的は、 Azure Active Directory (Azure AD) と Birst Agile Business Analytics を統合する方法を示すことです。<br>Azure AD と Birst Agile Business Analytics の統合には、次の利点があります。

- Birst Agile Business Analytics にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Birst Agile Business Analytics にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Azure AD と Birst Agile Business Analytics の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Birst Agile Business Analytics でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Birst Agile Business Analytics を追加する
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーから Birst Agile Business Analytics を追加する
Azure AD への Birst Agile Business Analytics の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Birst Agile Business Analytics を追加する必要があります。

**ギャラリーから Birst Agile Business Analytics を追加するには、次の手順を実行します。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br> ![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]<br>
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]<br>
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4]<br>
6. 検索ボックスに、「**Birst Agile Business Analytics**」と入力します。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/tutorial_birst_01.png)<br>
7. 結果ウィンドウで **[Birst Agile Business Analytics]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。 <br><br>

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Birst Agile Business Analytics で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Birst Agile Business Analytics ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Birst Agile Business Analytics の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Birst Agile Business Analytics の **[Username]** の値として割り当てます。

Birst Agile Business Analytics で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Birst Agile Business Analytics のテスト ユーザーの作成](#creating-a-Birst Agile Business Analytics-test-user)** - Birst Agile Business Analytics で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、Birst Agile Business Analytics アプリケーションでシングル サインオンを構成するにです。



**Birst Agile Business Analytics で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **Birst Agile Business Analytics** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。 <br><br> ![Configure Single Sign-On][6] <br>

2. **[Birst Agile Business Analytics へのサインオン方法を選択してください]** ページで、**[Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。 <br><br> ![Configure Single Sign-On](./media/active-directory-saas-birst-tutorial/tutorial_birst_03.png) <br>

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![Configure Single Sign-On](./media/active-directory-saas-birst-tutorial/tutorial_birst_04.png) <br>


    a.[サインオン URL] テキストボックスに、Birst Agile Business Analytics アプリケーションにユーザーがサインオンするときに使用する URL を入力します。その際の「**https://login.bws.birst.com/SAMLSSO/Service.aspx?birst.idpid=TENANTIDPID**」のパターンを利用します。


4. **[Birst Agile Business Analytics でのシングル サインオンの構成]** ページで、次の手順を実行します。<br><br>![Configure Single Sign-On](./media/active-directory-saas-birst-tutorial/tutorial_birst_05.png) <br>

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


5. お使いのアプリケーション用に構成された SSO を取得するために、ダウンロードした証明書ファイルを電子メールに添付して、Birst Agile Business Analytics のサポート チーム ([info@birst.com](emailTo:info@birst.com)) に問い合わせます。SAML SSO URL、サインアウト URL、発行元 URL もお知らせください。サポート チームが SSO 統合で設定します。


> [AZURE.NOTE] Birst チームには、この統合には SHA256 アルゴリズムが必要であることを伝えてください (SHA1 はサポートされません)。Birst チームは、**app2101** などの適切なサーバーに SSO を設定します。



6. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![Azure AD のシングル サインオン][10]<br>

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![Azure AD のシングル サインオン][11]



### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Azure AD ユーザーの作成][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_09.png) <br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_03.png) <br>

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_04.png) <br>

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_05.png) <br>

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_06.png) <br>

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_07.png) <br>

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_08.png) <br>

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### Birst Agile Business Analytics テスト ユーザーの作成

このセクションの目的は、Birst Agile Business Analytics で Britta Simon というユーザーを作成することです。Birst Agile Business Analytics サポート チームと連携し、Birst アカウントにユーザーを追加してください。


> [AZURE.NOTE] ユーザーを手動で作成する必要がある場合は、Birst Agile Business Analytics のサポート チームにお問い合わせください。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Birst Agile Business Analytics へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200] <br>

**Birst Agile Business Analytics に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。 <br><br>![ユーザーの割り当て][201] <br>

2. アプリケーションの一覧で、**[Birst Agile Business Analytics]** を選択します。 <br><br>![Configure Single Sign-On](./media/active-directory-saas-birst-tutorial/tutorial_birst_50.png) <br>

1. 上部のメニューで **[ユーザー]** をクリックします。<br><br>![ユーザーの割り当て][203] <br>

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br> アクセス パネルで Birst Agile Business Analytics タイルをクリックすると、Birst Agile Business Analytics アプリケーションに自動的にサインオンするはずです。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-birst-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-birst-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-birst-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-birst-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-birst-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-birst-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-birst-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-birst-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-birst-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-birst-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-birst-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-birst-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-birst-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0128_2016-->