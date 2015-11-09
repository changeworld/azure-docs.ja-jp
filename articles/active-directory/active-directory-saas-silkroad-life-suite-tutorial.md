<properties
	pageTitle="チュートリアル: Azure Active Directory と SilkRoad Life Suite の統合 | Microsoft Azure"
	description="Azure Active Directory と SilkRoad Life Suite の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="markusvi"/>


# チュートリアル: Azure Active Directory と SilkRoad Life Suite の統合

このチュートリアルの目的は、SilkRoad Life Suite と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>SilkRoad Life Suite と Azure AD の統合には、次の利点があります。

- SilkRoad Life Suite にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に SilkRoad Life Suite にサインオン (シングル サインオン) できるようにします。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

SilkRoad Life Suite と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SilkRoad Life Suite でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SilkRoad Life Suite の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの SilkRoad Life Suite の追加
Azure AD への SilkRoad Life Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SilkRoad Life Suite を追加する必要があります。

**ギャラリーから SilkRoad Life Suite を追加するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br> ![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]<br>
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]<br>
5. **[実行する内容]** ダイアログ ボックスで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4]<br>
6. 検索ボックスに、「**SilkRoad Life Suite**」と入力します。<br><br> ![アプリケーション][5]<br>
7. 結果ウィンドウで **[SilkRoad Life]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![アプリケーション][50]<br>


##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、SilkRoad Life Suite で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SilkRoad Life Suite ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと SilkRoad Life Suite の関連ユーザーの間で、リンク関係が確立されている必要があります。<br> このリンク関係は、Azure AD の **[ユーザー名]** の値を、SilkRoad Life Suite の **[Username]** の値として割り当てることで確立されます。
 
SilkRoad Life Suite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[SilkRoad Life Suite のテスト ユーザーの作成](#creating-a-silkroad-life-suite-test-user)** - SilkRoad Life Suite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、SilkRoad Life Suite アプリケーションでシングル サインオンを構成することです。<br>

**SilkRoad Life Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

5. SilkRoad 企業サイトに管理者としてサインオンします。 


    > [AZURE.NOTE]Microsoft Azure AD とフェデレーションを構成するために SilkRoad Life Suite の認証アプリケーションへのアクセス権を取得するには、SilkRoad サポートまたは SilkRoad サービス担当者にお問い合わせください。


6. **[Service Provider]** へ移動して、**[Federation Details]** をクリックします。<br><br>![Azure AD Single Sign-On][10] <br>


1. **[Download Federation Metadata]** をクリックし、メタデータ ファイルをコンピューターに保存します。<br><br>![Azure AD Single Sign-On][11] <br>

3. Azure AD ポータルの **SilkRoad Life Suite** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログ ボックスを開きます。<br><br> ![シングル サインオンの構成][6] <br>

2. **[ユーザーの SilkRoad Life Suite へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Azure AD Single Sign-On][7] <br>

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD Single Sign-On][8] <br>
 
    a.**[サインオン URL]** ボックスに、ユーザーが SilkRoad Life Suite サイトへのサインオンに使用する URL を入力します (例: **https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*))。

    b.ダウンロードした **Silkroad** メタデータ ファイルを開きます。

    c.**AssertionConsumerService** タグを探し、**Location** 属性をコピーします。 <br><br>![Azure AD Single Sign-On][21] <br>
   
    d.**[応答 URL]** テキスト ボックスに値を貼り付けます。
 
    e.**[次へ]** をクリックします。
 
4. **[SilkRoad Life Suite でのシングル サインオンの構成]** ページで、次の手順を実行します。<br><br>![Azure AD Single Sign-On][9] <br>

    a.[証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。




1. **SilkRoad** アプリケーションで、**[Authentication Sources]** をクリックします。<br><br>![Azure AD Single Sign-On][12] <br>



1. **[Add Authentication Source]** をクリックします。<br><br>![Azure AD Single Sign-On][13] <br>



1. **[Add Authentication Source]** セクションで、次の手順に従います。<br><br>![Azure AD Single Sign-On][14] <br>

    a.**[Option 2 - Metadata File]** の下の **[Browse]** をクリックして、ダウンロードしたメタデータ ファイルをアップロードします。

    b.**[Create Identity Provider using File Data]** をクリックします。



1. **[Authentication Sources]** セクションで、**[Edit]** をクリックします。<br><br>![Azure AD Single Sign-On][15] <br>


1. **[Edit Authentication Source]** ダイアログ ボックスで、次の手順を実行します。<br><br>![Azure AD Single Sign-On][16] <br>

    a.**[Enabled]** で **[Yes]** を選択します。

    b.**[IdP Description]** ボックスに構成の説明を入力します (例: *Azure AD の SSO*)。

    c.**[IdP Name]** ボックスに、構成の固有の名前を入力します (例: *Azure SP*)。

    d.**[保存]** をクリックします。


6. その他のすべての認証のソースを無効にします。<br><br>![Azure AD Single Sign-On][17]<br>

7. Azure AD ポータルの **[シングル サインオンの確認]** ページで、**[次へ]** をクリックします。<br><br>![Azure AD Single Sign-On][18]

1. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![Azure AD Single Sign-On][19]


### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。<br> ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![Create Azure AD User][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png) <br> 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. ユーザーの一覧を表示するには、上部のメニューで **[ユーザー]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) <br>
 
4. **[ユーザーの追加]** ダイアログを開くには、下部にあるツール バーで **[ユーザーの追加]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) <br>

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png) <br>

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png) <br>
 
    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br> ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) <br>
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png) <br>
  
    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。

  
 
### SilkRoad Life Suite テスト ユーザーの作成

このセクションの目的は、SilkRoad Life Suite で Britta Simon というユーザーを作成することです。Britta は、Azure AD 内の Britta の **emailaddress** に一致する SSO ID (*AuthParam* と呼ばれることがあります) を持っている必要があります。

**SilkRoad Life Suite で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. SilkRoad Life Suite サポート チームに、Azure AD 内の Britta Simon の **emailaddress** と同じ値の **SSO ID** 属性を持つユーザーを作成することを依頼します。



### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に SilkRoad Life Suite へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![ユーザーの割り当て][200] <br>

**SilkRoad Life Suite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルでアプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br> <br>![ユーザーの割り当て][201]<br>
2. アプリケーションの一覧で **[SilkRoad Life Suite]** を選択します。<br><br>![ユーザーの割り当て][202] <br>
1. 上部のメニューで **[ユーザー]** をクリックします。<br> <br>![ユーザーの割り当て][203]<br>
1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br> アクセス パネルで SilkRoad Life Suite のタイルをクリックすると、自動的に SilkRoad Life Suite アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO1-->