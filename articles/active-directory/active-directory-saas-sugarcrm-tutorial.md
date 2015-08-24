<properties pageTitle="チュートリアル: Azure Active Directory と Sugar CRM の統合 | Microsoft Azure" description="Sugar CRM と Azure Active Directory を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Sugar CRM の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=534768)をクリックしてください。
  
このチュートリアルでは、Azure と Sugar CRM の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Sugar CRM でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、Sugar CRM に割り当てた Azure AD ユーザーは、Sugar CRM 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Sugar CRM のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "シナリオ")

##Sugar CRM のアプリケーション統合の有効化
  
このセクションでは、Sugar CRM のアプリケーション統合を有効にする方法を説明します。

###Sugar CRM のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に「**Sugar CRM**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Sugar CRM]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、Sugar CRM で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書を Sugar CRM テナントにアップロードする必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **[Sugar CRM]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configure Single Sign-On")

2.  **[ユーザーの Sugar CRM へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページの **[サインオン URL]** テキストボックスに、ユーザーが Sugar CRM アプリケーションにサインオンするときに使用する URL (例: **http://company.sugarondemand.com*") を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "アプリケーション URL の構成")

4.  **[Sugar CRMでのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Sugar CRM 企業サイトに管理者としてログインします。

6.  **[Admin]** に移動します。

    ![管理者](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "管理者")

7.  **[Administration]** セクションで、**[Password Management]** をクリックします。

    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "管理")

8.  **[Enable SAML Authentication]** を選択します。

    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "管理")

9.  **[SAML Authentication]** セクションで、次の手順に従います。

    ![SAML 認証](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML 認証")

    1.  Azure ポータルの **[Sugar CRMでのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、**[Login URL]** テキスト ボックスに貼り付けます。
    2.  Azure ポータルの **[Sugar CRMでのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、**[SLO URL]** テキスト ボックスに貼り付けます。
    3.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。

        >[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    4.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
    5.  **[保存]** をクリックします。

10. Azure ポータルの **[Sugar CRM でのシングル サインオンの構成]** ダイアログ ページで、シングル サインオンの構成確認を選択し、**[完了]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Sugar CRM にログインできるようにするには、そのユーザーを Sugar CRM にプロビジョニングする必要があります。Sugar CRM の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **Spring CM** 企業サイトに管理者としてログインします。

2.  **[Admin]** に移動します。

    ![管理者](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "管理者")

3.  **[Administration]** セクションで、**[User Management]** をクリックします。

    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "管理")

4.  **[Users]、[Create New User]** の順に選択します。

    ![新しいユーザーの作成](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "新しいユーザーの作成")

5.  **[User Profile]** タブで、次の手順に従います。

    ![新しいユーザー](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "新しいユーザー")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントのユーザー名、姓、および電子メール アドレスを入力します。

6.  **[Status]** として、**[Active]** を選択します。

7.  [Password] タブで、次の手順に従います。

    ![新しいユーザー](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "新しいユーザー")

    1.  該当するテキスト ボックスにパスワードを入力します。
    2.  **[保存]** をクリックします。

>[AZURE.NOTE]Sugar CRM から提供されている他の Sugar CRM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Sugar CRM に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Sugar CRM** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確認します。

    ![あり](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=August15_HO7-->