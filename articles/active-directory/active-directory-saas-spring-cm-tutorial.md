<properties 
    pageTitle="チュートリアル: Azure Active Directory と SpringCM の統合 | Microsoft Azure" 
    description="SpringCM と Azure Active Directory を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と SpringCM の統合
  
このチュートリアルでは、Azure Active Directory と SpringCM の間でのシングル サインオンを設定する方法を説明します。
  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   SpringCM でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、SpringCM に割り当てられている Azure Active Directory ユーザーは、AAD アクセス パネルを使用してシングル サインオンできます。

1.  SpringCM のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "シナリオ")

##SpringCM のアプリケーション統合の有効化
  
このセクションでは、SpringCM のアプリケーション統合を有効にする方法について説明します。

###SpringCM のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**SpringCM**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[SpringCM]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、SpringCM で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **SpringCM** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "シングル サインオンの構成")

2.  **[ユーザーの SpringCM へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[SpringCM サインオン URL]** ボックスに、ユーザーが SpringCM アプリケーションのサインオンに使用する URL を入力し、　**[次へ]** をクリックします。

    アプリケーション URL は、SpringCM テナントの URL です (例: **https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*)。

    ![アプリケーション URL の構成](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "アプリケーション URL の構成")

4.  **[SpringCM でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターのローカルに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "シングル サインオンの構成")

5.  別の Web ブラウザー ウィンドウで、**SpringCM** 企業サイトに管理者としてサインオンします。

6.  上部にあるメニューの **[GO TO]** をクリックし、**[Preferences]** をクリックします。次に、**[Account Preferences]** セクションで、**[SAML SSO]** をクリックします。

    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  [Identity Provider Configuration] セクションで、次の手順に従います。

    ![ID プロバイダー構成](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "ID プロバイダー構成")

    1.  ダウンロードした Azure Active Directory 証明書をアップロードするために、**[Select Issuer Certificate]** または **[Change Issuer Certificate]** をクリックします。
    2.  Microsoft Azure ポータルの **[SpringCM でのシングル サインオンの構成]** ダイアログ ページで **[発行者の URL]** の値をコピーし、**[Issuer]** ボックスに貼り付けます。
    3.  Microsoft Azure ポータルの **[SpringCM でのシングル サインオンの構成]** ダイアログ ページで **[シングル サインオン サービス URL]** の値をコピーし、**[Service Provider (SP) Initiated Endpoint]** ボックスに貼り付けます。
    4.  **[SAML Enabled]** で **[Enable]** を選択します。
    5.  **[Save]** をクリックします。

8.  Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "シングル サインオンの構成")

##ユーザー プロビジョニングの構成
  
Azure Active Directory ユーザーが SpringCM にログインできるようにするには、そのユーザーを SpringCM にプロビジョニングする必要があります。SpringCM の場合、プロビジョニングは手動で行います。

>[AZURE.NOTE]詳細については、「[Create and Edit a SpringCM User (SpringCM ユーザーの作成と編集)](http://knowledge.springcm.com/create-and-edit-a-springcm-user)」をご覧ください。

###ユーザー アカウントを SpringCM にプロビジョニングするには、次の手順に従います。

1.  **SpringCM** 企業サイトに管理者としてログインします。

2.  **[GOTO]**、**[Address Book]** の順にクリックします。

    ![ユーザーの作成](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "ユーザーの作成")

3.  **[Create User]** をクリックします。

4.  **[User Role]** を選択します。

5.  **[Send Activation Email]** を選択します。

6.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの姓名と電子メール アドレスを入力します。

7.  ユーザーを **[Security group]** に追加します。

8.  **[Save]** をクリックします。

>[AZURE.NOTE]SpringCM から提供されている他の SpringCM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを SpringCM に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **SpringCM** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->