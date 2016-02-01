<properties 
    pageTitle="チュートリアル: Azure Active Directory と AppDynamics の統合 | Microsoft Azure" 
    description="Azure Active Directory で AppDynamics を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と AppDynamics の統合

このチュートリアルでは、Azure と AppDynamics の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   AppDynamics でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、AppDynamics に割り当てた Azure AD ユーザーは、AppDynamics 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  AppDynamics のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "シナリオ")
##AppDynamics のアプリケーション統合の有効化

このセクションでは、AppDynamics のアプリケーション統合を有効にする方法を説明します。

###AppDynamics のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **[検索]** ボックスに、**「AppDynamics」**と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[AppDynamics]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで AppDynamics に対する認証を行えるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **AppDynamics** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "シングル サインオンの構成")

2.  **[ユーザーの AppDynamics へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[AppDynamics サインオン URL]** テキスト ボックスに、ユーザーが AppDynamics アプリケーションのサインオンに使用する URL (例: *https://companyname.saas.appdynamics.com*")) を入力して、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")

4.  **[AppDynamics でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "シングル サインオンの構成")

5.  別の Web ブラウザーのウィンドウで、管理者として AppDynamics 企業サイトにログインします。

6.  上部にあるツールバーで **[Settings]**、**[Administration]** の順にクリックします。

    ![Administration](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")

7.  **[Authentication Provider]** タブをクリックします。

    ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")

8.  **[Authentication Provider]** セクションで、次の手順を実行します。

    ![SAML の構成](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML の構成")

    1.  **[Authentication Provider]** として、**[SAML]** を選択します。
    2.  Azure ポータルの **[AppDynamics でのシングル サインオンの構成]** ページで、**[リモート ログイン URL]** 値をコピーして、**[Login URL]** テキスト ボックスに貼り付けます。
    3.  Azure ポータルで、**[AppDynamics でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[Logout URL]** ボックスに貼り付けます。
    4.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    5.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[Certificate]** テキストボックスに貼り付けます。
    6.  **[Save]** をクリックします。![Save](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")

9.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが AppDynamics にログインできるようにするには、そのユーザーを AppDynamics にプロビジョニングする必要があります。AppDynamics の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  AppDynamics 企業サイトに管理者としてログインします。

2.  **[Users]** に移動して、**[+]** をクリックして **[Create User]** ダイアログを開きます。

    ![Users](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")

3.  **[Create User]** セクションで、次の手順に従います。

    ![Create User](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの **[Username]**、**[Name]**、**[Email]**、**[New Password]**、**[Repeat New Password]** を入力します。
    2.  **[保存]** をクリックします。

>[AZURE.NOTE]他の AppDynamics ユーザー アカウント作成ツールまたは AppDynamics から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを AppDynamics に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  AppDynamics アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0121_2016-->