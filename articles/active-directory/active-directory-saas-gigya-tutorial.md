<properties 
    pageTitle="チュートリアル: Azure Active Directory と Gigya の統合 | Microsoft Azure" 
    description="Azure Active Directory で Gigya を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Gigya の統合
  
このチュートリアルでは、Azure と Gigya の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Gigya でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、Gigya に割り当てた Azure AD ユーザーは、Gigya 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Gigya のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![Configure Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configure Single Sign-On")
##Gigya のアプリケーション統合の有効化
  
このセクションでは、Gigya のアプリケーション統合を有効にする方法について説明します。

###Gigya のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-gigya-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-gigya-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-gigya-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Gigya**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-gigya-tutorial/IC789513.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Gigya]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Gigya に対する認証を行うことができるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Gigya** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configure Single Sign-On")

2.  **[ユーザーの Gigya へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページの **[Gigya サインオン URL]** ボックスに、"*http://company.gigya.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-gigya-tutorial/IC789530.png "アプリケーション URL の構成")

4.  **[Gigya でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Gigya 企業サイトに管理者としてログインします。

6.  **[設定]、[SAML ログイン]** の順にクリックし、**[追加]** をクリックします。

    ![SAML のログイン](./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML のログイン")

7.  **[SAML ログイン]** セクションで、次の手順を実行します。

    ![SAML の構成](./media/active-directory-saas-gigya-tutorial/IC789533.png "SAML の構成")

    1.  [**名前**] テキスト ボックスに、構成の名前を入力します。
    2.  Azure クラシック ポータルの **[Gigya でのシングル サインオンの構成]** ダイアログ ページで、**[発行者の URL]** の値をコピーし、**[Issuer (発行者)]** ボックスに貼り付けます。
    3.  Azure クラシック ポータルの **[Gigya でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[Single Sign-On Service URL (シングル サインオン サービス URL)]** ボックスに貼り付けます。
    4.  Azure クラシック ポータルの **[Gigya でのシングル サインオンの構成]** ダイアログ ページで、**[名前識別子形式]** の値をコピーし、**[Name ID Format (名前識別子形式)]** ボックスに貼り付けます。
    5.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。
        
		>[AZURE.TIP]詳細については、[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o) をご覧ください。

    6.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X.509 証明書]** テキストボックスに貼り付けます。
    7.  **[設定の保存]** をクリックします。

8.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Gigya にログインできるようにするには、ユーザーを Gigya にプロビジョニングする必要があります。Gigya の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **Gigya** 企業サイトに管理者としてログインします。

2.  **[管理者]、[ユーザーの管理]** の順にクリックし、**[ユーザーの招待]** をクリックします。

    ![Manage Users](./media/active-directory-saas-gigya-tutorial/IC789535.png "Manage Users")

3.  [ユーザーの招待] ダイアログで、次の手順を実行します。

    ![ユーザーの招待](./media/active-directory-saas-gigya-tutorial/IC789536.png "ユーザーの招待")

    1.  **[電子メール]** テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール エイリアスを入力します。
    2.  **[ユーザーの招待]** をクリックします。
    
        >[AZURE.NOTE] Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE] Gigya から提供されている他の Gigya ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Gigya に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Gigya** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-gigya-tutorial/IC789537.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-gigya-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。

<!---HONumber=AcomDC_0907_2016-->