<properties 
    pageTitle="チュートリアル: Azure Active Directory と UserVoice の統合 | Microsoft Azure" 
    description="Azure Active Directory で UserVoice を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" 
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

#チュートリアル: Azure Active Directory と UserVoice の統合
  
このチュートリアルでは、Azure と UserVoice の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   UserVoice のテナント
  
このチュートリアルを完了すると、UserVoice に割り当てた Azure AD ユーザーは、UserVoice 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  UserVoice のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-uservoice-tutorial/IC777514.png "シナリオ")

##UserVoice のアプリケーション統合の有効化
  
このセクションでは、UserVoice のアプリケーション統合を有効にする方法を説明します。

###UserVoice のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-uservoice-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-uservoice-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-uservoice-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に「**UserVoice**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-uservoice-tutorial/IC777513.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[UserVoice]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで UserVoice に対する認証を行うことができるようにする方法を説明します。UserVoice のシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **[UserVoice]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configure single sign-on")

2.  **[ユーザーの UserVoice へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configure single sign-on")

3.  **[アプリケーション URL の構成]** ページで、**[UserVoice サインイン URL]** ボックスに、"*https://\<テナント名>.UserVoice.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-uservoice-tutorial/IC777517.png "アプリケーション URL の構成")

4.  **[UserVoice でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルを **c:\\UserVoice.cer** としてローカルに保存します。

    ![Configure single sign-on](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configure single sign-on")

5.  別の Web ブラウザーのウィンドウで、UserVoice 企業サイトに管理者としてログインします。

6.  上部のツール バーの [設定] をクリックし、[Web ポータル] を選択します。

    ![Settings](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Settings")

7.  **[Web ポータル]** タブの **[ユーザー認証]** セクションで、**[編集]** をクリックして **[ユーザー認証の編集]** ダイアログ ページを開きます。

    ![Web ポータル](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web ポータル")

8.  **[ユーザー認証の編集]** ダイアログ ページで、次の手順に従います。

    ![ユーザー認証の編集](./media/active-directory-saas-uservoice-tutorial/IC777521.png "ユーザー認証の編集")

    1.  **[シングル サインオン]** をクリックします。
    2.  Azure クラシック ポータルで、**[UserVoice でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[SSO リモート サインイン]** ボックスに貼り付けます。
    3.  Azure クラシック ポータルで、**[UserVoice でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[SSO リモート サインアウト]** ボックスに貼り付けます。
    4.  エクスポートした証明書から **[サムプリント]** の値をコピーし、**[現在の証明書 SHA1 フィンガープリント]** ボックスに貼り付けます。

        >[AZURE.TIP] 詳細については、「[How to retrieve a certificate's thumbprint value （証明書のサムプリント値を取得する方法）](http://youtu.be/YKQF266SAxI)」を参照してください。

    5.  **[認証設定の保存]** をクリックします。

9.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configure single sign-on")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが UserVoice にログインできるようにするには、そのユーザーを UserVoice にプロビジョニングする必要があります。UserVoice の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **UserVoice** テナントにログインします。

2.  **[設定]** に移動します。

    ![Settings](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Settings")

3.  **[全般]** をクリックします。

4.  **[エージェントとアクセス許可]** をクリックします。

    ![エージェントとアクセス許可](./media/active-directory-saas-uservoice-tutorial/IC777812.png "エージェントとアクセス許可")

5.  **[管理者の追加]** をクリックします。

    ![管理者の追加](./media/active-directory-saas-uservoice-tutorial/IC777813.png "管理者の追加")

6.  **[管理者の招待]** ダイアログで、次の手順を実行します。

    ![管理者の招待](./media/active-directory-saas-uservoice-tutorial/IC777814.png "管理者の招待")

    1.  [電子メール] ボックスに、プロビジョニングするアカウントの電子メール アドレスを入力し、**[追加]** をクリックします。
    2.  **[招待]** をクリックします。

>[AZURE.NOTE] UserVoice から提供されている他の UserVoice ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを UserVoice に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **UserVoice** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-uservoice-tutorial/IC777523.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

<!---HONumber=AcomDC_0914_2016-->