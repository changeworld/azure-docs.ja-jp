<properties 
    pageTitle="チュートリアル: Azure Active Directory と xMatters OnDemand の統合 | Microsoft Azure"
    description="Azure Active Directory で xMatters OnDemand を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="06/20/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と xMatters OnDemand の統合
  
このチュートリアルでは、Azure と xMatters OnDemand の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   xMatters OnDemand テナント
  
このチュートリアルを完了すると、xMatters OnDemand に割り当てた Azure AD ユーザーは、xMatters OnDemand 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  xMatters OnDemand のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "シナリオ")

##xMatters OnDemand のアプリケーション統合の有効化
  
このセクションでは、xMatters OnDemand のアプリケーション統合を有効にする方法を説明します。

###xMatters OnDemand のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**xMatters OnDemand**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[xMatters OnDemand]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで xMatters OnDemand に対する認証を行うことができるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **xMatters OnDemand** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configure single sign-on")

2.  **[ユーザーの xMatters OnDemand へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configure single sign-on")

3.  **[アプリケーション URL の構成]** ページで、次の手順を実行します。

    ![アプリケーション URL の構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "アプリケーション URL の構成")

	a.**[xMatters OnDemand サインイン URL]** ボックスに、`https://<tenant-name>.XMattersOnDemandapp.com` という形式で URL を入力します。

	b.**[次へ]** をクリックします。


4.  **[xMatters OnDemand でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、証明書ファイルをローカルで **c:\\XMatters OnDemand.cer** としてコンピューターに保存します。

    >[AZURE.IMPORTANT] XMatters サポート チームに証明書を転送する必要があります。シングル サインオンの構成を確定するには、その前に、xMatters サポート チームによって証明書がアップロードされる必要があります。

    ![シングル サインオンの有効化](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "シングル サインオンの有効化")

5.  別の Web ブラウザーのウィンドウで、XMatters OnDemand の企業サイトに管理者としてログインします。

6.  上部のツールバーで、**[管理者]** をクリックし、左側にあるナビゲーション バーで **[会社の詳細]** をクリックします。

    ![管理者](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "管理者")

7.  **[SAML 構成]** ページで、次の手順を実行します。

    ![SAML の構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML の構成")

    1.  **[Enable SAML]** を選択します。
    2.  Azure クラシック ポータルの **[XMatters OnDemand でのシングル サインオンの構成]** ダイアログ ページで、**[ID プロバイダーの ID]** 値をコピーして、**[ID プロバイダーの URL]** テキスト ボックスに貼り付けます。
    3.  Azure クラシック ポータルの **[XMatters OnDemand でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[シングル サインオン サービス URL]** テキスト ボックスに貼り付けます。
    4.  Azure クラシック ポータルの **[XMatters OnDemand でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインアウト サービス URL]** の値をコピーし、**[シングル ログアウト URL]** テキスト ボックスに貼り付けます。
    5.  [会社の詳細] ページで、上部にある **[変更の保存]** をクリックします。![会社の詳細情報](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "会社の詳細情報")

8.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの有効化](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "シングル サインオンの有効化")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが XMatters OnDemand にログインできるようにするには、そのユーザーを XMatters OnDemand にプロビジョニングする必要があります。XMatters OnDemand の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **XMatters OnDemand** テナントにログインします。

2.  **[ユーザー]** タブをクリックします。

3.  **[ユーザーの追加]** をクリックします。

    ![ユーザー](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "ユーザー")

4.  **[アクティブ]** を選択します。

5.  **[ユーザーの追加]** セクションで、次の手順に従います。

    ![ユーザーの追加](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "ユーザーの追加")

    1.  プロビジョニングする有効な AAD アカウントの**ユーザー ID**、**名**、**姓**、**サイト**を入力します。
    2.  **[保存]** をクリックします。

>[AZURE.NOTE] XMatters OnDemand から提供されている他の XMatters OnDemand ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを XMatters OnDemand に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **XMatters OnDemand** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0622_2016-->