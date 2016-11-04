---
title: 'チュートリアル: Azure Active Directory と eTouches の統合 | Microsoft Docs'
description: Azure Active Directory と eTouches の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: jeedes

---
# チュートリアル: Azure Active Directory と eTouches の統合
このチュートリアルでは、eTouches と Azure Active Directory (Azure AD) を統合する方法について説明します。

eTouches と Azure AD の統合には、次の利点があります。

* eTouches にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に eTouches にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件
eTouches と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* eTouches でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの eTouches の追加
2. Azure AD シングル サインオンの構成とテスト

## ギャラリーからの eTouches の追加
Azure AD への eTouches の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に eTouches を追加する必要があります。

**ギャラリーから eTouches を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**eTouches**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_01.png)
7. 結果ウィンドウで **[eTouches]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_02.png)

## Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、eTouches で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する eTouches ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと eTouches の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、eTouches の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

eTouches で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[eTouches のテスト ユーザーの作成](#creating-a-predictix-price-reporting-test-user)** - eTouches で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、eTouches アプリケーションでシングル サインオンを構成します。

eTouches アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。このアプリケーションには、次の要求を構成してください。この属性の値は、アプリケーションの **[属性]** タブから管理できます。次のスクリーンショットはその例です。

![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_07.png)

**eTouches で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **eTouches** アプリケーション統合ページで、上部のメニューから **[属性]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_general_80.png)
2. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。
   
   | 属性名 | 属性値 |
   | --- | --- |
   | 電子メール |User.mail |
   
    a.**[ユーザー属性の追加]** をクリックして **[ユーザー属性の追加]** ダイアログを開きます。
   
    ![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_general_81.png)

    b.**[属性名]** ボックスに、その行に対して表示される属性名を入力します。

    c.**[属性値]** リストから、その行に対して表示される属性値を選択します。

    d.**[完了]** をクリックします。


1. クラシック ポータルの **eTouches** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6]
2. **[ユーザーの eTouches へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_03.png)
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_04.png)
   
    a.**[サインオン URL]** ボックスに、**https://www.eiseverywhere.com/saml/accounts/?sso&accountid=\<アカウント ID>** というパターンを使用して、ユーザーが eTouches アプリケーションへのサインオンに使用する URL を入力します。
   
    b. **[次へ]** をクリックします。
4. **[eTouches でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_05.png)
   
    a.**[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b.**[次へ]** をクリックします。
5. お使いのアプリケーション用に構成された SSO を取得するには、eTouches アプリケーションで次の手順を実行します。
   
    a.管理者権限を使用して **eTouches** アプリケーションにログインします。
   
    b.**[SAML Configuration (SAML の構成)]** に移動します。
   
    c.**[General Settings (全般設定)]** セクションで、Azure AD のフェデレーション メタデータの内容をテキスト ボックスに貼り付けます。
   
    d.**[Save & Stay (保存のみ)]** ボタンをクリックします。
   
    e.[SAML Metadata (SAML メタデータ)] セクションの **[Update Metadata (メタデータの更新)]** をクリックします。
   
    f.ページが開き、SSO が実行されます。SSO が実行されたら、ユーザー名を設定できます。
   
    g.**[Username (ユーザー名)]** フィールドで、次の画像に示すように **emailaddress** を選択します。
   
    h.**[SSO URL / ACS]** の値をコピーし、Azure AD アプリケーションの構成ウィザードの [サインオン URL] ボックスに貼り付けます。
   
    ![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png)
6. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
   
    ![Azure AD Single Sign-On][10]
7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

    ![Azure AD Single Sign-On][11]


### Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_05.png)
   
    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。
   
    c.**[次へ]** をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_06.png)
   
   a.**[名]** ボックスに「**Britta**」と入力します。
   
   b.**[姓]** ボックスに「**Simon**」と入力します。
   
   c.**[表示名]** ボックスに「**Britta Simon**」と入力します。
   
   d.**[ロール]** 一覧で **[ユーザー]** を選択します。
   
   e.**[次へ]** をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_08.png)
   
    a.**[新しいパスワード]** の値を書き留めます。
   
    b.**[完了]** をクリックします。

### eTouches のテスト ユーザーの作成
このセクションでは、eTouches で Britta Simon というユーザーを作成します。eTouches サポート チームと連携し、eTouches プラットフォームにユーザーを追加してください。

### Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に eTouches へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200]

**eTouches に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[eTouches]** を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_50.png)
3. 上部のメニューで **[ユーザー]** をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]** を選択します。
5. 下部にあるツール バーで **[割り当て]** をクリックします。
   
    ![ユーザーの割り当て][205]

### シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで eTouches のタイルをクリックすると、自動的に eTouches アプリケーションにサインオンします。

## その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0803_2016-->