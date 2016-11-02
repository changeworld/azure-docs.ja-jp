<properties 
    pageTitle="チュートリアル: Azure Active Directory と Bonus.ly の統合 | Microsoft Azure" 
    description="Azure Active Directory で Bonus.ly を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と Bonus.ly の統合

このチュートリアルでは、Azure と Bonus.ly の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Bonus.ly のテスト テナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Bonus.ly のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-bonus-tutorial/IC773679.png "シナリオ")
##Bonus.ly のアプリケーション統合の有効化

このセクションでは、Bonus.ly のアプリケーション統合を有効にする方法について説明します。

###Bonus.ly のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Enable single sign-on](./media/active-directory-saas-bonus-tutorial/IC773680.png "Enable single sign-on")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-bonus-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-bonus-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-bonus-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Bonus.ly**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-bonus-tutorial/IC773681.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Bonus.ly]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Bonus.ly に対する認証を行えるようにする方法を説明します。Bonus.ly のシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Bonus.ly** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configure single sign-on")

2.  **[ユーザーの Bonus.ly へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configure single sign-on")

3.  **[アプリケーション URL の構成]** ページで、**[Bonus.ly テナント URL]** ボックスに、"*https://\<テナント名>.Bonus.ly*" のパターンで URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-bonus-tutorial/IC773684.png "アプリケーション URL の構成")

4.  **[Bonus.ly でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルを **c:\\Bonusly.cer** としてローカルに保存します。

    ![Configure single sign-on](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configure single sign-on")

5.  別のブラウザー ウィンドウで、**Bonus.ly** テナントにログインします。

6.  上部のツール バーの **[Settings]** をクリックし、**[Integrations and apps]** を選択します。

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  **[Single Sign-On]** の **[SAML]** を選択します。

8.  **[SAML]** ダイアログ ページで、次の手順を実行します。

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  Azure クラシック ポータルで、**[Bonus.ly でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[IdP SSO target URL]** ボックスに貼り付けます。
    2.  Azure クラシック ポータルで、**[Bonus.ly でのシングル サインオンの構成]** ダイアログ ページの **[発行者 ID]** の値をコピーし、**[IdP Issuer]** ボックスに貼り付けます。
    3.  Azure クラシック ポータルで、**[Bonus.ly でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[IdP ログイン URL]** ボックスに貼り付けます。
    4.  エクスポートした証明書から **[サムプリント]** の値をコピーし、**[Cert Fingerprint]** ボックスに貼り付けます。

        >[AZURE.TIP] 詳細については、「[How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) (証明書のサムプリント値を取得する方法)」をご覧ください。

9.  **[Save]** をクリックします。

10. Microsoft Azure クラシック ポータルで構成確認を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configure single sign-on")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Bonus.ly にログインできるようにするには、ユーザーを Bonus.ly にプロビジョニングする必要があります。Bonus.ly の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  Web ブラウザー ウィンドウで、Bonus.ly テナントにログインします。

2.  **[設定]** をクリックします

    ![Settings](./media/active-directory-saas-bonus-tutorial/IC781041.png "Settings")

3.  **[Users and bonuses]** タブをクリックします。

    ![Users and bonuses](./media/active-directory-saas-bonus-tutorial/IC781042.png "Users and bonuses")

4.  **[Manage Users]** をクリックします。

    ![Manage Users](./media/active-directory-saas-bonus-tutorial/IC781043.png "Manage Users")

5.  **[Add User]** をクリックします。

    ![ユーザーの追加](./media/active-directory-saas-bonus-tutorial/IC781044.png "ユーザーの追加")

6.  **[Add User]** ダイアログで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-bonus-tutorial/IC781045.png "ユーザーの追加")

    1.  **[Email]**、**[First name]**、**[Last name]** の各ボックスに、プロビジョニングする有効な AAD アカウントの対応する値を入力します。
    2.  **[保存]** をクリックします。

    >[AZURE.NOTE] アカウントがアクティブになる前に、AAD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE] Bonus.ly から提供されている他の Bonus.ly ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Bonus.ly に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  Bonus.ly アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-bonus-tutorial/IC773690.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-bonus-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0713_2016-->