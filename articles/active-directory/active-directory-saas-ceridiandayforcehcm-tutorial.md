<properties
	pageTitle="チュートリアル: Azure Active Directory と Ceridian Dayforce HCM の統合 | Microsoft Azure"
	description="Azure Active Directory と Ceridian Dayforce HCM の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="06/14/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と Ceridian Dayforce HCM の統合

このチュートリアルの目的は、Ceridian Dayforce HCM と Azure Active Directory (Azure AD) を統合する方法を説明することです。Ceridian Dayforce HCM と Azure AD の統合には、次の利点があります。

- Ceridian Dayforce HCM にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Ceridian Dayforce HCM にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。


SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Ceridian Dayforce HCM と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Ceridian Dayforce HCM でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Ceridian Dayforce HCM の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Ceridian Dayforce HCM の追加
Azure AD への Ceridian Dayforce HCM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Ceridian Dayforce HCM を追加する必要があります。

**ギャラリーから Ceridian Dayforce HCM を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**Ceridian Dayforce HCM**」と入力します。

	![アプリケーション](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_01.png)

7. 結果ウィンドウで **[Ceridian Dayforce HCM]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![アプリケーション](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)


##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Ceridian Dayforce HCM で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Ceridian Dayforce HCM ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Ceridian Dayforce HCM の関連ユーザーの間で、リンク関係が確立されている必要があります。

Ceridian Dayforce HCM で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Ceridian Dayforce HCM のテスト ユーザーの作成](#creating-a-ceridian-dayforce-hcm-test-user)** - Ceridian Dayforce HCM で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Ceridian Dayforce HCM アプリケーションでシングル サインオンを構成することです。

Ceridian Dayforce HCM アプリケーションでは、特定の形式での SAML アサーションが必要です。適切なユーザー ID を識別するには、まず Dayforce HCM チームと協力してください。ユーザー ID として **"name"** 属性を使用することをお勧めします。この属性の値は、**[属性]** タブで管理できます。次のスクリーンショットはその例です。

![Configure Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_02.png)

**Ceridian Dayforce HCM で Azure AD シングル サインオンを構成するには、次の手順に従います。**




1. Azure クラシック ポータルの **[Ceridian Dayforce HCM]** アプリケーション統合ページで、上部のメニューから **[属性]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_81.png)


1. **[SAML トークンの属性]** 一覧で、name 属性を選択して **[編集]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_06.png)


1. **[ユーザー属性の編集]** ダイアログで、次の手順を実行します。
 
    a.**[属性値]** 一覧で、実装で使用するユーザー属性を選択します。たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、**[user.extensionattribute2]** を選択します。

    b.**[完了]** をクリックします。
	



1. 上部のメニューで **[クイック スタート]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)





1. Azure クラシック ポータルの **Ceridian Dayforce HCM** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックします。

	![Configure Single Sign-On][6]

2. **[ユーザーの Ceridian Dayforce HCM へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

	![Configure Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_04.png)


    a.**[サインオン URL]** ボックスに、ユーザーが Ceridian Dayforce HCM アプリケーションへのサインオンに使用する URL を入力します。運用環境では、`https://sso.dayforcehcm.com/DayforcehcmNamespace` の URL 形式を使用します。

	わかりやすくするため、次のように DayforcehcmNamespace をお客様の環境や会社 ID の名前空間に置き換えます。`https://sso.dayforcehcm.com/contoso`
	
	テスト環境では、`https://ssotest.dayforcehcm.com/DayforcehcmNamespace` の URL 形式を使用します。

	b.**[応答 URL]** テキストボックスで、Azure AD が応答を投稿するために使用する URL を入力します。運用環境では、`https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` の URL 形式を使用します。テスト環境では、`https://fs-test.dayforcehcm.com/sp/ACS.saml2` の URL 形式を使用します。
   

4. **[Ceridian Dayforce HCM でのシングル サインオンの構成]** ページで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


5. アプリケーション用に構成された SSO を入手するには、Ceridian Dayforce HCM サポート チームにメールで連絡し、次のものを情報として提供してください。

    - ダウンロードした証明書ファイル
    - **発行者の URL**
    - **SAML SSO URL** 
    - **シングル サインアウト サービス URL** 


6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

	![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

	![Azure AD Single Sign-On][11]



### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順を実行します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### Ceridian Dayforce HCM テスト ユーザーの作成

このセクションの目的は、Ceridian Dayforce HCM で Britta Simon というユーザーを作成することです。

Ceridian Dayforce HCM サポート チームと協力して、Ceridian Dayforce HCM アプリケーションに追加するユーザーを取得して ください。





### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Ceridian Dayforce HCM へのアクセスを許可することにより、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**Ceridian Dayforce HCMに Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルのディレクトリ ビューで、トップ メニューにある **[アプリケーション]** をクリックしてアプリケーション ビューを開きます

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Ceridian Dayforce HCM]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。アクセス パネルで [Ceridian Dayforce HCM] タイルをクリックすると、自動的に Ceridian Dayforce HCM アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0615_2016-->