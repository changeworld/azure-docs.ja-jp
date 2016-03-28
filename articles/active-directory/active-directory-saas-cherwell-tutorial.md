<properties 
    pageTitle="チュートリアル: Azure Active Directory と Cherwell の統合 | Microsoft Azure" 
    description="Azure Active Directory で Cherwell を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="03/16/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Cherwell の統合

このチュートリアルでは、Azure と Cherwell の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Cherwell でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Cherwell に割り当てた Azure AD ユーザーは、Cherwell 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Cherwell のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-cherwell-tutorial/IC798988.png "シナリオ")
##Cherwell のアプリケーション統合の有効化

このセクションでは、Cherwell のアプリケーション統合を有効にする方法について説明します。

###Cherwell のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-cherwell-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-cherwell-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-cherwell-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Cherwell**」と入力します。

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  結果ウィンドウで **[Cherwell]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
##シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Cherwell に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Cherwell** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configure Single Sign-On")

2.  **[ユーザーの Cherwell へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページで、次の手順を実行します。

    ![Configure App URL](./media/active-directory-saas-cherwell-tutorial/IC798992.png "アプリケーション URL の構成")

    1.  **[サインオン URL]** ボックスに、ユーザーが **Cherwell** へのサインオンに使用する URL を入力します (例: **https://pictdev.cherwellondemand.com/cherwellclient*)。
2.  **[次へ]** をクリックします。

4.  **[Cherwell でのシングル サインオンの構成]** ページで、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configure Single Sign-On")

    1.  **[証明書のダウンロード]** をクリックし、コンピューターで証明書をローカルに保存します。
    2.  **[ID プロバイダー URL]** をコピーします。
    3.  **[シングル サインオン サービス URL]** をコピーします。
    4.  **[次へ]** をクリックします。

5.  ダウンロードした証明書、**ID プロバイダー URL**、**シングル サインオン サービス URL** を、Cherwell サポート チームに送信します。

    >[AZURE.NOTE] Cherwell サポート チームが、実際に SSO を構成する必要があります。ご使用のサブスクリプションで SSO が有効になると通知されます。

6.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Cherwell にログインできるようにするには、ユーザーを Cherwell にプロビジョニングする必要があります。Cherwell の場合、Cherwell サポート チームがユーザー アカウントを作成する必要があります。

>[AZURE.NOTE] Cherwell から提供されている他の Cherwell ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Cherwell に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Cherwell** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-cherwell-tutorial/IC798995.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

<!---HONumber=AcomDC_0316_2016-->