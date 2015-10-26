<properties pageTitle="チュートリアル: Azure Active Directory と PagerDuty の統合 | Microsoft Azure" description="Azure Active Directory で PagerDuty を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と PagerDuty の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=522516)をクリックしてください。
  
このチュートリアルでは、Azure と PagerDuty の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   PagerDuty テナント
  
このチュートリアルを終了すると、PagerDuty に割り当てた Azure AD ユーザーは、PagerDuty 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  PagerDuty のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "シナリオ")
##PagerDuty のアプリケーション統合の有効化
  
このセクションでは、PagerDuty のアプリケーション統合を有効にする方法を説明します。

###PagerDuty のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**PagerDuty**」と入力します

    ![アプリケーション ギャラリー](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[PagerDuty]** を選び、**[完了]** をクリックしてアプリケーションを追加します。

    ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで PagerDuty に対する認証を行えるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **PagerDuty** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "シングル サインオンの構成")

2.  **[ユーザーの PagerDuty へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選び、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "シングル サインオンの構成")

3.  **[アプリ URL の構成]** ページで、**[PagerDuty サインイン URL]** ボックスに、"*https://\<tenant-name>.PagerDuty.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリ URL の構成](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "アプリ URL の構成")

4.  **[PagerDuty でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "シングル サインオンの構成")

5.  別の Web ブラウザーのウィンドウで、PagerDuty 企業サイトに管理者としてログインします。

6.  上部のメニューで **[アカウント設定]** をクリックします。

    ![アカウントの設定](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "アカウントの設定")

7.  **[シングル サインオン]** をクリックします。

    ![シングル サインオン](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "シングル サインオン")

8.  [シングル サインオンの有効化 (SSO)] ページで、次の手順に従います。

    ![シングル サインオンの有効化](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "シングル サインオンの有効化")

    1.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    2.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X.509 証明書]** テキストボックスに貼り付けます。
    3.  Azure ポータルの **[PagerDuty でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** 値をコピーし、**[ログイン URL]** ボックスに貼り付けます。
    4.  Azure ポータルの **[PagerDuty でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** 値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。
    5.  **[シングル サインオンを有効にする]** を選びます。
    6.  **[変更を保存]** をクリックします。

9.  Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが PagerDuty にログインできるようにするには、ユーザーを PagerDuty にプロビジョニングする必要があります。PagerDuty の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **PagerDuty** テナントにログインします。

2.  上部のメニューで **[ユーザー]** をクリックします。

3.  **[ユーザーの追加]** をクリックします。

    ![ユーザーの追加](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "ユーザーの追加")

4.  **[チームを招待]** ダイアログで、プロビジョニングする Azure AD ユーザーの**[氏名]** と **[電子メール]** アドレスを入力し、 **[追加]** 、 **[招待を送信]** の順にクリックします。

    ![チームの招待](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "チームの招待")

    >[AZURE.NOTE]PagerDuty アカウントを作成すると、追加したすべてのユーザーが招待を受信します。

>[AZURE.NOTE]PagerDuty から提供されている他の PagerDuty ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを PagerDuty に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **PagerDuty ** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->