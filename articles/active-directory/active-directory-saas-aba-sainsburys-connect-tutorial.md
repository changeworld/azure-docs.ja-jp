<properties pageTitle="チュートリアル: Azure Active Directory と ABa Sainsburys Connect の統合 | Microsoft Azure" description="Azure Active Directory で ABa Sainsburys Connect を使用して、シングル サインオンや自動化されたプロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と ABa Sainsburys Connect の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=615290)をクリックしてください。

このチュートリアルでは、Azure と ABa Sainsburys Connect の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Aba Sainsburys Connect でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Aba Sainsburys Connect に割り当てた Azure AD ユーザーは、Aba Sainsburys Connect 企業サイト (サービス プロバイダーが開始したサインオン) で、または[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Aba Sainsburys Connect のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807723.png "シナリオ")
##Aba Sainsburys Connect のアプリケーション統合の有効化

このセクションでは、Aba Sainsburys Connect のアプリケーション統合を有効にする方法を説明します。

###Aba Sainsburys Connect のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  [**検索**] ボックスで、「**Aba Sainsburys Connect**」と入力します。

    ![ABa Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807724.png "ABa Sainsburys Connect")

7.  結果ウィンドウで** [Aba Sainsburys Connect] **を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ABa Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807725.png "ABa Sainsburys Connect")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Aba Sainsburys Connect に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Aba Sainsburys Connect** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807726.png "シングル サインオンの構成")

2.  **[ユーザーの Aba Sainsburys Connect へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807727.png "シングル サインオンの構成")

3.  **[アプリケーション設定の構成]** ページで、次の手順を実行します。

    ![Configure App Settings](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807728.png "Configure App Settings")

    1.  **[サインオン URL]** テキスト ボックスに、ユーザーが Aba Sainsburys Connect アプリケーションへのサインオンに使用する URL を入力します (例: *https://myaba.co.uk/client-access/sainsburys/saml.php*))。
2.  **[次へ]** をクリックします。

4.  **[Aba Sainsburys Connect でのシングル サインオンの構成]** ページで、メタデータをダウンロードするには、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807729.png "シングル サインオンの構成")

5.  ダウンロードしたメタデータ ファイルを Aba Sainsburys Connect サポート チームに送信します。

    >[AZURE.NOTE]Aba Sainsburys Connect サポート チームが、実際に SSO を構成する必要があります。ご使用のサブスクリプションで SSO が有効になると、通知が届きます。

6.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807730.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Aba Sainsburys Connect にログインできるようにするには、ユーザーを Aba Sainsburys Connect にプロビジョニングする必要があります。Aba Sainsburys Connect の場合、ユーザー アカウントは、Aba Sainsburys Connect のサポート チームによって作成される必要があります。

>[AZURE.NOTE]他の Aba Sainsburys Connect ユーザー アカウント作成ツールまたはAba Sainsburys Connect から提供されている API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###Aba Sainsburys Connect にユーザーを割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  Aba Sainsburys Connect アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807731.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を参照してください。

<!---HONumber=August15_HO7-->