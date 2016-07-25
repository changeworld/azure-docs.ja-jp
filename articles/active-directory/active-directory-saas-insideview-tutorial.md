<properties 
    pageTitle="チュートリアル: Azure Active Directory と InsideView の統合 | Microsoft Azure" 
    description="Azure Active Directory で InsideView を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" 
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

#チュートリアル: Azure Active Directory と InsideView の統合
  
このチュートリアルでは、Azure と InsideView の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   InsideView テナント
  
このチュートリアルを完了すると、InsideView に割り当てた Azure AD ユーザーは、InsideView 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  InsideView のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-insideview-tutorial/IC794128.png "シナリオ")
##InsideView のアプリケーション統合の有効化
  
このセクションでは、InsideView のアプリケーション統合を有効にする方法を説明します。

###InsideView のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-insideview-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-insideview-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-insideview-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に「**InsideView**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-insideview-tutorial/IC794129.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[InsideView]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで InsideView に対する認証を行うことができるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **InsideView** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-insideview-tutorial/IC794131.png "シングル サインオンの構成")

2.  **[ユーザーの InsideView へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-insideview-tutorial/IC794132.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページで、**[InsideView 応答 URL]** テキストボックスに InsideView SSO URL (例: `https://my.insideview.com/iv/<STS Name>/login.iv`) を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")

4.  **[InsideView でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-insideview-tutorial/IC794134.png "シングル サインオンの構成")

5.  別の Web ブラウザー ウィンドウで、InsideView 企業サイトに管理者としてログインします。

6.  上部のツールバーで、**[管理者]**、**[シングル サインオン設定]** をクリックし、**[SAML の追加]** をクリックします。

    ![SAML シングル サインオンの設定](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML シングル サインオンの設定")

7.  **[新しい SAML の追加]** セクションで、次の手順を実行します。

    ![新しい SAML の追加](./media/active-directory-saas-insideview-tutorial/IC794136.png "新しい SAML の追加")

    1.  **[STS 名]** テキストボックスに、構成の名前を入力します。
    2.  Azure クラシック ポータルの **[InsideView でのシングル サインオンの構成]** ダイアログ ページで、**[サービス プロバイダー (SP) が開始したエンドポイント]** の値をコピーし、**[SamlP/WS-Fed 未承諾エンドポイント]** テキストボックスに貼り付けます。
    3.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。
        
		>[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」を参照してください。

    4.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[STS 証明書]** テキストボックスに貼り付けます。
    5.  **[Crm ユーザー ID マッピング]** テキストボックスに、「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。
    6.  **[Crm 電子メール マッピング]** テキストボックスに、「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。
    7.  **[Crm ファースト ネーム マッピング]** テキストボックスに、「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**」と入力します。
    8.  **[Crm ラスト ネーム マッピング]** テキストボックスに、「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**」と入力します。
    9.  **[保存]** をクリックします。

8.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-insideview-tutorial/IC794137.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが InsideView にログインできるようにするには、そのユーザーを InsideView にプロビジョニングする必要があります。InsideView の場合、プロビジョニングは手動で行います。
  
InsideView で作成されたユーザーまたは連絡先を取得するには、カスタマー サクセス マネージャーに問い合わせるか、**support@insideview.com** に電子メールを送信してください。

>[AZURE.NOTE] InsideView から提供されている他の InsideView ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを InsideView に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **InsideView** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-insideview-tutorial/IC794138.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

<!---HONumber=AcomDC_0713_2016-->