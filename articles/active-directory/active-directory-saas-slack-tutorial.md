<properties 
    pageTitle="チュートリアル: Azure Active Directory と Slack の統合 | Microsoft Azure" 
    description="Azure Active Directory で Slack を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Slack の統合
  
このチュートリアルでは、Azure と Slack の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Slack でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、Slack に割り当てた Azure AD ユーザーは、Slack 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Slack のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-slack-tutorial/IC794980.png "シナリオ")

##Slack のアプリケーション統合の有効化
  
このセクションでは、Slack のアプリケーション統合を有効にする方法について説明します。

###Slack のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-slack-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-slack-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-slack-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Slack**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-slack-tutorial/IC794981.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Slack]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![シナリオ](./media/active-directory-saas-slack-tutorial/IC796925.png "シナリオ")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、Slack で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Slack** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/IC794982.png "Configure Single Sign-On")

2.  **[ユーザーの Slack へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/IC794983.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページの **[Slack サインイン URL]** ボックスに、Slack テナントの URL (例: "*https://azuread.slack.com*") を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-slack-tutorial/IC794984.png "アプリケーション URL の構成")

4.  **[Slack でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、コンピューターで証明書ファイルをローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/IC794985.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Slacka 企業サイトに管理者としてログインします。

6.  **[Microsoft Azure AD]、[Team Settings]** の順に選択します。

    ![チーム設定](./media/active-directory-saas-slack-tutorial/IC794986.png "チーム設定")

7.  **[Team Settings]** セクションで、**[Authentication]** タブをクリックし、**[Change Settings]** をクリックします。

    ![チーム設定](./media/active-directory-saas-slack-tutorial/IC794987.png "チーム設定")

8.  **[SAML Authentication Settings]** ダイアログで、次の手順を実行します。

    ![SAML 設定](./media/active-directory-saas-slack-tutorial/IC794988.png "SAML 設定")

    1.  Azure クラシック ポータルで、**[Slack でのシングル サインオンの構成]** ダイアログ ページの **[SAML SSO URL]** の値をコピーし、**[SAML 2.0 Endpoint (HTTP) (SAML 2.0 エンドポイント (HTTP))]** ボックスに貼り付けます。
    2.  Azure クラシック ポータルで、**[Slack でのシングル サインオンの構成]** ダイアログ ページの **[発行者の URL]** の値をコピーし、**[ID プロバイダーの発行者]** ボックスに貼り付けます。
    3.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。
    
        >[AZURE.TIP] 詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    4.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[Public Certificate]** テキスト ボックスに貼り付けます。
    5.  **[Allow users to change their email address]** を選択解除します。
    6.  **[Allow users to choose their own username]** を選択します。
    7.  **[Authentication for your team must be used by (チームの認証の使用者)]** で、**[It’s optional (省略可能)]** を選択します。
    8.  **[Save Configuration]** をクリックします。

9.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/IC794989.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Slack にログインできるようにするには、ユーザーを Slack にプロビジョニングする必要があります。
  
Slack へのユーザー プロビジョニングの構成にあたって必要な操作はありません。割り当てられているユーザーが Slack にログインしようとすると、必要に応じて Slack アカウントが自動的に作成されます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Slack に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Slack** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-slack-tutorial/IC794990.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-slack-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0921_2016-->