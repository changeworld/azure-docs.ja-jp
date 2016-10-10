<properties 
    pageTitle="チュートリアル: Azure Active Directory と Rally Software の統合 | Microsoft Azure" 
    description="Azure Active Directory で Rally Software を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Rally Software の統合
  
このチュートリアルでは、Azure と Rally Software の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Rally Software テナント
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Rally Software のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-rally-software-tutorial/IC769525.png "シナリオ")
##Rally Software のアプリケーション統合の有効化
  
このセクションでは、Rally Software のアプリケーション統合を有効にする方法を説明します。

###Rally Software のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-rally-software-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-rally-software-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-rally-software-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**rally**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-rally-software-tutorial/IC769526.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Rally Software]** を選び、**[完了]** をクリックしてアプリケーションを追加します。

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Rally Software に対する認証を行うことができるようにする方法を説明します。この手順の途中で、証明書を Rally Software にアップロードする必要があります。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Rally Software** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configure single sign-on")

2.  **[ユーザーの Rally Software へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選び、**[次へ]** をクリックします。

    ![Microsoft Azure AD シングル サインオン](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD シングル サインオン")

3.  **[アプリ URL の構成]** ページで、**[Rally Software テナント URL]** ボックスに、"*https://\<tenant-name>.rally.com*" というパターンで URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-rally-software-tutorial/IC769529.png "アプリケーション URL の構成")

4.  **[Rally Software でのシングル サインオンの構成]** ページで、[メタデータのダウンロード] をクリックしてメタデータをダウンロードし、コンピューターに保存します。

    ![Configure single sign-on](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configure single sign-on")

5.  **Rally Software** テナントにログインします。

6.  上部にあるツールバーの **[Setup]** をクリックして、**[Subscription]** を選びます。

    ![サブスクリプション](./media/active-directory-saas-rally-software-tutorial/IC769531.png "サブスクリプション")

7.  上部にあるツールバー右側の **[Action]** ボタンをクリックして、**[Edit Subscription]** を選びます。

8.  **[Subscription]** ダイアログ ページで、次の手順に従い、その後 **[Save & Close]** をクリックします。

    ![認証](./media/active-directory-saas-rally-software-tutorial/IC769542.png "認証")

    1.  Authentication のドロップダウン リストから、**[Rally or SSO authentication]** を選びます。
    2.  Azure クラシック ポータルの **[Rally Software でのシングル サインオンの構成]** ダイアログ ページで、**[ID プロバイダーの識別]** の値をコピーして、**[Identity Provider URL]** テキストボックスに貼り付けます。
    3.  Azure クラシック ポータルで、**[Rally Software でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーします。

9.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configure single sign-on")
##ユーザー プロビジョニングの構成
  
AAD ユーザーがサインインできるように、Azure Active Directory ユーザー名を使用して、Rally Software アプリケーションにユーザーをプロビジョニングする必要があります。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  Rally Software テナントにログインします。

2.  **[Setup] > [USERS]** へ移動し、**[+ Add New]** をクリックします。

    ![Users](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Users")

3.  New User テキストボックスに名前を入力し、**[Add with Details]** をクリックします。

4.  **[Create User]** セクションで、次の手順に従います。

    ![ユーザーの作成](./media/active-directory-saas-rally-software-tutorial/IC781040.png "ユーザーの作成")

    1.  **[User Name]** ボックスに、プロビジョニングする Azure AD ユーザーの名前を入力します。
    2.  **[Email Address]** ボックスに、プロビジョニングする Azure AD ユーザーのメール アドレスを入力します。
    3.  **[Save & Close]** をクリックします。

>[AZURE.NOTE]他の Rally Software ユーザー アカウントの作成ツールまたは Rally Software から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Rally Software に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Rally Software** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-rally-software-tutorial/IC769548.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0928_2016-->