<properties 
    pageTitle="チュートリアル: Azure Active Directory と LogicMonitor の統合 | Microsoft Azure" 
    description="Azure Active Directory で LogicMonitor を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と LogicMonitor の統合
  
このチュートリアルの目的は、Azure と LogicMonitor の統合を示すことです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   LogicMonitor テナント
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  LogicMonitor のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "シナリオ")
##LogicMonitor のアプリケーション統合の有効化
  
このセクションでは、LogicMonitor のアプリケーション統合を有効にする方法を説明します。

###LogicMonitor のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**logicmonitor**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[LogicMonitor]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで LogicMonitor に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **LogicMonitor** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "シングル サインオンの構成")

2.  **[ユーザーの LogicMonitor へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[サインオン URL]** ボックスに、ユーザーが LogicMonitor へのサインオンに使用する URL (例: "**http://company.logicmonitor.com*")) を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "アプリケーション URL の構成")

4.  **[LogicMonitor でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタデータをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "シングル サインオンの構成")

5.  **LogicMonitor** 企業サイトに管理者としてログインします。

6.  上部のメニューで **[Settings]** をクリックします。

    ![設定](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "設定")

7.  左側にあるナビゲーション バーで、**[Single Sign On]** をクリックします。

    ![シングル サインオン](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "シングル サインオン")

8.  **[Single Sign-on (SSO) settings] ** セクションで、次の手順を実行します。

    ![シングル サインオンの設定](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "シングル サインオンの設定")

    1.  **[Enable Single Sign-On]** を選択します。
    2.  **[Default Role Assignment]** で、**[readonly]** を選択します。
    3.  ダウンロードしたメタデータ ファイルをメモ帳で開き、ファイルの内容を **[Identity Provider Metadata]** ボックスに貼り付けます。
    4.  **[変更を保存]** をクリックします。

9.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
AAD ユーザーがサインインできるように、Azure Active Directory ユーザー名を使用して、LogicMonitor アプリケーションにユーザーをプロビジョニングする必要があります。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  LogicMonitor の企業サイトに管理者としてログインします。

2.  上部のメニューで、**[Settings]**、**[Roles and Users]** の順にクリックします。

    ![ロールとユーザー](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "ロールとユーザー")

3.  **[追加]** をクリックします。

4.  **[Add an account]** セクションで、次の手順を実行します。

    ![アカウントの追加](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "アカウントの追加")

    1.  対応するテキストボックスに、プロビジョニングする Azure Active Directory ユーザーの**ユーザー名**、**電子メール**、**パスワード**、**パスワードの再入力**の値を入力します。
    2.  **[Roles]**、**[View Permissions]**、**[Status]** をそれぞれ選択します。
    3.  **[Submit]** をクリックします。

>[AZURE.NOTE]LogicMonitor から提供されている他の LogicMonitor ユーザー アカウント作成ツールや API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを LogicMonitor に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  ****LogicMonitor**** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]**の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->