<properties 
    pageTitle="チュートリアル: Azure Active Directory と TimeOffManager の統合 | Microsoft Azure" 
    description="Azure Active Directory で TimeOffManager を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-active-directory-integration-with-timeoffmanager"></a>チュートリアル: Azure Active Directory と TimeOffManager の統合
  
このチュートリアルでは、Azure と TimeOffManager の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   TimeOffManager でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、TimeOffManager に割り当てた Azure AD ユーザーは、TimeOffManager 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  TimeOffManager のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Scenario")

##<a name="enabling-the-application-integration-for-timeoffmanager"></a>TimeOffManager のアプリケーション統合の有効化
  
このセクションでは、TimeOffManager のアプリケーション統合を有効にする方法を説明します。

###<a name="to-enable-the-application-integration-for-timeoffmanager,-perform-the-following-steps:"></a>TimeOffManager のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。

    ![[Active Directory]](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![[アプリケーションの追加]](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に、「**TimeOffManager**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Application Gallery")

7.  結果ウィンドウで **[TimeOffManager]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

##<a name="configuring-single-sign-on"></a>シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで TimeOffManager に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書を TimeOffManager テナントにアップロードする必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>シングル サインオンを構成するには、次の手順を実行します。

1.  Azure クラシック ポータルの **TimeOffManager** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configure Single Sign-On")

2.  **[ユーザーの TimeOffManager へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページで、**[TimeOffManager 応答 URL]** ボックスに、TimeOffManager の AssertionConsumerService URL (例: "*Example: https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*") を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configure App URL")

    応答 URL は、設定ページの [TimeOffManager シングル サインオン] から入手できます。

    ![[シングル サインオンの設定]](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Single Sign-On Settings")

4.  **[TimeOffManager でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、管理者として TimeOffManager 企業サイトにログインします。

6.  **[Account] \> [Account Options] \> [Single Sign-On Settings]** に移動します。

    ![[シングル サインオンの設定]](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Single Sign-On Settings")

7.  **[シングル サインオンの設定]** セクションで、次の手順に従います。

    ![[シングル サインオンの設定]](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Single Sign-On Settings")

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。  ダウンロードした証明書から **Base-64 でエンコードされた** ファイルを作成します。  

        >[AZURE.TIP] 詳細については、 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)

    b.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
    
    c.  Azure クラシック ポータルで、**[TimeOffManager でのシングル サインオンの構成]** ダイアログ ページの **[発行者の URL]** の値をコピーし、**[Idp Issuer]** ボックスに貼り付けます。
    
    d.  Azure クラシック ポータルで、**[TimeOffManager でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[IdP Endpoint URL]** ボックスに貼り付けます。
    
    e.  **[Enforce SAML]** で **[No]** を選択します。
    

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。  **[Auto-Create Users]** で **[Yes]** を選択します。
    
    g.  Azure クラシック ポータルで、**[TimeOffManager でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[Logout URL]** ボックスに貼り付けます。
    
    h.  **[変更を保存]**をクリックします。

8.  Azure クラシック ポータルの **[TimeOffManager でのシングル サインオンの構成]** ダイアログ ページで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configure Single Sign-On")

9.  上部のメニューで、 **属性** to open the **SAML Token 属性** ダイアログを開きます。

    ![属性](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attributes")

10. 必要な属性のマッピングを追加するには、次の手順を実行します。

    ![[SAML トークン属性]](./media/active-directory-saas-timeoffmanager-tutorial/123.png "saml token attributes")

  	|属性名|属性値|
  	|---|---|
  	|電子メール|User.mail|
  	|Firstname|User.givenname|
  	|Lastname|User.surname|

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。  上記の表の各データ行で、 **[ユーザー属性の追加]**をクリックします。

    b.  **[属性名]** ボックスに、その行に対して表示される属性名を入力します。

    c.  **[属性値]** ボックスで、その行に対して表示される属性値を選択します。

    d.  **[完了]** をクリックします。

11. **[変更の適用]**をクリックします。

##<a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
  
Azure AD ユーザーが TimeOffManager にログインできるようにするには、そのユーザーを TimeOffManager にプロビジョニングする必要があります。  
TimeOffManager は、ジャストインタイム ユーザー プロビジョニングをサポートしています。 ユーザー側で必要な操作はありません。  
シングル サインオンを使用して初めてログインするときに、ユーザーが自動的に追加されます。

>[AZURE.NOTE] TimeOffManager から提供されている他の TimeOffManager ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##<a name="assigning-users"></a>ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###<a name="to-assign-users-to-timeoffmanager,-perform-the-following-steps:"></a>ユーザーを TimeOffManager に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **TimeOffManager** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![[ユーザーの割り当て]](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Assign Users")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

    ![はい](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。



<!--HONumber=Oct16_HO2-->


