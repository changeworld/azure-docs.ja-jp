---
title: 'チュートリアル: Azure Active Directory と Heroku の統合 | Microsoft Docs'
description: Azure Active Directory と Heroku の間でシングル サインオンを構成する方法について説明します。
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
ms.date: 07/11/2016
ms.author: jeedes

---
# チュートリアル: Azure Active Directory と Heroku の統合
このチュートリアルでは、Heroku と Azure Active Directory (Azure AD) を統合する方法について説明します。

Heroku と Azure AD の統合には、次の利点があります。

* Heroku にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Heroku にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件
Heroku と Azure AD の統合を構成するには、次のものが必要です。

* Azure サブスクリプション
* Heroku でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Heroku を追加する
2. Azure AD シングル サインオンの構成とテスト

## ギャラリーから Heroku を追加する
Azure AD への Heroku の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Heroku を追加する必要があります。

**ギャラリーから Heroku を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**Box**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_01.png)
7. 結果ウィンドウで **[Heroku]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_02.png)

## Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Heroku で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Heroku ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Heroku の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係は、Azure AD の **[ユーザー名]** の値を、Heroku の **[Username]** の値として割り当てることで確立されます。

Heroku で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Heroku のテスト ユーザーの作成](#creating-an-heroku-test-user)** - Heroku で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Heroku アプリケーションでシングル サインオンを構成します。

**Heroku で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Heroku** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6]
2. **[ユーザーの Heroku へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_03.png)
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_04.png)
   
   > [!NOTE]
   > サインオン URL と識別子 URL の適切な値がわからない場合、値を取得する方法については、「[Heroku で SSO を有効にするには、次の手順に従います](#x123)」をご覧ください。
   > 
   > 

    a.**[サインオン URL]** ボックスには、**"https://sso.heroku.com/saml/<企業名>/init"** の形式で、ユーザーが Heroku アプリケーションへのサインオンに使用する URL を入力します。

    b.**[識別子]** ボックスに、"**https://sso.heroku.com/saml/\<企業名>**" の形式で URL を入力します。

    c.**[次へ]** をクリックします。


1. **[Heroku でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_05.png)
   
    a.**[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b.**[次へ]** をクリックします。
2. Heroku で SSO を有効にするには、次の手順に従います。
   
    a.Heroku アカウントに管理者としてログインします。
   
    b.**[設定]** タブをクリックします。
   
    c.**[Single Sign On Page]** で、**[Upload Metadata]** をクリックします。
   
    d.Azure クラシック ポータルからダウンロードしたメタデータ ファイルをアップロードします。
   
    e.セットアップが成功すると、管理者に確認のダイアログ ボックスと、エンドユーザー用の SSO ログインの URL が表示されます。
   
    f.<a name="x123"></a>**Heroku のログイン URL** と **Heroku エンティティ ID** をコピーして、Azure AD クラシック ポータルの **[アプリケーション設定の構成]** ページに戻り、関連するテキスト ボックスに値を貼り付けます。

    ![Configure Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png)

    g.**[次へ]** をクリックします。

1. シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
   
    ![Azure AD Single Sign-On][10]
2. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
   
    ![Azure AD Single Sign-On][11]

### Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_05.png)
   
    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。
   
    c.**[次へ]** をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順を実行します。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_06.png)
   
   a.**[名]** ボックスに「**Britta**」と入力します。
   
   b.**[姓]** ボックスに「**Simon**」と入力します。
   
   c.**[表示名]** ボックスに「**Britta Simon**」と入力します。
   
   d.**[ロール]** 一覧で **[ユーザー]** を選択します。
   
   e.**[次へ]** をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_08.png)
   
    a.**[新しいパスワード]** の値を書き留めます。
   
    b.**[完了]** をクリックします。

### Heroku テスト ユーザーの作成
このセクションでは、Heroku で Britta Simon というユーザーを作成します。Heroku では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。ユーザーがまだ存在しない場合は、Heroku へのアクセス時に新しいユーザーが作成されます。アカウントがプロビジョニングされると、確認の電子メールが送信されます。エンドユーザーはそこに記載された受信確認のリンクをクリックする必要があります。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、Heroku のサポート チームにお問い合わせください。
> 
> 

### Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Heroku へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200]

**Heroku に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[Heroku]** を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_50.png)
3. 上部のメニューで **[ユーザー]** をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]** を選択します。
5. 下部にあるツール バーで **[割り当て]** をクリックします。
   
    ![ユーザーの割り当て][205]

### シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。アクセス パネルで [Heroku] タイルをクリックすると、自動的に Heroku アプリケーションにサインオンします。

## その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->