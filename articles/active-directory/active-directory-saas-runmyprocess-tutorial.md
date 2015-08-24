<properties pageTitle="チュートリアル: Azure Active Directory と RunMyProcess の統合 | Microsoft Azure" description="Azure Active Directory で RunMyProcess を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と RunMyProcess の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=528571)をクリックしてください。
  
このチュートリアルでは、Azure と RunMyProcess の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   RunMyProcess テナント
  
このチュートリアルを完了すると、RunMyProcess に割り当てた Azure AD ユーザーは、RunMyProcess 企業サイト (サービス プロバイダーが開始したサインオン) または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  RunMyProcess のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーを割り当てる

![シナリオ](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "シナリオ")
##RunMyProcess のアプリケーション統合の有効化
  
このセクションでは、RunMyProcess のアプリケーション統合を有効にする方法について説明します。

###RunMyProcess のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**RunMyProcess**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[RunMyProcess]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで RunMyProcess に対する認証を行えるようにする方法について説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **RunMyProcess** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Configure Single Sign-On")

2.  **[ユーザーの RunMyProcess へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Configure Single Sign-On")

3.  **[アプリ URL の構成]** ページで、**[RunMyProcess サインオン URL]** テキストボックスに、"**http://company.runmyprocess.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリ URL の構成](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "アプリ URL の構成")

4.  **[RunMyProcess でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、RunMyProcess 企業サイトに管理者としてログインします。

6.  **[アカウント] > [構成]** に移動します。

    ![アカウント](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "アカウント")

7.  **[認証方法]** タブをクリックします。

8.  **[認証方法]** セクションで、次の手順に従います。

    ![SSO](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")

    1.  **[方法]** として、**[SSO with Samlv2]** を選択します。
    2.  Azure ポータルで、**[RunMyProcess でのシングル サインオンの構成]** ダイアログ ページの **[SAML SSO URL]** の値をコピーし、**[SSO リダイレクト]** テキストボックスに貼り付けます。
    3.  Azure ポータルで、**[RunMyProcess でのシングル サインオンの構成]** ダイアログ ページの **[シングル サインアウト サービス URL]** の値をコピーし、**[ログアウト リダイレクト]** テキストボックスに貼り付けます。
    4.  **[名前 ID 形式]** テキストボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**」と入力します。
    5.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    6.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[証明書]** テキストボックスに貼り付けます。
    7.  **[保存]** をクリックします。

9.  Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが RunMyProcess にログインできるようにするには、ユーザーを RunMyProcess にプロビジョニングする必要があります。RunMyProcess の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **RunMyProcess** 企業サイトに管理者としてログインします。

2.  **[アカウント] > [ユーザー]** に移動し、**[新しいユーザー]** をクリックします。

    ![新しいユーザー](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "新しいユーザー")

3.  **[ユーザーの設定]** セクションで、次の手順に従います。

    ![プロファイル](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "プロファイル")

    1.  関連するテキストボックスに、プロビジョニングする有効な AAD アカウントの **[名前]** と **[電子メール] **を入力します。
    2.  **[IDE 言語]**、**[言語]**、**[プロファイル]** を選択します。
    3.  **[アカウント作成の電子メールを自分に送信]** を選択します。
    4.  **[保存]** をクリックします。

>[AZURE.NOTE]他の RunMyProcess ユーザー アカウントの作成ツールまたは RunMyProcess から提供されている API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを RunMyProcess に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  RunMyProcess アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=August15_HO7-->