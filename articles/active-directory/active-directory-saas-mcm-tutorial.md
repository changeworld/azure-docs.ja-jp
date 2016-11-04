---
title: 'チュートリアル: Azure Active Directory と MCM の統合 | Microsoft Docs'
description: Azure Active Directory で MCM を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/30/2016
ms.author: jeedes

---
# チュートリアル: Azure Active Directory と MCM の統合
このチュートリアルの目的は、MCM と Azure Active Directory (Azure AD) を統合する方法を説明することです。

MCM と Azure AD の統合には、次の利点があります。

* MCM にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで MCM に自動的にサインオン (シングル サインオン) する機能を有効にすることができます。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件
MCM と Azure AD の統合を構成するには、次のものが必要です。

* 有効な Azure サブスクリプション
* MCM でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの MCM の追加
2. Azure AD シングル サインオンの構成とテスト

## ギャラリーからの MCM の追加
Azure AD への MCM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MCM を追加する必要があります。

**ギャラリーから MCM を追加するには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
   ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "アプリケーション")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "アプリケーションの追加")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "ギャラリーからのアプリケーションの追加")
6. **検索ボックス**に「**MCM**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "アプリケーション ギャラリー")
7. 結果ウィンドウで **[MCM]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

## Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、MCM で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する MCM ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと MCM の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の**ユーザー名**の値を MCM の **[Username (ユーザー名)]** の値として割り当てます。

MCM で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD テスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
3. **[MCM のテスト ユーザーの作成](#creating-a-mcm-test-user)** - MCM で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、MCM アプリケーションでシングル サインオンを構成します。

**MCM で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **MCM** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![Configure single sign-on](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configure single sign-on")
2. **[ユーザーの MCM へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Microsoft Azure AD シングル サインオン](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD シングル サインオン")
3. [アプリケーション設定の構成] ダイアログ ページで、次の手順に従います。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "アプリケーション URL の構成")
   
   a.**[サインオン URL]** ボックスに、「`https://myaba.co.uk/client-access/<company name>/saml.php`」と入力します。
   
   b. **[次へ]** をクリックします
4. **[MCM でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックして証明書ファイルをダウンロードし、コンピューターのローカルに保存します。
   
   ![Configure Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configure Single Sign-On")
5. アプリケーション用に構成された SSO を入手するために、MCM のサポート チームに問い合わせます。MCM チーム側で SSO を設定する必要があるため、ダウンロードしたメタデータ ファイルを添付して、チームと共有してください。
6. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
   
   ![Configure Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configure Single Sign-On")
7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configure Single Sign-On")

### Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)
   
    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。
   
    c.**[次へ]** をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)
   
   a.**[名]** ボックスに「**Britta**」と入力します。
   
   b.**[姓]** ボックスに「**Simon**」と入力します。
   
   c.**[表示名]** ボックスに「**Britta Simon**」と入力します。
   
   d.**[ロール]** 一覧で **[ユーザー]** を選択します。
   
   e.**[次へ]** をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)
   
    a.**[新しいパスワード]** の値を書き留めます。
   
    b.**[完了]** をクリックします。

### MCM テスト ユーザーの作成
このセクションでは、MCM で Britta Simon というユーザーを作成します。MCM サポート チームと連携し、MCM プラットフォームにユーザーを追加してください。

> [!NOTE]
> MCM から提供されている他の MCM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

### Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に MCM へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "ユーザーの割り当て")

**MCM に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "ユーザーの割り当て")
2. アプリケーションの一覧で **[MCM]** を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)
3. 上部のメニューで **[ユーザー]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "ユーザーの割り当て")
4. ユーザーの一覧で **[Britta Simon]** を選択します。
5. 下部にあるツール バーで **[割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "ユーザーの割り当て")

### シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで MCM のタイルをクリックすると、MCM アプリケーションに自動的にサインオンします。

## その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0907_2016-->