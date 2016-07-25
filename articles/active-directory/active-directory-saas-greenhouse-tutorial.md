<properties 
    pageTitle="チュートリアル: Azure Active Directory と Greenhouse の統合 | Microsoft Azure" 
    description="Azure Active Directory で Greenhouse を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Greenhouse の統合
  
このチュートリアルでは、Azure と Greenhouse の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Greenhouse シングル サインオンでのサブスクリプション
  
このチュートリアルを完了すると、Greenhouse に割り当てた Azure AD ユーザーは、Greenhouse 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Greenhouse のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "シナリオ")
##Greenhouse のアプリケーション統合の有効化
  
このセクションでは、Greenhouse のアプリケーション統合を有効にする方法を説明します。

###Greenhouse のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に「**greenhouse**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Greenhouse]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Greenhouse に対する認証を行うことができるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Greenhouse** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configure single sign-on")

2.  **[ユーザーの Greenhouse へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configure single sign-on")

3.  **[アプリケーション URL の構成]** ページで、**[サインオン URL]** ボックスに、"*https://company.greenhouse.io*"パターンの URL を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "アプリケーション URL の構成")

4.  **[Greenhouse でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configure single sign-on")

5.  このメタデータ ファイルを Greenhous サポート チームに転送します。

    >[AZURE.NOTE] Greenhouse サポート チームがシングル サインオンを有効にする必要があります。

6.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Greenhouse にログインできるようにするには、ユーザーを Greenhouse にプロビジョニングする必要があります。Greenhouse の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **Greenhouse** 企業サイトに管理者としてログインします。

2.  上部のメニューで、**[構成]**、**[ユーザー]** の順にクリックします。

    ![Users](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Users")

3.  **[新しいユーザー]** をクリックします。

    ![新しいユーザー](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "新しいユーザー")

4.  **[新しいユーザーの追加]** セクションで、次の手順を実行します。

    ![新しいユーザー名の追加](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "新しいユーザー名の追加")

    1.  **[ユーザー電子メールの入力]** テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール アドレスを入力します。
    2.  **[保存]** をクリックします。
        
		>[AZURE.NOTE] Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE] Greenhouse から提供されている他の Greenhouse ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Greenhouse に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Greenhouse** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0713_2016-->