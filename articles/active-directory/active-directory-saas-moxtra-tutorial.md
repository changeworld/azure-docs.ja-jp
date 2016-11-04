---
title: 'チュートリアル: Azure Active Directory と Moxtra の統合 | Microsoft Docs'
description: Azure Active Directory と Moxtra の間でシングル サインオンを構成する方法について説明します。
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
ms.date: 08/05/2016
ms.author: jeedes

---
# チュートリアル: Azure Active Directory と Moxtra の統合
このチュートリアルの目的は、Moxtra と Azure Active Directory (Azure AD) を統合する方法を説明することです。Moxtra と Azure AD の統合には、次の利点があります。

* Moxtra にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Moxtra にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件
Moxtra と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Moxtra でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。 <br>
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Moxtra を追加する
2. Azure AD シングル サインオンの構成とテスト

## ギャラリーから Moxtra を追加する
Azure AD への Moxtra の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Moxtra を追加する必要があります。

**ギャラリーから Moxtra を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**Moxtra**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)
7. 結果ウィンドウで **[Moxtra]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_02.png)

## Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Moxtra で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Moxtra ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Moxtra の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係は、Azure AD の **[ユーザー名]** の値を、Moxtra の **[Username]** の値として割り当てることで確立されます。

Moxtra で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Moxtra のテスト ユーザーの作成](#creating-a-moxtra-test-user)** - Moxtra で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Moxtra アプリケーションでシングル サインオンを構成することです。

Moxtra アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。次のスクリーンショットはその例です。

![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png)

**Moxtra で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Moxtra** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6]
2. **[ユーザーの Moxtra へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png)
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png)
   
    a.**[サインオン URL]** テキストボックスに、URL **https://www.moxtra.com/service/#login** を入力します。
   
    b.**[次へ]** をクリックします。
4. **[Moxtra でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png)
   
    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b.**[次へ]** をクリックします。
5. 別の Web ブラウザー ウィンドウで、管理者として Moxtra 企業サイトにサインオンします。
6. 左のツールバーで、**[Admin Console (管理コンソール)]、[SAML Single Sign-on (SAML シングル サインオン)]** の順にクリックし、**[New (新規)]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png)
7. **[SAML]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)
   
    a.**[Name]** ボックスに、構成の名前を入力します (例: *SAML*)。
   
    b.Azure クラシック ポータルで、**[Moxtra でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[IdP Entity ID (IdP エンティティ ID)]** ボックスに貼り付けます。
   
    c.Azure クラシック ポータルで、**[Moxtra でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[Login URL (ログイン URL)]** ボックスに貼り付けます。
   
    d.**[AuthnContextClassRef]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:ac:classes:Password**」と入力します。
   
    e.Azure クラシック ポータルの **[Moxtra でのシングル サインオンの構成]** ダイアログ ページで、**[名前識別子形式]** の値をコピーし、**[NameID Format (名前 ID の形式)]** ボックスに貼り付けます。
   
    f.ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[Certificate]** ボックスに貼り付けます。
   
    g.SAML 電子メール ドメイン テキストボックスに、SAML 電子メール ドメインを入力します。
   
   > [!NOTE]
   > ドメインを検証するための手順を確認するには、下の **i** をクリックします。
   > 
   > 

    h.[**更新**] をクリックします。


1. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
   
    ![Azure AD Single Sign-On][10]
2. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
   
    ![Azure AD のシングル サインオン][11]
3. カスタム属性マッピングを SAML トークン属性構成に追加するには、上部のメニューで、**[属性]** をクリックし、**[SAML トークン属性]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png)
4. 下の表のデータ行ごとに、次の手順を実行します。
   
   | 属性名 | 属性値 |
   | --- | --- |
   | firstname |givenname |
   | lastname |surname |
   | idpid |*\<Azure ポータルの [Moxtra でのシングル サインオンの構成] ダイアログの [エンティティ ID] 値 \>* |

    a.[ユーザー属性の追加] をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png)

    b.**[ユーザー属性の追加]** ダイアログで、テーブルのその行に表示されている属性名と属性値を入力します。

    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png)

    c.**[完了]** をクリックします。



1. **[変更の適用]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png)

### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png)
   
    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。
   
    c.**[次へ]** をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png)
   
   a.**[名]** ボックスに「**Britta**」と入力します。
   
   b.**[姓]** ボックスに「**Simon**」と入力します。
   
   c.**[表示名]** ボックスに「**Britta Simon**」と入力します。
   
   d.**[ロール]** 一覧で **[ユーザー]** を選択します。
   
   e.**[次へ]** をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png)
   
    a.**[新しいパスワード]** の値を書き留めます。
   
    b.**[完了]** をクリックします。

### Moxtra テスト ユーザーの作成
このセクションの目的は、Moxtra で Britta Simon というユーザーを作成することです。

**Moxtra で Britta Simon というユーザーを作成するには、次の手順を実行します。**

1. Moxtra 企業サイトに管理者としてサインオンします。
2. 左のツールバーで、**[Admin Console (管理コンソール)]、[User Management (ユーザー管理)]** の順にクリックし、**[Add User (ユーザーの追加)]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png)
3. **[Add User]** ダイアログで、次の手順を実行します。
   
    a.**[名]** ボックスに「**Britta**」と入力します。
   
    b.**[Last Name]** ボックスに「**Simon**」と入力します。
   
    c.**[Email]** ボックスに、Britta の Azure クラシックポータルの電子メール アドレスを入力します。
   
    d.**[事業部]** テキストボックスに、「**Dev**」と入力します。
   
    e.**[Department]** テキストボックスに、「**IT**」と入力します。
   
    f.**[Adminitrator]** を選択します。
   
    g.**[追加]** をクリックします。

### Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Moxtra へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**Moxtra に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[Moxtra]** を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png)
3. 上部のメニューで **[ユーザー]** をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]** を選択します。
5. 下部にあるツール バーで **[割り当て]** をクリックします。
   
    ![ユーザーの割り当て][205]

### シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。アクセス パネルで Moxtra のタイルをクリックすると、自動的に Moxtra アプリケーションにサインオンします。

## その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->