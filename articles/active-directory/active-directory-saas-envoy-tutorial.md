<properties 
    pageTitle="チュートリアル: Azure Active Directory と Envoy の統合 | Microsoft Azure" 
    description="Azure Active Directory で Envoy を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Envoy の統合
  
このチュートリアルでは、Azure と Envoy の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Envoy テナント
  
このチュートリアルを完了すると、Envoy に割り当てた Azure AD ユーザーは、Envoy 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Envoy のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-envoy-tutorial/IC776759.png "シナリオ")
##Envoy のアプリケーション統合の有効化
  
このセクションでは、Envoy のアプリケーション統合を有効にする方法について説明します。

###Envoy のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-envoy-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-envoy-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-envoy-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Envoy**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-envoy-tutorial/IC776760.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Envoy]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Envoy に対する認証を行うことができるようにする方法を説明します。Envoy のシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **Envoy** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Enable single sign-on](./media/active-directory-saas-envoy-tutorial/IC776778.png "Enable single sign-on")

2.  **[ユーザーの Envoy へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configure single sign-on")

3.  **[アプリケーション URL の構成]** ページの **[Envoy サインイン URL]** ボックスに、"https://\<テナント名>.Envoy.com" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![Configure app URL](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configure app URL")

4.  **[Envoy でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルを **c:\\Envoy.cer** としてローカルに保存します。

    ![Configure single sign-on](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configure single sign-on")

5.  別の Web ブラウザー ウィンドウで、Envoy 企業サイトに管理者としてログインします。

6.  上部のツールバーで **[設定]** をクリックします。

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  **[会社]** をクリックします。

    ![会社](./media/active-directory-saas-envoy-tutorial/IC776783.png "会社")

8.  **[SAML]** をクリックします。

    ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  **[SAML 認証]** 構成セクションで、次の手順を実行します。

    ![SAML 認証](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML 認証")

    >[AZURE.NOTE] [HQ 場所 ID] の値は、アプリケーションによって自動的に生成されます。

    1.  エクスポートした証明書から **[拇印]** の値をコピーし、**[指紋]** ボックスに貼り付けます。  

        >[AZURE.TIP]詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

    2.  Azure ポータルの **[Envoy でのシングル サインオンの構成]** ダイアログ ページで、**[SAML SSO URL]** の値をコピーし、**[ID プロバイダー HTTP SAML URL]** ボックスに貼り付けます。
    3.  **[変更を保存]** をクリックします。

10. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configure single sign-on")
##ユーザー プロビジョニングの構成
  
Envoy へのユーザー プロビジョニングの構成にあたって必要な操作はありません。割り当てられたユーザーがアクセス パネルを使用して Envoy にログインしようとすると、そのユーザーが存在するかどうかが Envoy によって確認されます。使用可能なユーザー アカウントがない場合、ユーザー アカウントは自動的に作成されます。
##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Envoy に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Envoy** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-envoy-tutorial/IC776787.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-envoy-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0121_2016-->