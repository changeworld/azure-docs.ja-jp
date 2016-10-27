<properties
    pageTitle="チュートリアル: Azure Active Directory と CloudPassage の統合 | Microsoft Azure"
    description="Azure Active Directory と CloudPassage の間でシングル サインオンを構成する方法について説明します。"
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-cloudpassage"></a>チュートリアル: Azure Active Directory と CloudPassage の統合

このチュートリアルの目的は、CloudPassage と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
CloudPassage と Azure AD の統合には、次の利点があります。 

- CloudPassage にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に CloudPassage にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。 


SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件 

CloudPassage と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- CloudPassage でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD のテスト環境がない場合は、 [ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料評価版を入手できます。 

 
## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。  
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの CloudPassage の追加 
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-cloudpassage-from-the-gallery"></a>ギャラリーからの CloudPassage の追加
Azure AD への CloudPassage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に CloudPassage を追加する必要があります。

### <a name="to-add-cloudpassage-from-the-gallery,-perform-the-following-steps:"></a>ギャラリーから CloudPassage を追加するには、次の手順に従います。

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
 
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに、「 **CloudPassage**」と入力します。

    ![アプリケーション][5]

7. 結果ウィンドウで **[CloudPassage]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![アプリケーション][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーを使用して、CloudPassage との Azure AD シングル サインオンを構成してテストする方法を示すことです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する CloudPassage ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと CloudPassage の関連ユーザーの間で、リンク関係が確立されている必要があります。  
このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を CloudPassage の **[Username]** の値として割り当てます。
 
CloudPassage での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[単一の Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[CloudPassage テスト ユーザーの作成](#creating-a-halogen-software-test-user)** - CloudPassage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD クラシック ポータルで Azure AD シングル サインオンを有効にし、CloudPassage アプリケーションでのシングル サインオンを構成することです。  
CloudPassage アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。

![[シングル サインオンの構成]][21]

**CloudPassage との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure AD クラシック ポータルの **CloudPassage** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On][7]

2. **[ユーザーの CloudPassage へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On][8]

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。 

    ![[アプリケーション設定の構成]][9]
 
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、ユーザーが CloudPassage アプリケーションへのサインオンに使用する URL (例: *https://portal.cloudpassage.com/saml/init/accountid*) を入力します。 

    b. **[応答 URL]** ボックスに、AssertionConsumerService URL (例: *https://portal.cloudpassage.com/saml/consume/accountid*) を入力します。 この属性の値は、CloudPassage ポータルの **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで **[SSO Setup documentation (SSO セットアップのドキュメント)]** をクリックすることで取得できます。  
    ![[シングル サインオンの構成]][10]

    C. **[次へ]** をクリックします。



4. **[CloudPassage でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターのローカルに保存します。 

    ![Configure Single Sign-On][11]

5. 別の Web ブラウザーのウィンドウで、管理者として CloudPassage 企業サイトにサインオンします。

6. 上部のメニューの **[Settings (設定)]** をクリックし、**[Site Administration (サイトの管理)]** をクリックします。 

    ![[シングル サインオンの構成]][12]

7. **[Authentication Settings (認証設定)]** タブをクリックします。 

    ![[シングル サインオンの構成]][13]


8. **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで、次の手順に従います。 

    ![[シングル サインオンの構成]][14]


    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 Azure クラシック ポータルで、**[CloudPassage でのシングル サインオンの構成]** ダイアログ ページの **[発行者の URL]** の値をコピーし、**[SAML issuer URL (SAML 発行者の URL)]** ボックスに貼り付けます。

    b. Azure クラシック ポータルの **[CloudPassage でのシングル サインオンの構成]** ダイアログ ページで **[サービス プロバイダー (SP) によって開始されたエンドポイント]** の値をコピーし、**[SAML endpoint URL (SAML エンドポイントの URL)]** テキスト ボックスに貼り付けます。

    c. Azure クラシック ポータルで、**[CloudPassage でのシングル サインオンの構成]** ダイアログ ページの **[ログアウト URL]** の値をコピーし、**[Logout landing page (ログアウト ランディング ページ)]** ボックスに貼り付けます。

    d. ダウンロードした証明書から **base-64** でエンコードされたファイルを作成します。 
          
    >[AZURE.TIP] 詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X.509 Certificate]** ボックスに貼り付けます。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 [ **Save**] をクリックします。


9. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] (シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。 

    ![Configure Single Sign-On][15]


10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。 

    ![[シングル サインオンの構成]][16]



11. 上部のメニューの **[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。 

    ![Configure Single Sign-On][17]

12. 必要なユーザー属性を追加するために、次の表の各行に対して、次の手順を実行します。 

  	| 属性名 | 属性値 |
  	| --- | --- |
  	| firstname | User.givenname |
  	| lastname | User.surname |
  	| email | User.mail |

 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[ユーザー属性の追加]**をクリックします。 

    ![[シングル サインオンの構成]][18]

    b. **[属性名]** ボックスにその行に表示される属性名を入力し、**[属性値]** でその行に表示される属性値を選択します。 

    ![Configure Single Sign-On][19]
     
    c. **[完了]** をクリックします。


13. 下部にあるツール バーの **[変更の適用]**をクリックします。 

    ![[シングル サインオンの構成]][20]



### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 
 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png) 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b. **[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。
  
    c. [次へ] をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png) 
  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
  
    b. **[姓]** ボックスに「**Simon**」と入力します。
  
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
  
    e. **[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png) 
 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png) 
  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
  
    b. **[完了]** をクリックします。   


  
 
### <a name="creating-a-cloudpassage-test-user"></a>CloudPassage テスト ユーザーの作成

このセクションの目的は、CloudPassage で Britta Simon というユーザーを作成することです。

#### <a name="to-create-a-user-called-britta-simon-in-cloudpassage,-perform-the-following-steps:"></a>CloudPassage で Britta Simon というユーザーを作成するには、次の手順に従います。

1.  **CloudPassage** 企業サイトに管理者としてサインオンします。 

2.  上部にあるツールバーの **[Settings (設定)]** をクリックし、**[Site Administration (サイトの管理)]** をクリックします。 

    ![CloudPassage テスト ユーザーの作成][22] 

3.  **[Users (ユーザー)]** タブをクリックし、**[Add New User (新しいユーザーの追加)]** をクリックします。 

    ![CloudPassage テスト ユーザーの作成][23]
    
4.  **[新しいユーザーの追加]** セクションで、次の手順を実行します。 

    ![CloudPassage テスト ユーザーの作成][24]

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「Britta」と入力します。

    b. **[姓]** ボックスに「Simon」と入力します。

    c. **[Username]**、**[Email]**、**[Retype Email]** の各ボックスに、Britta の Azure AD でのユーザー名を入力します。

    d. **[Access Type]** で **[Enable Halo Portal Access]** を選択します。

    e. **[追加]**をクリックします。










### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に CloudPassage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][30]

**Britta Simon を CloudPassage に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![ユーザーの割り当て][26]

2. アプリケーションの一覧で **[CloudPassage]**を選択します。

    ![ユーザーの割り当て][27]

1. 上部のメニューで **[ユーザー]**をクリックします。

    ![ユーザーの割り当て][25]

1. ユーザーの一覧で **[Britta Simon]**を選択します。

2. 下部にあるツール バーで **[割り当て]**をクリックします。

    ![ユーザーの割り当て][29]



### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [CloudPassage] タイルをクリックすると、自動的に CloudPassage アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png
























<!--HONumber=Oct16_HO2-->


