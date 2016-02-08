<properties 
    pageTitle="チュートリアル: Azure Active Directory と ServiceNow の統合 | Microsoft Azure" 
    description="Azure Active Directory で ServiceNow を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" 
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
    ms.date="01/26/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と ServiceNow の統合
  
このチュートリアルでは、Azure と ServiceNow の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ServiceNow のテナント
  
このチュートリアルを完了すると、ServiceNow に割り当てた Azure AD ユーザーは、ServiceNow 企業サイト (サービス プロバイダーが開始したサインオン) または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  ServiceNow のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-servicenow-tutorial/IC769496.png "シナリオ")
##ServiceNow のアプリケーション統合の有効化
  
このセクションでは、ServiceNow のアプリケーション統合を有効にする方法について説明します。

###ServiceNow のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-servicenow-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-servicenow-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-servicenow-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**ServiceNow**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-servicenow-tutorial/IC701016.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[ServiceNow]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで ServiceNow に対する認証を行えるようにする方法を説明します。

この手順の途中で、base-64 でエンコードされた証明書を Dropbox for Business テナントにアップロードする必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD クラシック ポータルの **ServiceNow** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configure single sign-on")

2.  **[ユーザーの ServiceNow へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configure single sign-on")

3.  **[アプリケーション設定の構成]** ページで、次の手順を実行します。

    ![Configure app URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configure app URL")

    a. **[ServiceNow サインオン URL]** ボックスに、ServiceNow アプリケーションへのサインオンにユーザーが使用する URL を入力します (例: *https://\<InstanceName>.service-now.com*)。

    b.**[発行者の URL]** ボックスに、ServiceNow アプリケーションへのサインオンにユーザーが使用する URL を入力します (例: *https://\<InstanceName>.service-now.com*)。

    c.**[次へ]** をクリックします。

4.  **[シングル サインオンの自動構成]** ページで、**[シングル サインオン用にこのアプリケーションを手動で構成する]** をクリックし、**[次へ]** をクリックします。

    ![Configure app URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configure app URL")



4.  **[ServiceNow でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをローカルにコンピューターに保存して、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configure single sign-on")

1. ServiceNow アプリケーションに管理者としてサインオンします。

2. 左側のナビゲーション ウィンドウで、**[Properties]** をクリックします。

    ![Configure app URL](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configure app URL")


3. **[Multiple Provider SSO Properties]** ダイアログで、次の手順を実行します。

    ![Configure app URL](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configure app URL")

    a.**[Enable multiple provider SSO]** で **[Yes]** を選択します。

    b.**[Enable debug logging got the multiple provider SSO integration]** で **[Yes]** を選択します。

    c.**[The field on the user table that...]** ボックスに「**user\_name**」と入力します。

    d.**[保存]** をクリックします。



1. 左側のナビゲーション ウィンドウで、**[x509 Certificates]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configure single sign-on")


1. **[x509 Certificates]** ダイアログで、**[New]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configure single sign-on")


1. **[X.509 Certificates]** ダイアログで、次の手順を実行します。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configure single sign-on")

    a.**[新規]** をクリックします。

    b.**[Name]** ボックスに、構成の名前を入力します (例: **TestSAML2.0**)。

    c.**[アクティブ]** を選択します。

    d.**[形式]** で **[PEM]** を選択します。

    e.**[Type]** で **[Trust Store Cert]** を選択します。

    f.ダウンロードした証明書から Base-64 でエンコードされたファイルを作成します。
    > [AZURE.NOTE] 詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
    
    g.base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[PEM Certificate]** ボックスに貼り付けます。

    h.[**更新**] をクリックします。


1. 左側のナビゲーション ウィンドウで、**[Identity Providers]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configure single sign-on")

1. **[Identity Providers]** で、**[New]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configure single sign-on")


1. **[Identity Providers]** ダイアログで、**[SAML2 Update1?]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configure single sign-on")


1. [SAML2 Update1 Properties] ダイアログで、次の手順を実行します。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configure single sign-on")


    **[Name]** ボックスに、構成の名前を入力します (例: **SAML 2.0**)。

    b.**[ユーザー フィールド]** ボックスに「**email**」と入力します。

    c.Azure AD クラシック ポータルで、**[Identity Provider ID]** 値をコピーし、**[Identity Provider URL]** ボックスに貼り付けます。

    d.Azure AD クラシック ポータルの **[認証要求 URL]** 値をコピーし、**[Identity Provider's AuthnRequest]** ボックスに貼り付けます。

    e.Azure AD クラシック ポータルの **[シングル サインオン サービス URL]** 値をコピーし、**[Identity Provider's SingleLogoutRequest]** ボックスに貼り付けます。

    f.**[ServiceNow Homepage]** ボックスに ServiceNow インスタンス ホームページの URL を入力します。

    > [AZURE.NOTE] ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: **https://fabrikam.service-now.com/navpage.do*))。
 

    g.**[Entity ID / Issuer]** ボックスに、ServiceNow テナントの URL を入力します。

    h.**[Audience URL]** ボックスに ServiceNow テナントの URL を入力します。

    i.**[Protocol Binding for the IDP's SingleLogoutRequest]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**」と入力します。

    j.[NameID Policy] ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**」と入力します。

    k.**[Create an AuthnContextClass]** をオフにします。

    l.**[AuthnContextClassRef Method]** に「****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**」と入力します。

    m.**[Clock Skew]** ボックスに「**60**」と入力します。

    n.**[Single Sign On Script]** で **[MultiSSO\_SAML2\_Update1]** を選択します。

    o.**[x509 Certificate]** で、前の手順で作成した証明書を選択します。

    p. **[Submit]** をクリックします。



6. Azure AD クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configure single sign-on")

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
 
    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configure single sign-on")



##ユーザー プロビジョニングの構成
  
このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを ServiceNow に対して有効にする方法について説明します。


### ユーザー プロビジョニングを構成するには、次の手順に従います。

1. Microsoft Azure 管理クラシック ポータルの **ServiceNow** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックします。<br><br> ![ユーザー プロビジョニング](./media/active-directory-saas-servicenow-tutorial/IC769498.png "ユーザー プロビジョニング")


2. **[自動ユーザー プロビジョニングを有効にするための ServiceNow の資格情報を入力してください]** ページで、以下の構成設定を入力します： ユーザー プロビジョニングの構成

     2\.1.**[ServiceNow インスタンス名]** ボックスに、ServiceNow インスタンス名を入力します。

     2\.2.**[ServiceNow 管理ユーザー名]** ボックスに、ServiceNow 管理者アカウントの名前を入力します。

     2\.3.**[ServiceNow 管理パスワード]** ボックスに、このアカウントのパスワードを入力します。

     2\.4.**[検証]** をクリックして、構成を確認します。

     2\.5.**[次へ]** をクリックして、**[次のステップ]** ページを開きます。

     2\.6.このアプリケーションのすべてのユーザーをプロビジョニングする場合は、**[ディレクトリ内のすべてのユーザー アカウントをこのアプリケーションに自動的にプロビジョニングする]** を選択します。<br><br> ![次のステップ](./media/active-directory-saas-servicenow-tutorial/IC698804.png "次のステップ")

     2\.7.**[次のステップ]** ページで、**[完了]** をクリックして構成を保存します。











##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを ServiceNow に割り当てるには、次の手順を実行します。

1.  Azure AD クラシック ポータルで、テスト アカウントを作成します。

2.  **ServiceNow** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-servicenow-tutorial/IC769499.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0128_2016-->