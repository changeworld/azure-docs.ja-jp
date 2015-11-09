<properties 
    pageTitle="チュートリアル: Azure Active Directory と Mozy Enterprise の統合 | Microsoft Azure" 
    description="Azure Active Directory で Mozy Enterprise を使用してシングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と Mozy Enterprise の統合
  
このチュートリアルでは、Azure と Mozy Enterprise の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Mozy Enterprise テナント
  
このチュートリアルを完了すると、Mozy Enterprise に割り当てた Azure AD ユーザーは、Mozy Enterprise の企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Mozy Enterprise のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "シナリオ")
##Mozy Enterprise のアプリケーション統合の有効化
  
このセクションでは、Mozy Enterprise のアプリケーション統合を有効にする方法を説明します。

###Mozy Enterprise のアプリケーション統合を有効にするには、次の手順に従います。

1.  Microsoft Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Mozy Enterprise**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Mozy Enterprise]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Mozy Enterprise に対する認証を行うことができるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書を Mozy Enterprise テナントにアップロードする必要があります。この手順に慣れていない場合は、「[バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **[Mozy Enterprise]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "シングル サインオンの構成")

2.  **[ユーザーの Mozy Enterprise へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "シングル サインオンの構成")

3.  **[アプリ URL の構成]** ページで、**[Mozy Enterpris サインイン URL]** ボックスに、"*https://\<tenant-name>.Mozyenterprise.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "アプリケーション URL の構成")

4.  **[Mozy Enterprise でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "シングル サインオンの構成")

5.  別の Web ブラウザーのウィンドウで、Mozy Enterprise の企業サイトに管理者としてログインします。

6.  **[構成]** セクションで、**[認証ポリシー]** をクリックします。

    ![認証ポリシー](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "認証ポリシー")

7.  **[認証ポリシー]** セクションで、次の手順に従います。

    ![認証ポリシー](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "認証ポリシー")

    1.  **[プロバイダー]** に **[ディレクトリ サービス]** を選択します。
    2.  **[LDAP プッシュを使用]** を選択します。
    3.  **[SAML 認証]** タブをクリックします。
    4.  Azure ポータルの **[Mozy Enterprise でのシングル サインオンの構成]** ダイアログ ページで **[認証要求 URL]** の値をコピーし、それを **[認証要求 URL]** テキストボックスに貼り付けます。
    5.  Azure ポータルの **[Mozy Enterprise でのシングル サインオンの構成]** ダイアログ ページで **[ID プロバイダー ID]** の値をコピーし、それを **[SAML エンドポイント]** テキストボックスに貼り付けます。
    6.  ダウンロードした証明書から **Base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP]詳細については、「[バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    7.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[SAML 証明書]** テキストボックスに貼り付けます。
    8.  **[Enable SSO for Admins to log in with their network credentials (管理者のネットワーク資格情報を使用した SSO を許可する)]** を選択します。
    9.  **[変更を保存]** をクリックします。

8.  Azure ポータルの **[Sugar CRMMozy Enterprise でのシングル サインオンの構成]** ダイアログ ページで、シングル サインオンの構成確認を選択し、**[完了]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Mozy Enterprise にログインできるようにするには、ユーザーを Mozy Enterprise にプロビジョニングする必要があります。Mozy Enterprise の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **Mozy Enterprise** テナントにログインします。

2.  **[ユーザー]** をクリックして、**[新規ユーザーの追加]** をクリックします。

    ![ユーザー](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "ユーザー")

    >[AZURE.NOTE]**[新規ユーザーの追加]** オプションは、**[認証ポリシー]** で **[Mozy]** が選択されている場合にのみ表示されます。 SAML 認証が構成されている場合、ユーザーはシングル サインオンでの初回ログイン時に自動的に追加されます。

3.  新しいユーザーのダイアログで、次の手順に従います。

    ![ユーザーの追加](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "ユーザーの追加")

    1.  **[グループを選択]** の一覧で、グループを選択します。
    2.  **[ユーザーのタイプ]** の一覧で、タイプを選択します。
    3.  **[ユーザー名]** ボックスに、Azure AD ユーザーの名前を入力します。
    4.  **[メール]** ボックスに、Azure AD ユーザーのメール アドレスを入力します。
    5.  **[ユーザーに指示メールを送信する]** を選択します。
    6.  **[ユーザーの追加]** をクリックします。

    >[AZURE.NOTE]ユーザーの作成後、Azure AD ユーザーに、アカウントがアクティブになる前にアカウントを確認するためのリンクが記載されたメールが送信されます。

>[AZURE.NOTE]Mozy Enterprise から提供されている他の Mozy Enterprise ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
 
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Mozy Enterprise に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  Mozy Enterprise アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->