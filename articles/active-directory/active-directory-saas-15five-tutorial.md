<properties 
    pageTitle="チュートリアル: Azure Active Directory と 15Five の統合 | Microsoft Azure" 
    description="Azure Active Directory で 15Five を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と 15Five の統合

このチュートリアルの目的は、Azure と 15Five の統合を紹介することです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   15Five でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、15Five に割り当てた Azure AD ユーザーは、15Five 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  15Five のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-15five-tutorial/IC784667.png "シナリオ")
##15Five のアプリケーション統合の有効化

このセクションでは、15Five のアプリケーション統合を有効にする方法について説明します。

###15Five のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-15five-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-15five-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-15five-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  [**検索**] ボックスに、「**15Five**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-15five-tutorial/IC784668.png "アプリケーション ギャラリー")

7.  結果ウィンドウで [**15Five**] を選択し、[**完了**] をクリックしてアプリケーションを追加します。

    ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで 15Five に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **15Five** アプリケーション統合ページで [**シングル サインオンの構成**] をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-15five-tutorial/IC784670.png "Configure single sign-on")

2.  [**ユーザーの 15Five へのアクセスを設定してください**] ページで、[**Microsoft Azure AD のシングル サインオン**] を選択し、[**次へ**] をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-15five-tutorial/IC784671.png "Configure single sign-on")

3.  [**アプリケーション URL の構成**] ページの [**15Five サインイン URL]** テキスト ボックスに、"**https://company.15Five.com*" というパターンの URL を入力し、[**次へ**] をクリックします。

    ![Configure App URL](./media/active-directory-saas-15five-tutorial/IC784672.png "Configure App URL")

4.  [**15Five でのシングルサインオンの構成**] ページで、[**メタデータのダウンロード**] をクリックし、メタ データファイルを 15Five サポート チームに転送します。

    ![Configure single sign-on](./media/active-directory-saas-15five-tutorial/IC784673.png "Configure single sign-on")

    >[AZURE.NOTE]15Five サポート チームがシングル サインオンを有効にする必要があります。

5.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、[**完了**] をクリックして [**シングル サインオンの構成**] ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-15five-tutorial/IC784674.png "Configure single sign-on")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが 15Five にログインできるようにするには、そのユーザーを 15Five にプロビジョニングする必要があります。15Five の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **15Five** 企業サイトに管理者としてログインします。

2.  [**会社の管理**] に移動します。

    ![会社の管理](./media/active-directory-saas-15five-tutorial/IC784675.png "会社の管理")

3.  **[ユーザー] > [ユーザーを追加]** の順にクリックします。

    ![People](./media/active-directory-saas-15five-tutorial/IC784676.png "People")

4.  [新しいユーザーの追加] セクションで、次の手順に従います。

    ![新しいユーザーの追加](./media/active-directory-saas-15five-tutorial/IC784677.png "新しいユーザーの追加")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**名**、**姓**、**役職**、**電子メール アドレス**を入力します。
    2.  **[Done]** をクリックします。

    >[AZURE.NOTE]アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE]他の 15Five ユーザー アカウントの作成ツールまたは 15Five から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを 15Five に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **15Five** アプリケーション統合ページで、[**ユーザーの割り当て**] をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-15five-tutorial/IC784678.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-15five-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0121_2016-->