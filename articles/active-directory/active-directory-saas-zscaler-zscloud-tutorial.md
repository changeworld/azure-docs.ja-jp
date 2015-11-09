<properties 
    pageTitle="チュートリアル: Azure Active Directory と Zscaler ZSCloud の統合 | Microsoft Azure" description="Azure Active Directory で Zscaler ZSCloud を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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


#チュートリアル: Azure Active Directory と Zscaler ZSCloud の統合
  
このチュートリアルでは、Azure と ZScaler ZSCloud の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ZScaler ZSCloud でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、ZScaler ZSCloud に割り当てた Azure AD ユーザーは、ZScaler ZSCloud 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  ZScaler ZSCloud のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  プロキシ設定の構成
4.  ユーザー プロビジョニングの構成
5.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800275.png "シナリオ")

##ZScaler ZSCloud のアプリケーション統合の有効化
  
このセクションでは、ZScaler ZSCloud のアプリケーション統合を有効にする方法を説明します。

###ZScaler ZSCloud のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**ZScaler ZSCloud**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800276.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[ZScaler ZSCloud]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ZScaler ZSCloud](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800277.png "ZScaler ZSCloud")

##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで ZScaler ZSCloud に対する認証を行うことができるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書を ZScaler ZSCloud テナントにアップロードする必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **ZScaler ZSCloud** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800278.png "シングル サインオンの構成")

2.  **[ユーザーの ZScaler ZSCloud へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800279.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[ZScaler ZSCloud サインオン URL]** ボックスに、ユーザーが ZScaler ZSCloud アプリケーションのサインオンに使用する URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800280.png "アプリケーション URL の構成")

    >[AZURE.NOTE]ご使用の環境の実際の値は、ZScaler ZSCloud サポート チームから入手できます。

4.  **[ZScaler ZSCloud でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800281.png "シングル サインオンの構成")

5.  別の Web ブラウザー ウィンドウで、ZScaler ZSCloud 企業サイトに管理者としてログインします。

6.  上部のメニューで **[Administration]** をクリックします。

    ![管理](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800206.png "管理")

7.  **[Manage Administrators & Roles]** をクリックし、**[Manage Users & Authentication]** をクリックします。

    ![ユーザーと認証の管理](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800207.png "ユーザーと認証の管理")

8.  **[Choose Authentication Options for your Organization]** セクションで、次の手順を実行します。

    ![認証](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800208.png "認証")

    1.  **[Authenticate using SAML Single Sign-On]** を選択します。
    2.  **[Configure SAML Single Sign-On Parameters]** をクリックします。

9.  **[Configure SAML Single Sign-On Parameters]** ダイアログ ページで、次の手順を実行し、**[Done]** をクリックします。

    ![シングル サインオン](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800209.png "シングル サインオン")

    1.  Azure ポータルの **[ZScaler ZSCloud でのシングル サインオンの構成]** ダイアログ ページで **[認証要求 URL]** の値をコピーし、**[URL of the SAML Portal to which users are sent for authentication]** ボックスに貼り付けます。
    2.  **[Attribute containing Login Name]** ボックスに、「**NameID**」と入力します。
    3.  ダウンロードした証明書をアップロードするには、**[Zscaler pem]** をクリックします。
    4.  **[Enable SAML Auto-Provisioning]** を選択します。

10. **[Configure User Authentication]** ダイアログ ページで、次の手順を実行します。

    ![管理](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800210.png "管理")

    1.  **[Save]** をクリックします。
    2.  **[Activate Now]** をクリックします。

11. Azure ポータルの **[ZScaler ZSCloud でのシングル サインオンの構成]** ダイアログ ページで、シングル サインオンの構成確認を選択し、**[完了]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800282.png "シングル サインオンの構成")

##プロキシ設定の構成

###Internet Explorer でプロキシ設定を構成するには

1.  **Internet Explorer** を起動します。

2.  **[ツール]** メニューの **[インターネット オプション]** を選択して、**[インターネット オプション]** ダイアログを開きます。

    ![インターネット オプション](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769492.png "インターネット オプション")

3.  **[接続]** タブをクリックします。

    ![接続](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769493.png "接続")

4.  **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5.  [プロキシ サーバー] セクションで、次の手順を実行します。

    ![プロキシ サーバー](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769494.png "プロキシ サーバー")

    1.  [LAN にプロキシ サーバーを使用する] をオンにします。
    2.  [アドレス] ボックスに、「**gateway.zscalerone.net**」と入力します。
    3.  [ポート] ボックスに、「**80**」と入力します。
    4.  **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。
    5.  **[OK]** をクリックして、**[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

6.  **[OK]** をクリックして、**[インターネット オプション]** ダイアログを閉じます。

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが ZScaler ZSCloud にログインできるようにするには、そのユーザーを ZScaler ZSCloud にプロビジョニングする必要があります。ZScaler ZSCloud の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Zscaler** テナントにログインします。

2.  **[Administration]** をクリックします。

    ![管理](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781035.png "管理")

3.  **[User Management]** をクリックします。

    ![追加](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "追加")

4.  **[Users]** タブで、**[Add]**.をクリックします。

    ![追加](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "追加")

5.  [Add User] セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781038.png "ユーザーの追加")

    1.  プロビジョニングする有効な AAD アカウントの**ユーザー ID**、**ユーザー表示名**、**パスワード**、**パスワードの確認**を入力し、**グループ**と**部署**を選択します。
    2.  **[Save]** をクリックします。

>[AZURE.NOTE]ZScaler ZSCloud から提供されている他の ZScaler ZSCloud ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを ZScaler ZSCloud に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **ZScaler ZSCloud** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800283.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

    ![あり](./media/active-directory-saas-zscaler-zscloud-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->