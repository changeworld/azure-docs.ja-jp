<properties 
    pageTitle="チュートリアル: Azure Active Directory と Aha! の統合 | Microsoft Azure" 
    description="Azure Active Directory で Aha! を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と Aha! の統合

このチュートリアルでは、Azure と Aha! の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Aha! でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Aha! に割り当てた Azure AD ユーザーは、Aha! 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Aha! のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-aha-tutorial/IC798944.png "シナリオ")
##Aha! のアプリケーション統合の有効化

このセクションでは、Aha! のアプリケーション統合を有効にする方法について説明します。

###Aha! のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-aha-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-aha-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-aha-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  [**検索**] ボックスに、「**Aha!**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-aha-tutorial/IC798945.png "アプリケーション ギャラリー")

7.  結果ウィンドウで [**Aha!**] を選択し、[**完了**] をクリックしてアプリケーションを追加します。

    ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Aha! に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Aha!** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798946.png "Configure Single Sign-On")

2.  [**ユーザーの Aha! へのアクセスを設定してください**] ページで、[**Microsoft Azure AD のシングル サインオン**] を選択し、[**次へ**] をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798947.png "Configure Single Sign-On")

3.  [**アプリケーション URL の構成**] ページの [** Aha! サインオン URL**] テキスト ボックスに、Aha! アプリケーションへのサインオンにユーザーが使用する URL (例:"*https://company.aha.io/session/new*") を入力してから、**[次へ]* をクリックします。

    ![Configure App URL](./media/active-directory-saas-aha-tutorial/IC798948.png "アプリケーション URL の構成")

4.  [**Aha! でのシングル サインオンの構成**] ページで、メタデータをダウンロードするには、[**メタデータのダウンロード**] をクリックし、コンピューターでメタデータ ファイルをローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798949.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、管理者として Aha! 企業サイトにログインします。

6.  上部のメニューで [**設定**] をクリックします。

    ![Settings](./media/active-directory-saas-aha-tutorial/IC798950.png "Settings")

7.  [**アカウント**] クリックします。

    ![プロファイル](./media/active-directory-saas-aha-tutorial/IC798951.png "プロファイル")

8.  [**セキュリティとシングル サインオン**] クリックします。

    ![セキュリティとシングル サインオン](./media/active-directory-saas-aha-tutorial/IC798952.png "セキュリティとシングル サインオン")

9.  [**シングル サインオン**] セクションで、[**ID プロバイダー**] として [**SAML2.0**] を選択します。

    ![セキュリティとシングル サインオン](./media/active-directory-saas-aha-tutorial/IC798953.png "セキュリティとシングル サインオン")

10. [**シングル サインオン**] 構成ページで、次の手順を実行します。

    ![シングル サインオン](./media/active-directory-saas-aha-tutorial/IC798954.png "シングル サインオン")

    1.  [**名前**] テキスト ボックスに、構成の名前を入力します。
    2.  [**Configure using**] には [**メタデータ ファイル**] を選択します。
    3.  ダウンロードしたメタデータ ファイルをアップロードするには、[**参照**] をクリックします。
    4.  [**更新**] をクリックします。

11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798955.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Aha! にログインできるようにするには、そのユーザーを Aha! にプロビジョニングする必要があります。Aha! の場合、プロビジョニングは自動化されています。ユーザー側で必要な操作はありません。
  
最初のシングル サインオンの試行中に、必要に応じてユーザーが自動的に作成されます。

>[AZURE.NOTE] 他の Aha! ユーザー アカウントの作成ツールまたは Aha! から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Aha! に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Aha!** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-aha-tutorial/IC798956.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-aha-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。

<!---HONumber=AcomDC_0713_2016-->