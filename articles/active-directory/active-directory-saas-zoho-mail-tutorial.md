<properties 
    pageTitle="チュートリアル: Azure Active Directory と Zoho Mail の統合 | Microsoft Azure" 
    description="Azure Active Directory で Zoho Mail を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と Zoho Mail の統合
  
このチュートリアルでは、Azure と Zoho Mail の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Zoho Mail テナント
  
このチュートリアルを完了すると、Zoho Mail に割り当てた Azure AD ユーザーは、Zoho Mail 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Zoho Mail のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "シナリオ")

##Zoho Mail のアプリケーション統合の有効化
  
このセクションでは、Zoho Mail のアプリケーション統合を有効にする方法を説明します。

###Zoho Mail のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Zoho Mail**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Zoho Mail]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Zoho Mail に対する認証を行うことができるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (バイナリ証明書をテキスト ファイルに変換する方法)」をご覧ください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **Zoho Mail** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "シングル サインオンの構成")

2.  **[ユーザーの Zoho Mail へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configure Single Sign-On")

3.  **[アプリ URL の構成]** ページで、**[Zoho Mail サインオン URL]** テキスト ボックスに、"**http://company.ZohoMail.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "アプリケーション URL の構成")

4.  **[Zoho Mail でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Zoho Mail 企業サイトに管理者としてログインします。

6.  **[コントロール パネル]**に移動します。

    ![コントロール パネル](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "コントロール パネル")

7.  **[SAML 認証]** タブをクリックします。

    ![SAML 認証](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "SAML 認証")

8.  **[SAML 認証の詳細]** セクションで、次の手順に従います。

    ![SAML 認証の詳細](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "SAML 認証の詳細")

    1.  Azure ポータルの **[Zoho Mail でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーして、**[ログイン URL]** テキストボックスに貼り付けます。
    2.  Azure ポータルの **[Zoho Mail でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログアウト URL]** の値をコピーして、**[ログアウト URL]** テキストボックスに貼り付けます。
    3.  Azure ポータルの **[Zoho Mail でのシングル サインオンの構成]** ダイアログ ページで **[パスワード変更 URL]** の値をコピーして、**[パスワード変更 URL]** テキストボックスに貼り付けます。
    4.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP]詳細については、[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o) をご覧ください。

    5.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[公開キー]** テキストボックスに貼り付けます。
    6.  **[アルゴリズム]** として **[RSA]** を選択します。
    7.  **[OK]** をクリックします。

9.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Zoho Mail にログインできるようにするには、そのユーザーを Zoho Mail にプロビジョニングする必要があります。Zoho Mail の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **Zoho Mail** 企業サイトに管理者としてログインします。

2.  **[コントロール パネル] > [メールとドキュメント]** に移動します。

3.  **[ユーザーの詳細] > [ユーザーの追加]** に移動します。

    ![ユーザーの追加](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "ユーザーの追加")

4.  **[ユーザーの追加]** ダイアログで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "ユーザーの追加")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの **[名]**、**[姓]**、**[電子メール ID]**、**[パスワード]** を入力します。
    2.  **[OK]** をクリックします。  

        >[AZURE.NOTE]Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE]Zoho Mail から提供されている他の Zoho Mail ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Zoho Mail に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Zoho Mail** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->