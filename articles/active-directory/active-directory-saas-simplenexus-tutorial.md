<properties 
    pageTitle="チュートリアル: Azure Active Directory と SimpleNexus の統合 | Microsoft Azure" 
    description="Azure Active Directory で SimpleNexus を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と SimpleNexus の統合
  
このチュートリアルの目的は、Azure と SimpleNexus の統合を示すことです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   SimpleNexus でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、SimpleNexus に割り当てた Azure AD ユーザーは、SimpleNexus 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  SimpleNexus のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "シナリオ")
##SimpleNexus のアプリケーション統合の有効化
  
このセクションでは、SimpleNexus のアプリケーション統合を有効にする方法を説明します。

###SimpleNexus のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**simple nexus**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[SimpleNexus]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Simple Nexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Simple Nexus")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、SimpleNexus で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **SimpleNexus** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configure Single Sign-On")

2.  **[ユーザーの SimpleNexus へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページで、**[SimpleNexus サインイン URL]** ボックスに、*https://simplenexus.com/CompanyName\_login*" というパターンで URL を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "アプリケーション URL の構成")

4.  **[SimpleNexus でのシングルサインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタ データファイルを SimpleNexus サポート チームに転送します。

    ![Configure Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configure Single Sign-On")

    >[AZURE.NOTE] シングル サインオンは SimpleNexus サポート チームが有効にする必要があります。

5.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが SimpleNexus にログインできるようにするには、ユーザーを SimpleNexus にプロビジョニングする必要があります。SimpleNexus の場合、プロビジョニングは、テナント管理者が手動で実行するタスクです。

>[AZURE.NOTE] SimpleNexus から提供されている他の SimpleNexus ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを SimpleNexus に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **SimpleNexus** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0921_2016-->