<properties 
    pageTitle="チュートリアル: Azure Active Directory と TalentLMS の統合 | Microsoft Azure" 
    description="Azure Active Directory で TalentLMS を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="06/21/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と TalentLMS の統合
  
このチュートリアルでは、Azure と TalentLMS の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   TalentLMS テナント
  
このチュートリアルを完了すると、TalentLMS に割り当てた Azure AD ユーザーは、TalentLMS 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  TalentLMS のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-talentlms-tutorial/IC777289.png "シナリオ")

##TalentLMS のアプリケーション統合の有効化
  
このセクションでは、TalentLMS のアプリケーション統合を有効にする方法について説明します。

###TalentLMS のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-talentlms-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-talentlms-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-talentlms-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**TalentLMS**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-talentlms-tutorial/IC777290.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[TalentLMS]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで TalentLMS に対する認証を行うことができるようにする方法を説明します。TalentLMS のシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **TalentLMS** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configure single sign-on")

2.  **[ユーザーの TalentLMS へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-talentlms-tutorial/IC777293.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページで、**[TalentLMS サインイン URL]** テキスト ボックスに、"*https://\<テナント名>.TalentLMS.com*" というパターンで URL を入力し、**[次へ]** をクリックします。

    ![サインオン URL](./media/active-directory-saas-talentlms-tutorial/IC777294.png "サインオン URL")

4.  **[TalentLMS でのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルを **c:\\TalentLMS.cer** としてローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、TalentLMS 企業サイトに管理者としてログインします。

6.  **[Account & Settings]** セクションで、**[Users]** タブをクリックします。

    ![アカウントと設定](./media/active-directory-saas-talentlms-tutorial/IC777296.png "アカウントと設定")

7.  **[Single Sign-On (SSO)]** をクリックします。

8.  [Single Sign-On] セクションで、次の手順に従います。

    ![シングル サインオン](./media/active-directory-saas-talentlms-tutorial/IC777297.png "シングル サインオン")

    1.  **[SSO integration type]** ボックスの一覧から **[SAML 2.0]** を選択します。
    2.  Azure クラシック ポータルの **[TalentLMS でのシングル サインオンの構成]** ダイアログ ページで、**[ID プロバイダーの ID]** 値をコピーして、**[Identity provider (IdP)]** テキスト ボックスに貼り付けます。
    3.  エクスポートした証明書から **[サムプリント]** 値をコピーし、**[証明書フィンガープリント]** テキストボックスに貼り付けます。

        >[AZURE.TIP] 詳細については、「[How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

    4.  Azure クラシック ポータルの **[TalentLMS でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、**[Remote sign-in URL]** テキスト ボックスに貼り付けます。
    5.  Azure クラシック ポータルの **[TalentLMS でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログアウト URL]** の値をコピーし、**[Remote sign-out URL]** テキスト ボックスに貼り付けます。
    6.  **[TargetedID]** テキスト ボックスに、**「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**」と入力します。
7.  **[First name]** テキスト ボックスに、**「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**」と入力します。
8.  **[Last name]** テキスト ボックスに、**「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**」と入力します。
9.  **[Email]** テキスト ボックスに、**「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。
10. **[保存]** をクリックします。

9.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが TalentLMS にログインできるようにするには、そのユーザーを TalentLMS にプロビジョニングする必要があります。TalentLMS の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **TalentLMS** テナントにログインします。

2.  **[Users]** をクリックし、**[Add User]** をクリックします。

3.  **[Add user]** ダイアログ ページで、次の手順に従います。

    ![ユーザーの追加](./media/active-directory-saas-talentlms-tutorial/IC777299.png "ユーザーの追加")

    1.  Azure AD のユーザー アカウントに関連する属性の値を次のテキスト ボックスに入力します。**[First name]**、**[Last name]**、**[Email address]**。
    2.  **[Add User]** をクリックします。

>[AZURE.NOTE] TalentLMS から提供されている他の TalentLMS ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを TalentLMS に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **TalentLMS **アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-talentlms-tutorial/IC777300.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0622_2016-->