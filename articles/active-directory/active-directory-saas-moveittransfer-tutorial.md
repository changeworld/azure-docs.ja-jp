<properties
	pageTitle="チュートリアル: Azure Active Directory と MOVEit Transfer の統合 | Microsoft Azure"
	description="Azure Active Directory と MOVEit Transfer の間でシングル サインオンを構成する方法について確認します。"
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
	ms.date="08/04/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と MOVEit Transfer の統合

このチュートリアルの目的は、MOVEit Transfer と Azure Active Directory (Azure AD) を統合する方法を説明することです。

MOVEit Transfer と Azure AD の統合には、次の利点があります。

- MOVEit Transfer にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで MOVEit Transfer に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

Azure AD と MOVEit Transfer の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- MOVEit Transfer でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの MOVEit Transfer の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの MOVEit Transfer の追加
Azure AD への MOVEit Transfer の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MOVEit Transfer を追加する必要があります。

**ギャラリーから MOVEit Transfer を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
	
	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。
	
	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに「**MOVEit Transfer**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_01.png)

7. 結果ウィンドウで **[MOVEit Transfer]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![ギャラリーでアプリを選択する](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_0001.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、MOVEit Transfer で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する MOVEit Transfer ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと MOVEit Transfer の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、MOVEit Transfer の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

MOVEit Transfer で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[MOVEit Transfer のテスト ユーザーの作成](#creating-a-moveit-transfer-test-user)** - MOVEit Transfer で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、MOVEit Transfer アプリケーションでシングル サインオンを構成します。

**MOVEit Transfer で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **MOVEit Transfer** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
	 
	![Configure Single Sign-On][6]

2. **[ユーザーの MOVEit Transfer へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
    
	![Configure Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_04.png)

	a.**[サインオン URL]** ボックスに、独自のドメインのサインイン URL を入力します。

    b.**[識別子]** ボックスに、エンティティ ID URL を入力します。

    c.**[応答 URL]** ボックスに、有効なアサーション コンシューマー インターフェイス URL を入力します。

	d.**[次へ]** をクリックします。

	> [AZURE.NOTE] 実際のサインオン URL と識別子でこれらの値を更新する必要があることに注意してください。これらの値を取得するには、手順 8. で詳細を参照するか、[MOVEit Transfer](https://www.ipswitch.com/support/technical-support) にお問い合わせください。

4. **[MOVEit Transfer でのシングル サインオンの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。

	![Configure Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_05.png)

    a.**[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。

5. MOVEit Transfer テナントに管理者としてサインオンします。

6. 左側のナビゲーション ウィンドウで、**[Settings (設定)]** をクリックします。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

7. **[Security Policies (セキュリティ ポリシー)] の [User Auth (ユーザー認証)]** にある、**[Single Signon (シングル サインオン)]** リンクをクリックします。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

8. メタデータ URL リンクをクリックし、メタデータ ドキュメントをダウンロードします。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)

	- **entityID** が手順 3. の **[識別子]** と一致していることを確認します。
	
	- **AssertionConsumerService** の場所 URL が手順 3. の **[応答 URL]** の値と一致していることを確認します。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

9. **[Add Identity Provider (ID プロバイダーの追加)]** ボタンをクリックして新しいフェデレーション ID プロバイダーを追加します。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

10. **[Browse... (参照)]** をクリックし、手順 4. でダウンロードしたメタデータ ファイルを選択します。次に、**[Add Identity Provider (ID プロバイダーの追加)]** をクリックし、ダウンロードしたファイルをアップロードします。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

11. **[Edit Federated Identity Provider Settings... (フェデレーション ID プロバイダーの設定の編集)]** ページの **[Enabled (有効)]** で **[Yes (はい)]** を選択し、**[Save (保存)]** をクリックします。

	 ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

12. **[Edit Federated Identity Provider User Settings (フェデレーション ID プロバイダー ユーザーの設定の編集)]** ページで次の操作を実行し、**[Save (保存)]** をクリックします。

	a.**[Login name (ログイン名)]** として **[SAML NameID]** を選択します。

	b.**[Full name (氏名)]** として **[Other (その他)]** を選択し、**[Attribute name (属性名)]** ボックスに値「http://schemas.microsoft.com/identity/claims/displayname」を入力します。

	c.**[Email (電子メール)]** として **[Other (その他)]** を選択し、**[Attribute name (属性名)]** ボックスに値「http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress」を入力します。

	d.**[Auto-create account on signon (サインオンのアカウントを自動作成する)]** で **[Yes (はい)]** を選択します。

	e.**[保存]** ボタンをクリックします。

	![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)

13. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
    
	![Azure AD Single Sign-On][10]

14. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
    
	![Azure AD Single Sign-On][11]



### Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
    
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### MOVEit Transfer テスト ユーザーの作成

このセクションの目的は、MOVEit Transfer で Britta Simon というユーザーを作成することです。MOVEit Transfer では、Just-In-Time プロビジョニングがサポートされています。この設定は有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。ユーザーが存在しない場合は、MOVEit Transfer へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

> [AZURE.NOTE] ユーザーを手動で作成する必要がある場合は、MOVEit Transfer のサポート チームにお問い合わせください。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に MOVEit Transfer へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。
	
![ユーザーの割り当て][200]

**MOVEit Transfer に Britta Simon を割り当てるには、次の手順を実行します。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
    
	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[MOVEit Transfer]** を選択します。
    
	![Configure Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_50.png)

3. 上部のメニューで **[ユーザー]** をクリックします。
    
	![ユーザーの割り当て][203]

4. ユーザーの一覧で **[Britta Simon]** を選択します。

5. 下部にあるツール バーで **[割り当て]** をクリックします。
    
	![ユーザーの割り当て][205]

### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。
 
アクセス パネルで MOVEit Transfer のタイルをクリックすると、自動的に MOVEit Transfer アプリケーションにサインオンします。

## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->