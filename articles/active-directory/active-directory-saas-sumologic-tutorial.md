<properties 
    pageTitle="チュートリアル: Azure Active Directory と SumoLogic の統合 | Microsoft Azure" 
    description="Azure Active Directory で SumoLogic を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と SumoLogic の統合
  
このチュートリアルでは、Azure と SumoLogic の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   SumoLogic テナント
  
このチュートリアルを完了すると、SumoLogic に割り当てた Azure AD ユーザーは、SumoLogic 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  SumoLogic のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sumologic-tutorial/IC778549.png "シナリオ")

##SumoLogic のアプリケーション統合の有効化
  
このセクションでは、SumoLogic のアプリケーション統合を有効にする方法を説明します。

###SumoLogic のアプリ統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-sumologic-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-sumologic-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sumologic-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**sumologic**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-sumologic-tutorial/IC778550.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[SumoLogic]** を選び、**[完了]** をクリックしてアプリを追加します。

    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、SumoLogic で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書を SumoLogic テナントにアップロードする必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **SumoLogic** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configure single sign-on")

2.  **[ユーザーの SumoLogic へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configure single sign-on")

3.  **[アプリケーション URL の構成]** ページで、**[SumoLogic サインイン URL]** テキストボックスに、"*https://\<テナント名>.SumoLogic.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-sumologic-tutorial/IC778554.png "アプリケーション URL の構成")

4.  **[SumoLogic でのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure single sign-on](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configure single sign-on")

5.  別の Web ブラウザーのウィンドウで、SumoLogic 企業サイトに管理者としてログインします。

6.  **[Manage]、[Security]** の順に選択します。

    ![管理](./media/active-directory-saas-sumologic-tutorial/IC778556.png "管理")

7.  **[SAML]** をクリックします。

    ![グローバル セキュリティ設定](./media/active-directory-saas-sumologic-tutorial/IC778557.png "グローバル セキュリティ設定")

8.  **[Select a configuration or create a new one]** ボックスの一覧から **[Azure AD]** を選択し、**[Configure]** をクリックします。

    ![SAML 2.0 の構成](./media/active-directory-saas-sumologic-tutorial/IC778558.png "SAML 2.0 の構成")

9.  **[Configure SAML 2.0]** ダイアログで、次の手順に従います。

    ![SAML 2.0 の構成](./media/active-directory-saas-sumologic-tutorial/IC778559.png "SAML 2.0 の構成")

    1.  **[Configuration Name]** テキスト ボックスに、「**Azure AD**」と入力します。
    2.  **[Debug Mode]** を選択します。
    3.  Azure クラシック ポータルの **[SumoLogic でのシングル サインオンの構成]** ダイアログ ページで **[発行者 URL]** の値をコピーし、それを **[発行者]** テキスト ボックスに貼り付けます。
    4.  Azure クラシック ポータルの **[SumoLogic でのシングル サインオンの構成]** ダイアログ ページで **[認証要求 URL]** の値をコピーし、それを **[Authn Request URL (認証要求 URL)]** テキスト ボックスに貼り付けます。
    5.  ダウンロードした証明書から **Base-64 でエンコードされた**ファイルを作成します。

        >[AZURE.TIP] 詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    6.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
    7.  **[Email Attribute]** として、**[Use SAML subject]** を選択します。
    8.  **[SP initiated Login Configuration]** を選択します。
    9.  **[Login Path]** テキスト ボックスに、「**Azure**」と入力します。
    10. [**Save**] をクリックします。

10. Azure クラシック ポータルの **[SumoLogic でのシングル サインオンの構成]** ダイアログ ページで、シングル サインオンの構成確認を選択し、**[完了]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configure single sign-on")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが SumoLogic にログインできるようにするには、そのユーザーを SumoLogic にプロビジョニングする必要があります。SumoLogic の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **SumoLogic** テナントにログインします。

2.  **[Manage Users]** に移動します。

    ![Users](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Users")

3.  **[追加]** をクリックします。

    ![Users](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Users")

4.  **[New User]** ダイアログ ページで、次の手順に従います。

    ![新しいユーザー](./media/active-directory-saas-sumologic-tutorial/IC778563.png "新しいユーザー")

    1.  プロビジョニングする Azure AD アカウントに関連する情報を、**[First Name]**、**[Last Name]**、および **[Email]** テキスト ボックスに入力します。
    2.  ロールを選択します。
    3.  **[Status]** として、**[Active]** を選択します。
    4.  **[保存]** をクリックします。

>[AZURE.NOTE] SumoLogic から提供されている他の SumoLogic ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを SumoLogic に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **SumoLogic** アプリ統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-sumologic-tutorial/IC778564.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

<!---HONumber=AcomDC_0914_2016-->