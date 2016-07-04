<properties
	pageTitle="チュートリアル: Azure Active Directory と ADP GlobalView の統合 | Microsoft Azure"
	description="Azure Active Directory と ADP GlobalView の間でシングル サインオンを構成する方法について説明します。"
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
	ms.date="06/17/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と ADP GlobalView の統合

このチュートリアルの目的は、ADP GlobalView と Azure Active Directory (Azure AD) を統合する方法を説明することです。ADP GlobalView と Azure AD の統合には、次の利点があります。

- ADP GlobalView にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に ADP GlobalView にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。


SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Azure AD と ADP GlobalView の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ADP GlobalView でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ADP GlobalView の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの ADP GlobalView の追加
Azure AD への ADP GlobalView の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ADP GlobalView を追加する必要があります。

**ギャラリーから ADP GlobalView を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**ADP GlobalView**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)

7. 結果ウィンドウで **[ADP GlobalView]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、ADP GlobalView で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ADP GlobalView ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと ADP GlobalView の関連ユーザーの間で、リンク関係を確立する必要があります。このリンク関係は、Azure AD の **[ユーザー名]** の値を、ADP GlobalView の **[Username]** の値として割り当てることで確立されます。

ADP GlobalView で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[ADP GlobalView のテスト ユーザーの作成](#creating-a-adp-globalview-test-user)** - ADP GlobalView で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、ADP GlobalView アプリケーションでシングル サインオンを構成することです。

ADP GlobalView アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。次のスクリーンショットはその例です。要求の名前は常に **"PersonImmutableID"** であり、その値はユーザーの EmployeeID を含む ExtensionAttribute2 にマップされています。ここで、Azure AD から ADP GlobalView にマップしているユーザーは EmployeeID では完了となりますが、アプリケーションの設定に基づいて、別の値にマップできます。そのため、ユーザーの正しい ID を使用し、その値を **"PersonImmutableID"** 要求でマップするには、まず ADP GlobalView チームと協力してください。また、電子メールおよびユーザー ID の要求を図に示すようにマップすることができます。
 
![Configure Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png)

SAML アサーションを構成する前に、ADP GlobalView サポート チームに連絡し、テナントの一意識別子属性の値を請求する必要があります。この値は、アプリケーションのカスタム要求を構成するのに必要です。


**ADP GlobalView で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **ADP GlobalView** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

	![Configure Single Sign-On][6]

2. **[ユーザーの ADP GlobalView へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
 
	![Configure Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

	![Configure Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png)


    a.**[識別子]** ボックスに、ADP GlobalView アプリケーションの識別に使用する URL を、**"https://<サーバー名>.globalview.adp.com/federate2"** または **"https://<サーバー名>.globalview.adp.com/federate"** のいずれかの形式で入力します。


    b.**[応答 URL]** ボックスに、ADP GlobalView アプリケーションに応答を投稿するために Azure AD が使用する URL を、**"https://<サーバー名>.globalview.adp.com/federate2/sp/ACS.saml2"** または **"https://<サーバー名>.globalview.adp.com/federate/sp/ACS.saml2"** のいずれかの形式で入力します。

    c.**[次へ]** をクリックします。


4. **[ADP GlobalView でのシングル サインオンの構成]** ページで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


5. アプリケーション用に構成された SSO を入手するには、ADP GlobalView サポート チームに連絡し、次のものを情報として提供してください。

    - ダウンロードした証明書ファイル
    - エンティティ ID
    - SAML SSO URL
    - シングル サインアウト サービス URL


    > [AZURE.NOTE] **ADP GlobalView** チームがインスタンスを構成したら、**RelayState** 値をチームから入手し、この値をアプリケーション インスタンス用に構成するよう Azure AD に要求します。この値、Azure AD テナント ID、およびアプリケーション ID を、Azure AD チーム ([waadpartners@microsoft.com](mailTo:waadpartners@microsoft.com)) に送信できます。この構成の後でのみ、統合をテストすることができ、統合が機能します。このアプリケーションの統合が機能するには、この構成のステップが重要であることに注意してください。

6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

	![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

	![Azure AD Single Sign-On][11]



### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。ユーザーの一覧で **[Britta Simon]** を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順を実行します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### ADP GlobalView テスト ユーザーの作成

このセクションの目的は、ADP GlobalView で Britta Simon というユーザーを作成することです。ADP GlobalView サポート チームと協力し、ADP GlobalView アカウントにユーザーを追加してください。


> [AZURE.NOTE] ユーザーを手動で作成する必要がある場合は、ADP GlobalView のサポート チームにお問い合わせください。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に ADP GlobalView へのアクセスを許可することにより、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**ADP GlobalView に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[ADP GlobalView]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。アクセス パネルで [ADP GlobalView] タイルをクリックすると、自動的に ADP GlobalView アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0622_2016-->