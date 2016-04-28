<properties 
    pageTitle="チュートリアル: Azure Active Directory と TeamSeer の統合 | Microsoft Azure" 
    description="Azure Active Directory で TeamSeer を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と TeamSeer の統合
  
このチュートリアルでは、Azure と TeamSeer の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   TeamSeer テナント
  
このチュートリアルを完了すると、TeamSeer に割り当てた Azure AD ユーザーは、TeamSeer 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  TeamSeer のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-teamseer-tutorial/IC789618.png "シナリオ")

##TeamSeer のアプリケーション統合の有効化
  
このセクションでは、TeamSeer のアプリケーション統合を有効にする方法について説明します。

###TeamSeer のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-teamseer-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-teamseer-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-teamseer-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**TeamSeer**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-teamseer-tutorial/IC789619.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[TeamSeer]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、TeamSeer で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **TeamSeer** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configure Single Sign-On")

2.  **[ユーザーの TeamSeer へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページで、**[サインイン URL]** テキスト ボックスに、"**http://www.teamseer.com/companyid**" というパターンで URL を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-teamseer-tutorial/IC789629.png "アプリケーション URL の構成")

4.  **[TeamSeer でのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、TeamSeer 企業サイトに管理者としてログインします。

6.  **[HR Admin]** に移動します。

    ![HR 管理](./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR 管理")

7.  **[Setup]** をクリックします。

    ![セットアップ](./media/active-directory-saas-teamseer-tutorial/IC789635.png "セットアップ")

8.  **[Set up SAML provider details]** をクリックします。

    ![SAML 設定](./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML 設定")

9.  SAML プロバイダーの詳細セクションで、次の手順に従います。

    ![SAML 設定](./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML 設定")

    1.  Azure ポータルの **[TeamSeer でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[URL]** テキスト ボックスに貼り付けます。
    2.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP] 詳細については、「[バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    3.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[IdP Public Certificate]** テキスト ボックスに貼り付けます。

10. SAML プロバイダー構成を完了するには、次の手順に従います。

    ![SAML 設定](./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML 設定")

    1.  **[Test Email Addresses]** に、テスト ユーザーの電子メール アドレスを入力します。
    2.  **[Issuer]**テキスト ボックスに、サービス プロバイダーの発行元 URL を入力します。
    3.  **[保存]** をクリックします。

11. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが TeamSeer にログインできるようにするには、ユーザーを TeamSeer にプロビジョニングする必要があります。TeamSeer の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **TeamSeer** 企業サイトに管理者としてログインします。

2.  次の手順に従います。

    ![HR 管理](./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR 管理")

    1.  **[HR Admin]、[Users]** の順に選択します。
    2.  **[Run the New User wizard]** をクリックします。

3.  **[User Details]** セクションで、次の手順に従います。

    ![ユーザーの詳細](./media/active-directory-saas-teamseer-tutorial/IC789641.png "ユーザーの詳細")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの**名**、**姓**、**ユーザー名 (電子メール アドレス)** を入力します。
    2.  **[次へ]** をクリックします。

4.  画面の指示に従って新しいユーザーを追加し、**[完了]** をクリックします。

>[AZURE.NOTE] TeamSeer から提供されている他の TeamSeer ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを TeamSeer に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **TeamSeer** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-teamseer-tutorial/IC789642.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0413_2016-->