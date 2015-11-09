<properties 
    pageTitle="チュートリアル: Azure Active Directory と SCC LifeCycle の統合 | Microsoft Azure" 
    description="Azure Active Directory で SCC LifeCycle を使用してシングル サインオンや自動化されたプロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と SCC LifeCycle の統合
  
このチュートリアルでは、Azure と SCC LifeCycle の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   SCC LifeCycle でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、SCC LifeCycle に割り当てた Azure AD ユーザーは、SCC LifeCycle 企業サイト (サービス プロバイダーが開始したサインオン) または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  SCC LifeCycle のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "シナリオ")
##SCC LifeCycle のアプリケーション統合の有効化
  
このセクションでは、SCC LifeCycle のアプリケーション統合を有効にする方法を説明します。

###SCC LifeCycle のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**SCC LifeCycle**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[SCC LifeCycle]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで SCC LifeCycle に対する認証を行えるようにする方法について説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **SCC LifeCycle** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "シングル サインオンの構成")

2.  **[ユーザーの SCC LifeCycle へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configure Single Sign-On")

3.  **[アプリ URL の構成]** ページの **[サインオン URL]** テキストボックスに、"**https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" というパターンで、SCC LifeCycle アプリケーションにサインオンするために使用する URLを入力して、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "アプリケーション URL の構成")

4.  **[SCC LifeCycle でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "シングル サインオンの構成")

5.  このメタデータ ファイルを SCC LifeCycle サポート チームに転送します。

    >[AZURE.NOTE]SCC LifeCycle サポート チームがシングル サインオンを有効にする必要があります。

6.  Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが SCC LifeCycle にログインできるようにするには、ユーザーを SCC LifeCycle にプロビジョニングする必要があります。
  
SCC LifeCycle へのユーザー プロビジョニングの構成にあたって必要な操作はありません。割り当て済みユーザーが SCC LifeCycle にログインしようとすると、必要に応じて SCC LifeCycle アカウントが自動的に作成されます。

>[AZURE.NOTE]AAD ユーザー アカウントをプロビジョニングするには、他の SCC LifeCycle ユーザー アカウント作成ツールまたは SCC LifeCycle から提供されている API を使用できます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを SCC LifeCycle に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  SCC LifeCycle アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->