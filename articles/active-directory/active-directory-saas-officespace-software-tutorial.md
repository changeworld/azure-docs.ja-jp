<properties 
    pageTitle="チュートリアル: Azure Active Directory と OfficeSpace Software の統合 | Microsoft Azure" 
    description="Azure Active Directory で OfficeSpace Software を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と OfficeSpace Software の統合
  
このチュートリアルの目的は、Azure と OfficeSpace Software の統合を示すことです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   シングル サインオン対応の OfficeSpace Software サブスクリプション
  
このチュートリアルを完了すると、OfficeSpace Software に割り当てた Azure AD ユーザーは、OfficeSpace Software 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  OfficeSpace Software のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "シナリオ")
##OfficeSpace Software のアプリケーション統合の有効化
  
このセクションでは、OfficeSpace Software のアプリケーション統合を有効にする方法を説明します。

###OfficeSpace Software のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**OfficeSpace Software**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[OfficeSpace Software]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで OfficeSpace Software に対する認証を行えるようにする方法を説明します。OfficeSpace Software にシングル サインオンを構成するには、証明書の拇印の値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **OfficeSpace Software** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "シングル サインオンの構成")

2.  **[ユーザーの OfficeSpace Software へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[OfficeSpace Software サインオン URL]** ボックスに、ユーザーが OfficeSpace Software アプリケーションのサインオンに使用する URL (例: "**https://company.officespacesoftware.com*") を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "アプリケーション URL の構成")

4.  **[OfficeSpace Software でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターのローカルに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "シングル サインオンの構成")

5.  別の Web ブラウザーのウィンドウで、管理者として OfficeSpace Software 企業サイトにログインします。

6.  **[管理者] > [コネクタ]** をクリックします。

    ![管理者](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "管理者")

7.  **[SAML 承認]** をクリックします。

    ![コネクタ](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "コネクタ")

8.  **[SAML 承認]** セクションで、次の手順を実行します。

    ![SAML の構成](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "SAML の構成")

    1.  Azure ポータルの **[OfficeSpace Software でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、**[ログアウト プロバイダー URL]** ボックスに貼り付けます。
    2.  Azure ポータルの **[OfficeSpace Software でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログアウト URL]** の値をコピーし、**[クライアント IDP ターゲット URL]** ボックスに貼り付けます。
    3.  エクスポートした証明書から **[拇印]** の値をコピーし、**[クライアント IDP 証明書の指紋]** ボックスに貼り付けます。  

        >[AZURE.TIP]詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

    4.  **[設定の保存]** をクリックします。

9.  Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーの OfficeSpace Software へのログインを有効にするためには、ユーザーを OfficeSpace Software にプロビジョニングする必要があります。OfficeSpace Software の場合は、プロビジョニングは自動化されています。アイテムを操作することはありません。最初のシングル サインオンの試行中に、必要に応じてユーザーが自動的に作成されます。

>[AZURE.NOTE]他の OfficeSpace Software ユーザー アカウントの作成ツールまたは OfficeSpace Software から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを OfficeSpace Software に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **OfficeSpace Software** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

    ![あり](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->