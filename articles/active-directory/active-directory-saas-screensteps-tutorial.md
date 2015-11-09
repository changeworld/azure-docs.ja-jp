<properties 
    pageTitle="チュートリアル: Azure Active Directory と ScreenSteps の統合 | Microsoft Azure" 
    description="ScreenSteps と Azure Active Directory を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と ScreenSteps の統合
  
このチュートリアルの目的は、Azure と ScreenSteps の統合を示すことです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ScreenSteps テナント
  
このチュートリアルを終了すると、ScreenSteps に割り当てた Azure AD ユーザーは、ScreenSteps 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  ScreenSteps のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-screensteps-tutorial/IC778516.png "シナリオ")
##ScreenSteps のアプリケーション統合の有効化
  
このセクションでは、ScreenSteps のアプリケーション統合を有効にする方法を説明します。

###ScreenSteps のアプリケーション統合を有効にするには、次の手順に従います。

1.  Microsoft Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-screensteps-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-screensteps-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**ScreenSteps**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-screensteps-tutorial/IC778517.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[ScreenSteps]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ScreenSteps で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **ScreenSteps** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778519.png "シングル サインオンの構成")

2.  **[ユーザーの ScreenSteps へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778520.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[ScreenSteps サインイン URL]** ボックスに、"*https://\<tenant-name>.ScreenSteps.com*" というパターンで URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-screensteps-tutorial/IC778521.png "アプリケーション URL の構成")

4.  **[ScreenSteps でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778522.png "シングル サインオンの構成")

5.  別の Web ブラウザー ウィンドウで、ScreenSteps 企業サイトに管理者としてログインします。

6.  **[Account Management]** をクリックします。

    ![アカウント管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "アカウント管理")

7.  **[Remote Authentication]** をクリックします。

    ![リモート認証](./media/active-directory-saas-screensteps-tutorial/IC778524.png "リモート認証")

8.  **[Create authentication endpoint]** をクリックします。

    ![リモート認証](./media/active-directory-saas-screensteps-tutorial/IC778525.png "リモート認証")

9.  **[Create an Authentication Endpoint]** セクションで、次の手順に従います。

    ![認証エンドポイントの作成](./media/active-directory-saas-screensteps-tutorial/IC778526.png "認証エンドポイントの作成")

    1.  **[Title]** テキスト ボックスに、タイトルを入力します。
    2.  **[Mode]** の一覧から **[SAML]** を選択します。
    3.  **[作成]** をクリックします。

10. **[Remote Authentication Endpoint]** セクションで、次の手順に従います。

    ![リモート認証エンドポイント](./media/active-directory-saas-screensteps-tutorial/IC778527.png "リモート認証エンドポイント")

    1.  Azure AD ポータルの **[ScreenSteps でのシングル サインオンの構成]** ページで、**[リモート ログイン URL]** 値をコピーして、**[Remote Login URL]** ボックスに貼り付けます。
    2.  Azure AD ポータルの **[ScreenSteps でのシングル サインオンの構成]** ページで、**[リモート ログアウト URL]** 値をコピーして、**[Log out URL]** ボックスに貼り付けます。
    3.  **[Choose a file]** をクリックし、ダウンロードした証明書をアップロードします。
    4.  **[Update]** をクリックします。

11. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778542.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが **ScreenSteps** にログインできるようにするには、ユーザーを **ScreenSteps** にプロビジョニングする必要があります。**ScreenSteps** の場合、プロビジョニングは手動で行います。

###ユーザー アカウントを ScreenSteps にプロビジョニングするには、次の手順に従います。

1.  **ScreenSteps** テナントにログインします。

2.  **[Account Management]** をクリックします。

    ![アカウント管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "アカウント管理")

3.  **[ユーザー]** をクリックします。

    ![ユーザー](./media/active-directory-saas-screensteps-tutorial/IC778544.png "ユーザー")

4.  **[Create a user]** をクリックします。

    ![すべてのユーザー](./media/active-directory-saas-screensteps-tutorial/IC778545.png "すべてのユーザー")

5.  **[User Role]** の一覧からユーザーのロールを選択します。

6.  [User Role] セクションで、プロビジョニングする有効な AAD アカウントの**姓**、名、**電子メール**、**ログイン**、**パスワード**、および **パスワードの確認**を該当するテキスト ボックスに入力します。

    ![新しいユーザー](./media/active-directory-saas-screensteps-tutorial/IC778546.png "新しいユーザー")

7.  [Groups] セクションで、**[Authentication Group (SAML)]** を選択し、**[Create User]** をクリックします。

    ![グループ](./media/active-directory-saas-screensteps-tutorial/IC778547.png "グループ")

>[AZURE.NOTE]ScreenSteps から提供されている他の ScreenSteps ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを ScreenSteps に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  ScreenSteps アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-screensteps-tutorial/IC773094.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

    ![ユーザーの割り当て](./media/active-directory-saas-screensteps-tutorial/IC778548.png "ユーザーの割り当て")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->