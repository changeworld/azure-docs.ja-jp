<properties
    pageTitle="チュートリアル: Azure Active Directory と Halogen Software の統合"
    description="Azure Active Directory と Halogen Software の間でシングル サインオンを構成する方法について説明します。"
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-halogen-software"></a>チュートリアル: Azure Active Directory と Halogen Software の統合

このチュートリアルの目的は、Halogen Software と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Halogen Software と Azure AD の統合には、次の利点があります。 

- Halogen Software にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に Halogen Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件 

Halogen Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン対応の Halogen Software サブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Halogen Software の追加 
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-halogen-software-from-the-gallery"></a>ギャラリーからの Halogen Software の追加
Azure AD への Halogen Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Halogen Software を追加する必要があります。

**ギャラリーから Halogen Software を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。 

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに、「 **halogen software**」と入力します。

    ![アプリケーション][5]

7. 結果ウィンドウで **[Halogen Software]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Halogen Software で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Halogen Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Halogen Software の関連ユーザーの間で、リンクの関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Halogen Software の **[Username]** の値として割り当てます。
 
Halogen Software で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[単一の Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Halogen Software のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Halogen Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-single-sign-on"></a>単一の Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Halogen Software アプリケーションでシングル サインオンを構成することです。


**Halogen Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Halogen Software** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On][8]

2. **[ユーザーの Halogen Software へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Azure AD のシングル サインオン][9]

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行します。 ![アプリケーション設定の構成][10]
 
     a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、*https://global.hgncloud.com/fabrikam/welcome.jsp* のパターンを使用して、ユーザーが Halogen Software アプリケーションへのサインオンに使用する URL を入力します。

     b. **[次へ]** をクリックします。
 
4. **[Halogen Software でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターのローカルに保存します。
    
    ![Azure AD Connect とは][11]

5. 別のブラウザー ウィンドウで、管理者として **Halogen Software** アプリケーションにサインオンします。

6. **[Options]** タブをクリックします。 

    ![What is Azure AD Connect][12]


7. 左のナビゲーション ウィンドウで、 **[SAML Configuration]**をクリックします。 

    ![Azure AD Connect とは][13]

8. **[SAML 構成]** ページで、次の手順を実行します。 ![Azure AD Connect とは][14]

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[Unique Identifier]** で **[NameID]** を選択します。

    b. **[Unique Identifier Maps To]** で **[Username]** を選択します。

    c. ダウンロードしたメタデータ ファイルをアップロードするには、**[Browse]** をクリックしてファイルを選択し、**[Upload File]** をクリックします。

    d. 構成をテストするには、**[Run Test]** をクリックします。 

    > [AZURE.NOTE] "*The SAML test is complete.Please close this window*" というメッセージが表示されるまで待機する必要があります。 次に、開いているブラウザー ウィンドウを閉じます。 **[Enable SAML]** チェック ボックスは、テストが完了した場合にのみ有効にします。

    e. **[Enable SAML]**を選択します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[変更を保存]**をクリックします。 


9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。 

    ![Azure AD Connect とは][15]

10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  

    ![Azure AD Connect とは][16]




### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD Connect とは][100] 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD Connect とは][101] 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 

    ![Azure AD Connect とは][102] 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![What is Azure AD Connect][103] 
 
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。

    b. **[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c. [次へ] をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 

    ![What is Azure AD Connect][104] 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[表示名]** ボックスに、「**Britta Simon**」と入力します。

    d. **[ロール]** 一覧で **[ユーザー]** を選択します。

    e. **[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD Connect とは][105]  

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![What is Azure AD Connect][106]   

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
    b. **[完了]** をクリックします。   
  
 
### <a name="creating-a-halogen-software-test-user"></a>Halogen Software のテスト ユーザーの作成

このセクションの目的は、Halogen Software で Britta Simon というユーザーを作成することです。

**Halogen Software で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 管理者として **Halogen Software** アプリケーションにサインオンします。

2. **[User Center]** タブをクリックし、**[Create User]** をクリックします。

    ![Azure AD Connect とは][300]  

3. **[New User]** ダイアログ ページで、次の手順に従います。

    ![What is Azure AD Connect][301]

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。 
  
    b. **[姓]** ボックスに「**Simon**」と入力します。
  
    c. **[Username]** ボックスに、**Azure クラシック ポータルでの Brita Simon のユーザー名**を入力します。
  
    d. **[Password]** ボックスに Britta のパスワードを入力します。
  
    e. [ **Save**] をクリックします。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Halogen Software へのアクセスを許可することによって、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![What is Azure AD Connect][200]

**Halogen Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![What is Azure AD Connect][201]

2. アプリケーションの一覧で **[Halogen Software]**を選択します。

    ![What is Azure AD Connect][202]

1. 上部のメニューで **[ユーザー]**をクリックします。

    ![Azure AD Connect とは][203]

1. ユーザーの一覧で **[Britta Simon]**を選択します。

    ![Azure AD Connect とは][204]

2. 下部にあるツール バーで **[割り当て]**をクリックします。

    ![Azure AD Connect とは][205]



### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで Halogen Software のタイルをクリックすると、自動的に Halogen Software アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png


<!--HONumber=Oct16_HO2-->


