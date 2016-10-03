<properties 
    pageTitle="チュートリアル: Azure Active Directory と Sprinklr の統合 | Microsoft Azure" 
    description="Azure Active Directory で Sprinklr を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と Sprinklr の統合
  
このチュートリアルでは、Azure と Sprinklr の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Sprinklr テナント
  
このチュートリアルを完了すると、Sprinklr に割り当てた Azure AD ユーザーは、Sprinklr 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Sprinklr のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "シナリオ")

##Sprinklr のアプリケーション統合の有効化
  
このセクションでは、Sprinklr のアプリケーション統合を有効にする方法について説明します。

###Sprinklr のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Sprinklr**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Sprinklr]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、Sprinklr で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  
この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **[Sprinklr]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configure single sign-on")

2.  **[ユーザーの Sprinklr へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configure single sign-on")

3.  **[アプリケーション URL の構成]** ページで、**[Sprinklr サインイン URL]** ボックスに、"*https://\<テナント名>.sprinklr.com*" というパターンで URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "アプリケーション URL の構成")

4.  **[prinklr.com でのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure single sign-on](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configure single sign-on")

5.  別の Web ブラウザー ウィンドウで、Sprinklr 企業サイトに管理者としてログインします。

6.  **[Administration]、[Settings]** の順に選択します。

    ![管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "管理")

7.  左側のパネルで、**[Manage Partner]、[Single Sign]** の順に選択します。

    ![パートナーの管理](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "パートナーの管理")

8.  **[+Add Single Sign Ons]** をクリックします。

    ![シングル サインオン](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "シングル サインオン")

9.  **[Single Sign on]** ページで、次の手順に従います。

    ![シングル サインオン](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "シングル サインオン")

    1.  **[Name]** テキスト ボックスに、構成の名前を入力します (例: *WAADSSOTest*)。
    2.  **[Enabled]** を選択します。
    3.  **[Use new SSO Certificate]** を選択します。
    4.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。

        >[AZURE.TIP] 詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    5.  base-64 でエンコードされた証明書をノートパッドで開き、その内容をクリップボードにコピーして、**[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。
    6.  Azure クラシック ポータルの **[Sprinklr でのシングル サインオンの構成]** ダイアログ ページで、**ID プロバイダーの ID** の値をコピーし、**[エンティティ ID]** ボックスに貼り付けます。
    7.  Azure クラシック ポータルで、**[Sprinklr でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[Identity Provider Login URL (ID プロバイダーのログイン URL)]** ボックスに貼り付けます。
    8.  Azure クラシック ポータルで、**[Sprinklr でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[Identity Provider Logout URL (ID プロバイダーのログアウト URL)]** ボックスに貼り付けます。
    9.  **[SAML User ID Type]** として**[Assertion contains User”s sprinklr.com username]** を選択します。
    10. **[SAML User ID Location]** として **[User ID is in the Name Identifier element of the Subject statement]** を選択します。
    11. **[Save]** をクリックします。

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configure single sign-on")

##ユーザー プロビジョニングの構成
  
AAD ユーザーがサインインできるようにするには、ユーザーを Sprinklr アプリケーションにプロビジョニングする必要があります。このセクションでは、Sprinklr で AAD ユーザー アカウントを作成する方法について説明します。

###ユーザー アカウントを Sprinklr にプロビジョニングするには、次の手順に従います。

1.  Sprinklr 企業サイトに管理者としてログインします。

2.  **[Administration]、[Settings]** の順に選択します。

    ![管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "管理")

3.  左側のパネルで、**[クライアントの管理]、[Users]** の順に選択します。

    ![Settings](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Settings")

4.  **[Add User]** をクリックします。

    ![Settings](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Settings")

5.  **[Edit user]** ダイアログで、次の手順に従います。

    ![ユーザーの編集](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "ユーザーの編集")

    1.  **[Email]**、**[First Name]**、および **[Last Name]** テキスト ボックスに、プロビジョニングする Azure AD のユーザー アカウントの情報を入力します。
    2.  **[Password Disabled]** を選択します。
    3.  **[Language]** を選択します。
    4.  **[User Type]** を選択します。
    5.  **[Update]** をクリックします。

    >[AZURE.IMPORTANT] ユーザーが ID プロバイダー経由でログインできるようにするには、**[Password Disabled]** を選択する必要があります。

6.  **[Role]** に移動して、次の手順に従います。

    ![パートナーのロール](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "パートナーのロール")

    1.  **[Global]** ボックスの一覧から、**[ALL\_Permissions]** を選択します。
    2.  **[Update]** を選択します。

>[AZURE.NOTE] Sprinklr から提供されている他の Sprinklr ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Sprinklr に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Sprinklr** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0921_2016-->