<properties 
    pageTitle="チュートリアル: Azure Active Directory と SuccessFactors の統合 | Microsoft Azure"
    description="Azure Active Directory で SuccessFactors を使用してシングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="06/29/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と SuccessFactors の統合
  
このチュートリアルでは、Azure と SuccessFactors を **サービス プロバイダー (SP) によって開始されたシングル サインオン モード**で統合する方法について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   SP によって開始されたモードで SuccessFactors でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、SuccessFactors に割り当てた Azure AD ユーザーは、SuccessFactors 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  SuccessFactors のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-successfactors-tutorial/IC791135.png "シナリオ")

##SuccessFactors のアプリケーション統合の有効化
  
このセクションでは、SuccessFactors のアプリケーション統合を有効にする方法を説明します。

###SuccessFactors アプリケーションのアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-successfactors-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-successfactors-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-successfactors-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**SuccessFactors**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-successfactors-tutorial/IC791136.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[SuccessFactors]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、SuccessFactors で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。
  
シングル サインオンを構成するには、SuccessFactors サポート チームに連絡する必要があります。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **SuccessFactors** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Configure Single Sign-On")

2.  **[ユーザーの SuccessFactors へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-successfactors-tutorial/IC791140.png "アプリケーション URL の構成")

    1.  **[サインオン URL]** ボックスに、ユーザーが SuccessFactors アプリにサインオンするために使用する URL を入力します (例: "*https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*")。
    2.  **[SuccessFactors 応答 URL]** テキスト ボックスに、「**https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**」と入力します。

        >[AZURE.NOTE] この値は一時的なプレースホルダーです。実際の値は、SuccessFactors サポート チームから取得します。このチュートリアルの中に、SuccessFactors サポート チームに連絡することを指示する手順があります。サポート チームから、実際の SuccessFactors 応答 URL を取得できます。

4.  **[SuccessFactors でのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Configure Single Sign-On")

5.  SAML ベースのシングル サインオンを構成するために、SuccessFactors サポート チームに連絡し、次の項目をチームに提供します。

    1.  ダウンロードした証明書
    2.  リモート ログイン URL
    3.  リモート ログアウト URL

    >[AZURE.IMPORTANT] SuccessFactors サポート チームに、NameId Format パラメーターを *[Unspecified]* に設定するように依頼します。

    Successfactors サポート チームから、**[アプリケーション URL の構成]** ダイアログで必要な、適切な **Successfactors 応答 URL** が送信されます。

6.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが SuccessFactors にログインできるようにするには、ユーザーを SuccessFactors にプロビジョニングする必要があります。SuccessFactors の場合、プロビジョニングは手動で行います。
  
SuccessFactors でユーザーを作成するには、SuccessFactors のサポート チームに連絡する必要があります。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを SuccessFactors に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **SuccessFactors** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-successfactors-tutorial/IC791143.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-successfactors-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

<!---HONumber=AcomDC_0629_2016-->