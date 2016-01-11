<properties
	pageTitle="チュートリアル: Azure Active Directory と O.C. Tanner - AppreciateHub の統合 | Microsoft Azure"
	description="Azure Active Directory と O.C. Tanner - AppreciateHub の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="12/18/2015"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と O.C. Tanner - AppreciateHub の統合

このチュートリアルの目的は、O.C. Tanner - AppreciateHub Azure と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>O.C. Tanner - AppreciateHub と Azure AD の統合には、次の利点があります。

- O.C. Tanner - AppreciateHub にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に O.C. Tanner にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Azure AD と O.C. Tanner - AppreciateHub の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- O.C. Tanner - AppreciateHub のシングル サインオンが有効になっているサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの O.C. Tanner - AppreciateHub の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの O.C. Tanner - AppreciateHub の追加
Azure AD への O.C. Tanner - AppreciateHub の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に O.C. Tanner - AppreciateHub を追加する必要があります。

**ギャラリーから O.C. Tanner - AppreciateHub を追加するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br> ![Active Directory][1] <br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2] <br>

4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3] <br>

5. **[実行する内容]** ダイアログ ボックスで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4] <br>

6. 検索ボックスに、「**O.C.Tanner - AppreciateHub**」と入力します。 <br><br> ![アプリケーション][5] <br>

7. 結果ウィンドウで **[O.C. Tanner - AppreciateHub]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。 <br><br> ![アプリケーション][25] <br>




##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、Azure AD と O.C. Tanner - AppreciateHub のシングル サインオンを構成し、テスト ユーザー "Britta Simon" を使用してテストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する O.C.Tanner - AppreciateHub ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと O.C.Tanner - AppreciateHub の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンク関係は、Azure AD の **[ユーザー名]** の値を、O.C.Tanner - AppreciateHub の **[Username]** の値として割り当てることで確立されます。
 
O.C. Tanner - AppreciateHub で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[O.C. Tanner - AppreciateHub のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - O.C. Tanner - AppreciateHub で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、O.C. Tanner - AppreciateHub アプリケーションでシングル サインオンを構成することです。<br>

**O.C. Tanner - AppreciateHub で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **O.C. Tanner - AppreciateHub** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。<br><br> ![シングル サインオンの構成][6]

2. **[ユーザーの O.C. Tanner - AppreciateHub へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Azure AD Single Sign-On][7]

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![アプリケーションの設定の構成][8]
 
     a.次のリンクを使用してメタデータ ファイルを開きます。[https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata)。

     b.**md:AssertionConsumerService** ノードを探します。

     c.**[Location]** 属性の値をコピーします。 <br><br>![アプリケーションの設定の構成][12]
     
     d.**[サインオン URL]** テキスト ボックスに、前の手順で取得した値を貼り付けます。

     >[AZURE.NOTE]メタデータ ファイルからの応答 URL の取得で問題が発生した場合は、O.C. Tanner - AppreciateHub サポート チーム ([sso@octanner.com](mailto:sso@octanner.com)) に問い合わせてください。

     e.**[次へ]** をクリックします。
 
4. **[O.C. Tanner - AppreciateHub でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターのローカルにメタデータ ファイルを保存します。<br><br>![What is Azure AD Connect][9]

5. O.C. Tanner - AppreciateHub サポート チーム (xyz) に連絡してメタデータ ファイルを提供し、SSO を有効にする必要があることを伝えます。


6. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![What is Azure AD Connect][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![What is Azure AD Connect][11]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Azure AD ユーザーの作成][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、[**Active Directory]** をクリックします。<br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png)
 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png)
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png)
  
    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。

  
 
### O.C. Tanner - AppreciateHub テスト ユーザーの作成

このセクションの目的は、O.C. Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。

**O.C. Tanner - AppreciateHub で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. OC Tanner サポート チームに、Azure AD 内のユーザー名 Britta Simon と同じ値の nameID 属性を持つユーザーを作成することを依頼します。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に O.C. Tanner - AppreciateHub へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200]

**O.C. Tanner - AppreciateHub に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルでアプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br> <br><br>![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[O.C. Tanner - AppreciateHub]** を選択します。 <br><br>![ユーザーの割り当て][202]
1. 上部のメニューで **[ユーザー]** をクリックします。<br> <br><br>![ユーザーの割り当て][203]
1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br> アクセス パネルで [O.C. Tanner - AppreciateHub ] タイルをクリックすると、自動的に O.C. Tanner - AppreciateHub アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1223_2015-->