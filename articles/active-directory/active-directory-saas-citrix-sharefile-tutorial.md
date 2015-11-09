<properties 
    pageTitle="チュートリアル: Azure Active Directory と Citrix ShareFile の統合 | Microsoft Azure" 
    description="Azure Active Directory で Citrix ShareFile を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と Citrix ShareFile の統合

このチュートリアルでは、Azure と Citrix ShareFile の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Citrix ShareFile テナント

このチュートリアルを終了すると、Citrix ShareFile に割り当てた Azure AD ユーザーは、Citrix ShareFile 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Citrix ShareFile のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "シナリオ")
##Citrix ShareFile のアプリケーション統合の有効化

このセクションでは、Citrix ShareFile のアプリケーション統合を有効にする方法を説明します。

###Citrix ShareFile のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Microsoft Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Citrix ShareFile**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Citrix ShareFile]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Citrix ShareFile に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **Citrix ShareFile** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの有効化](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "シングル サインオンの有効化")

2.  **[ユーザーの Citrix ShareFile へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページで、**[Citrix ShareFile サインオン URL]** ボックスに、`https://<tenant-name>.shareFile.com` のパターンで URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "アプリケーション URL の構成")

4.  **[Citrix ShareFile でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "シングル サインオンの構成")

5.  別の Web ブラウザー ウィンドウで、**Citrix ShareFile** 企業サイトに管理者としてログインします。

6.  上部のツールバーの **[Admin]** をクリックします。

7.  左側のナビゲーション ウィンドウで、**[Configure Single Sign-On]** を選択します。

    ![アカウント管理](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "アカウント管理")

8.  **[Single Sign-On/ SAML 2.0 Configuration]** ダイアログ ページの **[Basic Settings]** で、次の手順を実行します。

    ![シングル サインオン](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "シングル サインオン")

    1.  **[Enable SAML]** をクリックします。
    2.  Azure ポータルで、**[Citrix ShareFile でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[Your IDP Issuer/ Entity ID]** ボックスに貼り付けます。
    3.  Azure ポータルで、**[Citrix ShareFile でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[Login URL]** ボックスに貼り付けます。
    4.  Azure ポータルで、**[Citrix ShareFile でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[Logout URL]** ボックスに貼り付けます。
    5.  **[X.509 Certificate]** フィールドの横の **[Change]** をクリックし、Azure AD ポータルからダウンロードした証明書をアップロードします。![基本設定](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "基本設定")

9.  Citrix ShareFile 管理ポータルで **[Save]** をクリックします。

10. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Citrix ShareFile にログインできるようにするには、ユーザーを Citrix ShareFile にプロビジョニングする必要があります。Citrix ShareFile の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **Citrix ShareFile** テナントにログインします。

2.  **[Manage Users]、[Manage Users Home]、[+ Create Employee]** の順にクリックします。

    ![従業員の作成](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "従業員の作成")

3.  プロビジョニングする有効な Azure AD アカウントの**電子メール**、**名**、**姓**を入力します。

    ![基本情報](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "基本情報")

4.  **[ユーザーの追加]** をクリックします。

    >[AZURE.NOTE]AAD アカウント所有者は、アカウントがアクティブになる前に、電子メールを受取、アカウント確認用のリンクに従います。

>[AZURE.NOTE]Citrix ShareFile から提供されている他の Citrix ShareFile ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、ユーザーにアプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Citrix ShareFile に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  Citrix ShareFile アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->