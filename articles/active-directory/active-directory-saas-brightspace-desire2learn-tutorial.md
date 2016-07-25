<properties 
    pageTitle="チュートリアル: Azure Active Directory と Brightspace by Desire2Learn の統合 | Microsoft Azure" 
    description="Azure Active Directory で Brightspace by Desire2Learn を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Brightspace by Desire2Learn の統合

このチュートリアルでは、Azure と Brightspace by Desire2Learn の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Brightspace by Desire2Learn でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Brightspace by Desire2Learn に割り当てた Azure AD ユーザーは、Brightspace by Desire2Learn 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Brightspace by Desire2Learn のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "シナリオ")
##Brightspace by Desire2Learn のアプリケーション統合の有効化

このセクションでは、Brightspace by Desire2Learn のアプリケーション統合を有効にする方法を説明します。

###Brightspace by Desire2Learn のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Brightspace by Desire2Learn**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Brightspace by Desire2Learn]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
##シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Brightspace by Desire2Learn に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Brightspace by Desire2Learn** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configure Single Sign-On")

2.  **[ユーザーの Brightspace by Desire2Learn へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページで、次の手順を実行します。

    ![Configure App URL](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "アプリケーション URL の構成")

    1.  **[サインオン URL]** ボックスに、ユーザーが **Brightspace by Desire2Learn** へのサインオンに使用する URL を入力します (例: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*)。
    2.  **[次へ]** をクリックします。

4.  **[Brightspace by Desire2Learn でのシングル サインオンの構成]** ページで、メタデータをダウンロードするために、**[メタデータのダウンロード]** をクリックし、メタデータをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configure Single Sign-On")

5.  ダウンロードしたメタデータ ファイルを Brightspace by Desire2Learn サポート チームに送信します。

    >[AZURE.NOTE] Brightspace by Desire2Learn サポート チームが、実際に SSO を構成する必要があります。ご使用のサブスクリプションで SSO が有効になると通知されます。

6.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Brightspace by Desire2Learn にログインできるようにするには、ユーザーを Brightspace by Desire2Learn にプロビジョニングする必要があります。Brightspace by Desire2Learn の場合、Brightspace by Desire2Learn サポート チームがユーザー アカウントを作成する必要があります。

>[AZURE.NOTE] Brightspace by Desire2Learn から提供されている他の Brightspace by Desire2Learn ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Brightspace by Desire2Learn に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Brightspace by Desire2Learn** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0713_2016-->