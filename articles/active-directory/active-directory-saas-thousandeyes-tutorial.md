<properties 
    pageTitle="チュートリアル: Azure Active Directory と ThousandEyes の統合 | Microsoft Azure" 
    description="Azure Active Directory で ThousandEyes を使用してシングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="06/21/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と ThousandEyes の統合
  
このチュートリアルでは、Azure Active Directory (Azure AD) と ThousandEyes の間でのシングル サインオンを設定する方法を説明します。
  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ThousandEyes でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、ThousandEyes に割り当てた AAD ユーザーは、ThousandEyes 企業サイト (サービス プロバイダーが開始したサインオン) で、または AAD アクセス パネルを使用して、アプリケーションにシングル サインオンできるようになります。

1.  ThousandEyes のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "シナリオ")

##ThousandEyes のアプリケーション統合の有効化
  
このセクションでは、ThousandEyes のアプリケーション統合を有効にする方法を説明します。

###ThousandEyes のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**ThousandEyes**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[ThousandEyes]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure Active Directory でのユーザーのアカウントで ThousandEyes に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **[ThousandEyes]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "シングル サインオンの構成")

2.  **[ユーザーの ThousandEyes へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[ThousandEyes のサインオン URL]** テキストボックスに、ユーザーが ThousandEyes アプリケーションにサインオンするときに使用する URL (例: **https://app.thousandeyes.com/login/sso*")) を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configure App URL")

4.  **[ThousandEyes でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "シングル サインオンの構成")

5.  別の Web ブラウザーのウィンドウで、管理者として **ThousandEyes** 企業サイトにサインオンします。

6.  上部のメニューで **[Settings]** をクリックします。

    ![設定](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "設定")

7.  **[Account]** をクリックします。

    ![アカウント](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "アカウント")

8.  **[Security & Authentication]** タブをクリックします。

    ![セキュリティと認証](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "セキュリティと認証")

9.  **[Setup Single Sign-On]** セクションで、次の手順を実行します。

    ![シングル サインオンの設定](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "シングル サインオンの設定")

    1.  **[Enable Single Sign-On]** を選択します。
    2.  Microsoft Azure クラシック ポータルの **[ThousandEyes でのシングル サインオンの構成]** ページで、**[リモート ログイン URL]** 値をコピーして、**[ログイン ページの URL]** テキスト ボックスに貼り付けます。
    3.  Microsoft Azure クラシック ポータルの **[ThousandEyes でのシングル サインオンの構成]** ページで、**[リモート ログアウト URL]** 値をコピーして、**[ログアウト ページの URL]** テキスト ボックスに貼り付けます。
    4.  Microsoft Azure クラシック ポータルの **[ThousandEyes でのシングル サインオンの構成]** ページで、**[発行者の URL]** 値をコピーして、**[ID プロバイダーの発行者]** テキスト ボックスに貼り付けます。
    5.  **[ID プロバイダー証明書]** で **[ファイルの選択]** をクリックし、Microsoft Azure クラシック ポータルからダウンロードした証明書をアップロードします。
    6.  **[保存]** をクリックします。

10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "シングル サインオンの構成")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが ThousandEyes にログインできるようにするには、そのユーザーを ThousandEyes にプロビジョニングする必要があります。ThousandEyes の場合、プロビジョニングは手動で行います。

###ユーザー アカウントを ThousandEyes にプロビジョニングするには、次の手順に従います。

1.  ThousandEyes 企業サイトに管理者としてログインします。

2.  **[Settings]** をクリックします。

    ![設定](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "設定")

3.  [**アカウント**] クリックします。

    ![アカウント](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "アカウント")

4.  **[Accounts & Users]** タブをクリックします。

    ![アカウントとユーザー](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "アカウントとユーザー")

5.  **[Add Users & Accounts]** セクションで、次の手順を実行します。

    ![ユーザー アカウントの追加](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "ユーザー アカウントの追加")

    1.  プロビジョニングする有効な Azure Active Directory アカウントの**[Name]**、**[Email]**、その他の詳細を該当するボックスに入力します。
    2.  **[Add New User to Account]** をクリックします。

        >[AZURE.NOTE] AAD アカウントの所有者にアカウントの確認およびアクティブ化用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE] ThousandEyes から提供されている他の ThousandEyes ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを ThousandEyes に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **ThousandEyes** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0622_2016-->