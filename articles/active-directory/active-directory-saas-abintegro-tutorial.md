<properties pageTitle="チュートリアル: Azure Active Directory と Abintegro の統合 | Microsoft Azure" description="Azure Active Directory で Abintegro を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Abintegro の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=529073)をクリックしてください。

このチュートリアルでは、Azure と Abintegro の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Abintegro でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Abintegro に割り当てた Azure AD ユーザーは、Abintegro 企業サイト (サービス プロバイダーが開始したサインオン) で、または[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Abintegro のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-abintegro-tutorial/IC790076.png "シナリオ")
##Abintegro のアプリケーション統合の有効化

このセクションでは、Abintegro のアプリケーション統合を有効にする方法を説明します。

###Abintegro のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-abintegro-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-abintegro-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  [**検索**] ボックスに、「**Abintegro**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-abintegro-tutorial/IC790077.png "アプリケーション ギャラリー")

7.  結果ウィンドウで [**Abintegro**] を選択し、[**完了**] をクリックしてアプリケーションを追加します。

    ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Abintegro に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Abintegro** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-abintegro-tutorial/IC790079.png "シングル サインオンの構成")

2.  **[ユーザーの Abintegro へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-abintegro-tutorial/IC790080.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[Abintegro サインオン URL]** テキスト ボックスに、ユーザーが Abintegro アプリケーションのサインオンに使用する URL (例: `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`) を入力して、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-abintegro-tutorial/IC790081.png "アプリケーション URL の構成")

4.  **[Abintegro でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-abintegro-tutorial/IC790082.png "シングル サインオンの構成")

5.  Abintegro サポート チームに、メタデータ ファイルを送信します。

    >[AZURE.NOTE]シングル サインオンの構成は、Abintegro サポート チームが実行する必要があります。構成が完了すると、サポート チームから通知が届きます。

6.  Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-abintegro-tutorial/IC790083.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成

Abintegro へのユーザー プロビジョニングの構成にあたって必要な操作はありません。割り当てられたユーザーがアクセス パネルを使用してAbintegro にログインしようとすると、そのユーザーが存在するかどうかが Abintegro によって確認されます。使用可能なユーザー アカウントがない場合、ユーザー アカウントは Abintegro により自動的に作成されます。
##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Abintegro に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  Abintegro アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-abintegro-tutorial/IC790084.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-abintegro-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を参照してください。

<!---HONumber=Oct15_HO3-->