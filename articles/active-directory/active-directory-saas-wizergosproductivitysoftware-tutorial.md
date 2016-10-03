<properties
	pageTitle="チュートリアル: Azure Active Directory と Wizergos Productivity Software の統合 | Microsoft Azure"
	description="Azure Active Directory と Wizergos Productivity Software の間でシングル サインオンを構成する方法について説明します。"
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


# チュートリアル: Azure Active Directory と Wizergos Productivity Software の統合 

このチュートリアルの目的は、Wizergos Productivity Software と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Wizergos Productivity Software と Azure AD の統合には、次の利点があります。

- Wizergos Productivity Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Wizergos Productivity Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Wizergos Productivity Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Wizergos Productivity Software でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Wizergos Productivity Software の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Wizergos Productivity Software の追加
Azure AD への Wizergos Productivity Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Wizergos Productivity Software を追加する必要があります。

**ギャラリーから Wizergos Productivity Software を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
	
	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。
	
	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**Wizergos Productivity Software**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)

7. 結果ウィンドウで **[Wizergos Productivity Software]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![ギャラリーでアプリを選択する](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Wizergos Productivity Software で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Wizergos Productivity Software ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Wizergos Productivity Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Wizergos Productivity Software の **[Username (ユーザー名)]** の値として割り当てます。

Wizergos Productivity Software で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Wizergos Productivity Software のテスト ユーザーの作成](#creating-a-wizergos-productivity-software-test-user)** - Wizergos Productivity Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Wizergos Productivity Software アプリケーションでシングル サインオンを構成します。

**Wizergos Productivity Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Wizergos Productivity Software** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
	 
	![Configure Single Sign-On][6]

2. **[ユーザーの Wizergos Productivity Software へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
    
	![Configure Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行して **[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)

	a.**[識別子]** ボックスに、「`https://www.wizergos.net`」と入力します。

    b.**[応答 URL]** ボックスに、「`https://www.wizergos.net/register_users/saml2/`」と入力します。

	c.**[次へ]** をクリックします。

4. **[アプリケーション設定の構成]** ダイアログ ページで、**SP 開始モード**でアプリケーションを構成する場合は、**[詳細設定を表示します (オプション)]** をクリックし、**[サインオン URL]** を入力して **[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)

	a.**[サインオン URL]** ボックスに、「`https://www.wizergos.net`」と入力します。

	b.**[次へ]** をクリックします。

5. **[Wizergos Productivity Software でのシングル サインオンの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_06.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。

6. 別の Web ブラウザーのウィンドウで、管理者として Wizergos Productivity Software テナントにサインオンします。

7. ハンバーガー メニューから **[Admin (管理者)]** を選択します。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

8. [Admin (管理者)] ページの左側にあるメニューで **[AUTHENTICATION (認証)]** を選択し、**[Azure AD]** をクリックします。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

9. **[AUTHENTICATION (認証)]** セクションで、次の手順に従います。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)

	a.Azure AD からダウンロードした証明書を開き、証明書の拇印をコピーします。次に、それを **[Certificate ThumbPrint (証明書の拇印)]** ボックスに貼り付けます。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_004.png)

	b.**[Issuer URL (発行者の URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[発行者の URL]** の値を入力します。

	c.**[Signle Sign-On URL (シングル サインオン URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[シングル サインオン サービス URL]** の値を入力します。

	d.**[Signle Sign-Out URL (シングル サインアウト URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[シングル サインアウト サービス URL]** の値を入力します。

	e.**[保存]** ボタンをクリックします。

	> [AZURE.NOTE] 証明書フィンガープリントにスペースを含めることはできませんので注意してください。

7. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
    
	![Azure AD Single Sign-On][10]

8. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
    
	![Azure AD Single Sign-On][11]



### Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### Wizergos Productivity Software のテスト ユーザーの作成

このセクションでは、Wizergos Productivity Software で Britta Simon というユーザーを作成します。Wizergos Productivity Software サポート チーム ([support@wizergos.com](emailTo:support@wizergos.com)) と連携し、Wizergos Productivity Software プラットフォームにユーザーを追加してください。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Wizergos Productivity Software へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。
	
   ![ユーザーの割り当て][200]

**Wizergos Productivity Software に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
    
	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Wizergos Productivity Software]** を選択します。
    
	![Configure Single Sign-On](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。
    
	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。
    
	![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。
 
アクセス パネルで [Wizergos Productivity Software] タイルをクリックすると、自動的に Wizergos Productivity Software アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->