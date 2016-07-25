<properties 
    pageTitle="チュートリアル: Azure Active Directory と PolicyStat の統合 | Microsoft Azure" 
    description="Azure Active Directory で PolicyStat を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="07/07/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と PolicyStat の統合
  
このチュートリアルでは、Azure と PolicyStat の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   PolicyStat テナント
  
このチュートリアルを完了すると、PolicyStat に割り当てた Azure AD ユーザーは、PolicyStat 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  PolicyStat のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-policystat-tutorial/IC808662.png "シナリオ")
##PolicyStat のアプリケーション統合の有効化
  
このセクションでは、PolicyStat のアプリケーション統合を有効にする方法を説明します。

###PolicyStat のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-policystat-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-policystat-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-policystat-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**PolicyStat**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-policystat-tutorial/IC808627.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[PolicyStat]** を選び、**[完了]** をクリックしてアプリケーションを追加します。

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで PolicyStat に対する認証を行えるようにする方法を説明します。PolicyStat アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを **SAML トークンの属性**の構成に追加する必要があります。次のスクリーンショットはその例です。

![属性](./media/active-directory-saas-policystat-tutorial/IC808628.png "属性")

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **PolicyStat** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configure Single Sign-On")

2.  **[ユーザーの PolicyStat へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選び、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configure Single Sign-On")

3.  **[アプリ設定の構成]** ページの **[サインオン URL]** テキストボックスに、ユーザーが PolicyStat アプリケーションのサインオンに使用する URL (例: *“https://demo-azure.policystat.com”*) を入力して、**[次へ]** をクリックします。

    ![Configure App Settings](./media/active-directory-saas-policystat-tutorial/IC808631.png "Configure App Settings")

4.  **[PolicyStat でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、PolicyStat 企業サイトに管理者としてログインします。

6.  **[Admin]** タブをクリックしてから、左側のナビゲーション ウィンドウの **[Single Sign-On Configuration]** をクリックします。

    ![管理者メニュー](./media/active-directory-saas-policystat-tutorial/IC808633.png "管理者メニュー")

7.  **[Setup]** セクションで、**[Enable Single Sign-on Integration]** を選びます。

    ![シングル サインオンの構成](./media/active-directory-saas-policystat-tutorial/IC808634.png "シングル サインオンの構成")

8.  **[Configure Attributes]** をクリックして 、**[Configure Attributes]** セクションで次の手順に従います。

    ![シングル サインオンの構成](./media/active-directory-saas-policystat-tutorial/IC808635.png "シングル サインオンの構成")

    1.  **[Username Attribute]** テキストボックスに、「**uid**」と入力します。
    2.  **[First Name Attribute]** テキストボックスに、「**firstname**」と入力します。
    3.  **[Last Name Attribute]** テキストボックスに、「**lastname**」と入力します。
    4.  **[Email Attribute]** テキストボックスに、「**emailaddress**」と入力します。
    5.  **[変更を保存]** をクリックします。

9.  **[Your IDP Metadata]** をクリックして、**[Your IDP Metadata]** セクションで次の手順に従います。

    ![シングル サインオンの構成](./media/active-directory-saas-policystat-tutorial/IC808635.png "シングル サインオンの構成")

    1.  ダウンロードしたメタデータ ファイルの内容をコピーし、**[Your Identity Provider Metadata]** テキストボックスに貼り付けます。
    2.  **[変更を保存]** をクリックします。

10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configure Single Sign-On")

11. 12. 上部のメニューで、**[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。

    ![属性](./media/active-directory-saas-policystat-tutorial/IC795920.png "属性")

13. 必要な属性のマッピングを追加するには、次の手順を実行します。

    ![属性](./media/active-directory-saas-policystat-tutorial/IC804823.png "属性")

    1.  **[ユーザー属性の追加]** をクリックします。
    2.  **[属性名]** テキストボックスに、「**uid**」と入力します。
    3.  **[属性値]** テキストボックスで、「**ExtractMailPrefix()**」を選びます。
    4.  **[メール]** 一覧で、「**User.mail**」を選びます。
    5.  **[完了]** をクリックします。
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが PolicyStat にログインできるようにするには、ユーザーを PolicyStat にプロビジョニングする必要があります。PolicyStat は、ジャストインタイム ユーザー プロビジョニングをサポートしています。つまり、PolicyStat にユーザーを手動で追加する必要はありません。ユーザーはシングル サインオンの最初のログイン時に自動的に追加されます。

>[AZURE.NOTE]PolicyStat から提供されている他の PolicyStat ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを PolicyStat に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **PolicyStat** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-policystat-tutorial/IC808636.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-policystat-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0713_2016-->