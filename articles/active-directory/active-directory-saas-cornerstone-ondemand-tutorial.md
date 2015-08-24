<properties pageTitle="チュートリアル: Azure Active Directory と Cornerstone OnDemand の統合 | Microsoft Azure" description="Azure Active Directory で Cornerstone OnDemand を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Cornerstone OnDemand の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=526246)をクリックしてください。

このチュートリアルでは、Azure と Cornerstone OnDemand の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Cornerstone OnDemand テナント

このチュートリアルを完了すると、Cornerstone OnDemand に割り当てた Azure AD ユーザーは、Cornerstone OnDemand 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Cornerstone OnDemand のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "シナリオ")
##Cornerstone OnDemand のアプリケーション統合の有効化

このセクションでは、Cornerstone OnDemand のアプリケーション統合を有効にする方法について説明します。

###Cornerstone OnDemand のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**cornerstone ondemand**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Cornerstone OnDemand]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Cornerstone OnDemand に対する認証を行うことができるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **Cornerstone OnDemand** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンを有効にする](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "シングル サインオンを有効にする")

2.  **[ユーザーの Cornerstone OnDemand へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Microsoft Azure AD シングル サインオン](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD シングル サインオン")

3.  **[アプリケーション URL の構成]** ページの **[Cornerstone OnDemand サインイン URL]** ボックスに、"**http://company.csod.com*"" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "アプリケーション URL の構成")

4.  **[Cornerstone OnDemand でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをローカルでコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configure Single Sign-On")

5.  次のアイテムを Cornerstone OnDemand サポート チームに送信します。

    1.  ダウンロードした証明書
    2.  **リモート ログイン URL** 値
    3.  **リモート ログイアウト URL** 値

    >[AZURE.NOTE]シングル サインオンは、Cornerstone OnDemand サポート チームが構成する必要があります。構成が完了すると、サポート チームから通知が届きます。

6.  [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Cornerstone OnDemand にログインできるようにするには、そのユーザーを Cornerstone OnDemand にプロビジョニングする必要があります。Cornerstone OnDemand の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  プロビジョニングする Azure AD ユーザーに関する情報 (名前、電子メールなど) を、Cornerstone OnDemand サポート チームに送信します。

>[AZURE.NOTE]Cornerstone OnDemand から提供されている他の Cornerstone OnDemand ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Cornerstone OnDemand に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Cornerstone OnDemand** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![ユーザーの割り当て](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "ユーザーの割り当て")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を参照してください。

<!---HONumber=August15_HO7-->