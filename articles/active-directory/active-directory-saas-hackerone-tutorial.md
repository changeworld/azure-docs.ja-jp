<properties
	pageTitle="チュートリアル: Azure Active Directory と HackerOne の統合 | Microsoft Azure"
	description="Azure Active Directory と HackerOne の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="07/09/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と HackerOne の統合

このチュートリアルでは、HackerOne と Azure Active Directory (Azure AD) を統合します。

HackerOne と Azure AD の統合には、次の利点があります。

- HackerOne にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に HackerOne にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Azure AD と HackerOne の統合を構成するには、次のものが必要です。

- Azure サブスクリプション
- HackerOne でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの HackerOne の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの HackerOne の追加
HackerOne を Azure AD に統合するには、ギャラリーから管理対象 SaaS アプリの一覧に HackerOne を追加する必要があります。

**ギャラリーから HackerOne を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

![アプリケーション][4]

6. 検索ボックスに、「**HackerOne**」と入力します。

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. 結果ウィンドウで **[HackerOne]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。


##  Azure AD シングル サインオンの構成とテスト
次に、"Britta Simon" というテスト ユーザーに基づいて、HackerOne で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する HackerOne ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと HackerOne の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係は、Azure AD の **[ユーザー名]** の値を、HackerOne の **[Username]** の値として割り当てることで確立されます。

HackerOne で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[HackerOne のテスト ユーザーの作成](#creating-a-hackerone-test-user)** - Certify で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

次に、クラシック ポータルで Azure AD のシングル サインオンを有効にして、HackerOne アプリケーションでシングル サインオンを構成します。

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**HackerOne で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **HackerOne** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

	![Configure Single Sign-On][6]

2. **[ユーザーの HackerOne へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png)


    a.**[サインオン URL]** ボックスに、ユーザーが HackerOne アプリケーションへのサインオンに使用する URL を入力します。その際、**"https://hackerone.com/<企業名>/authentication"** パターンを使用します。

    b.テナントの URL がわからない場合は、HackerOne サポート チーム ([support@hackerone.com](mailto:support@hackerone.com)) にお問い合わせください。

	c.**[識別子]** ボックスに、テナントの URL を入力します。

	d.**[次へ]** をクリックします。


4. **[HackerOne シングル サインオン パラメーターの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


1. HackerOne テナントに管理者としてサインオンします。

1. 上部のメニューで **[設定]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png)

1. **[Authentication]** に移動し、**[Add SAML settings]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png)


1. **[SAML Settings]** ダイアログ ボックスで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png)

    a.登録されているドメインを **[Email Domain]** ボックスに入力します。

	b.Azure クラシック ポータルで、**[シングル サインオン サービス URL]** をコピーし、[シングル サインオン URL] ボックスに貼り付けます。

    c.ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。

       >[AZURE.TIP] 詳細については、[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o) をご覧ください。
	
    d.base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X509 Certificate]** ボックスに貼り付けます。

    e.**[Save]** をクリックします。


1. [Authentication Settings] ダイアログで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png)

    a.**[Run test]** をクリックします。

    b.**[Status]** フィールドの値が **[Last test status: created]** と同じ場合は、HackerOne サポート チーム ([support@hackerone.com](mailto:support@hackerone.com)) に構成のレビューをご依頼ください。


6. Azure クラシック ポータルで、[single sign-on configuration confirmation] \(シングル サインオンの構成の確認) を選択し、**[次へ]** をクリックします。

	![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
 
	![Azure AD Single Sign-On][11]




### Azure AD のテスト ユーザーの作成

次に、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD で Secure Deliver テスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。


### HackerOne テスト ユーザーの作成

次に、HackerOne で Britta Simon というユーザーを作成します。HackerOne では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。HackerOne にアクセスすると、新しいユーザーが作成されます (まだ作成されていない場合)。[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)

> [AZURE.NOTE] ユーザーを手動で作成する必要がある場合は、Certify のサポート チームにお問い合わせください。




### Azure AD テスト ユーザーの割り当て

次に、Britta Simon に HackerOne へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200]

**HackerOne に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[HackerOne]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]



### シングル サインオンのテスト

最後に、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。アクセス パネルで [HackerOne] タイルをクリックすると、自動的に HackerOne アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->