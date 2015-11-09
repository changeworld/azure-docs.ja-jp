<properties 
    pageTitle="チュートリアル: Azure Active Directory と Onit の統合 | Microsoft Azure" 
    description="Azure Active Directory で Onit を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と Onit の統合
  
このチュートリアルの目的は、Azure と Onit の統合を紹介することです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Onit でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを終了すると、Onit に割り当てた Azure AD ユーザーは、Onit 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Onit のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-onit-tutorial/IC791166.png "シナリオ")
##Onit のアプリケーション統合の有効化
  
このセクションでは、Onit のアプリケーション統合を有効にする方法について説明します。

###Onit のアプリケーション統合を有効にするには、次の手順に従います。

1.  Microsoft Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-onit-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-onit-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-onit-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Onit**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-onit-tutorial/IC791167.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Onit]** を選び、**[完了]** をクリックしてアプリケーションを追加します。

    ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Onit に対する認証を行えるようにする方法を説明します。Onit にシングル サインオンを構成するには、証明書の拇印の値を取得する必要があります。この手順に慣れていない場合は、[証明書の拇印の値を取得する方法](http://youtu.be/YKQF266SAxI)についてのビデオをご覧ください。
  
Onit アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを　**SAML トークンの属性**の構成に追加する必要があります。次のスクリーンショットはその例です。

![シングル サインオン](./media/active-directory-saas-onit-tutorial/IC791168.png "シングル サインオン")

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Onit** アプリケーション統合ページの一番上のメニューで **[属性]** をクリックして、**[SAML トークンの属性]** ダイアログを開きます。

    ![属性](./media/active-directory-saas-onit-tutorial/IC791169.png "属性")

2.  必要な属性のマッピングを追加するには、次の手順を実行します。

    
    |属性名|属性値|
	|---|---|
	|name|User.userprincipalname|
    |電子メール|User.mail|

    1.  上記の表の各データ行で、**[ユーザー属性の追加]** をクリックします。
    2.  **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    3.  **[属性値]** リストから、その行に対して表示される属性値を選択します。
    4.  **[完了]** をクリックします。

3.  **[変更の適用]** をクリックします。

4.  ブラウザーで、**[戻る]** をクリックして、**[クイック スタート]** ダイアログをもう一度開きます。

5.  [シングル サインオンの構成] ダイアログ ボックスを開くには、 **[シングル サインオンの構成]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791170.png "シングル サインオンの構成")

6.  **[ユーザーの Onit へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選び、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-onit-tutorial/IC791171.png "Configure Single Sign-On")

7.  **[アプリ URL の構成]** ページの **[Onit サインオン URL]** テキストボックスに、ユーザーが Onit アプリケーションのサインオンに使用する URL (例: "**https://ms-sso-test.onit.com*”)) を入力して、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-onit-tutorial/IC791172.png "アプリケーション URL の構成")

8.  **[Onit でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791173.png "Configure Single Sign-On")

9.  別の Web ブラウザー ウィンドウで、Onit 企業サイトに管理者としてログインします。

10. 上部のメニューで **[管理]** をクリックします。

    ![管理](./media/active-directory-saas-onit-tutorial/IC791174.png "管理")

11. **[コーポレーションの編集]** をクリックします。

    ![コーポレーションの編集](./media/active-directory-saas-onit-tutorial/IC791175.png "コーポレーションの編集")

12. **[セキュリティ]** タブをクリックします。

    ![会社情報の編集](./media/active-directory-saas-onit-tutorial/IC791176.png "会社情報の編集")

13. **[セキュリティ]** タブで、次の手順に従います。

    ![シングル サインオン](./media/active-directory-saas-onit-tutorial/IC791177.png "シングル サインオン")

    1.  **[認証方式]** として **[シングル サインオンとパスワード]** を選びます。
    2.  Azure ポータルの **[Onit でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** 値をコピーし、**[IDP ターゲット URL]** ボックスに貼り付けます。
    3.  Azure ポータルの **[Onit でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** 値をコピーし、**[IDP ログアウト URL]** ボックスに貼り付けます。
    4.  エクスポートした証明書から **[拇印]** の値をコピーして、**[IDP 証明書の指紋 (SHA1)]** ボックスに貼り付けます。  

        >[AZURE.TIP]詳細については、[証明書のサムプリント値を取得する方法](http://youtu.be/YKQF266SAxI)についてのビデオをご覧ください。

    5.  **[SSO 型]** として **[SAML]** を選びます。
    6.  **[SSO ログイン ボタンのテキスト]** テキストボックスに、任意のボタンのテキストを入力します。
    7.  **[SSO を使用してログイン: 次のドメインとユーザーに必要]** を選び、該当するテキストボックスにテスト ユーザーの電子メール アドレスを入力し、**[更新]** をクリックします。![コーポレーションの編集](./media/active-directory-saas-onit-tutorial/IC791178.png "コーポレーションの編集")

14. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791179.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Onit にログインできるようにするには、ユーザーを Onit にプロビジョニングする必要があります。Onit の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Onit** 企業サイトに管理者としてログインします。

2.  **[ユーザーの追加]** をクリックします。

    ![管理](./media/active-directory-saas-onit-tutorial/IC791180.png "管理")

3.  **[ユーザーの追加]** ダイアログ ページで、次の手順に従います。

    ![ユーザーの追加](./media/active-directory-saas-onit-tutorial/IC791181.png "ユーザーの追加")

    1.  プロビジョニングする有効な AAD アカウントの関連するテキストボックスに、**[名前]** と **[電子メール アドレス]** を入力します。
    2.  **[作成]** をクリックします。  

        >[AZURE.NOTE]アカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE]Onit から提供されている他の Onit ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Onit に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  Onit アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-onit-tutorial/IC791182.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

    ![あり](./media/active-directory-saas-onit-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->