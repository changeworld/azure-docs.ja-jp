<properties 
    pageTitle="チュートリアル: Azure Active Directory と AnswerHub の統合 | Microsoft Azure" 
    description="Azure Active Directory で AnswerHub を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と AnswerHub の統合

このチュートリアルでは、Azure と AnswerHub の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   有効なサブスクリプションでの AnswerHub のシングル サインオン

このチュートリアルを完了すると、AnswerHub に割り当てた Azure AD ユーザーは、AnswerHub 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  AnswerHub のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-answerhub-tutorial/IC785165.png "シナリオ")
##AnswerHub のアプリケーション統合の有効化

このセクションでは、AnswerHub のアプリケーション統合を有効にする方法を説明します。

###AnswerHub のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-answerhub-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-answerhub-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-answerhub-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  [**検索**] ボックスに、「**AnswerHub**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-answerhub-tutorial/IC785166.png "アプリケーション ギャラリー")

7.  結果ウィンドウで [**AnswerHub**] を選択し、[**完了**] をクリックしてアプリケーションを追加します。

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで AnswerHub に対する認証を行えるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **AnswerHub** アプリケーション統合ページで [**シングル サインオンの構成**] をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configure single sign-on")

2.  [**ユーザーの AnswerHub へのアクセスを設定してください**] ページで、[**Microsoft Azure AD のシングル サインオン**] を選択し、[**次へ**] をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configure single sign-on")

3.  [**アプリケーション URL の構成**] ページの [**AnswerHub サインイン URL**] テキスト ボックスに、*https://company.answerhub.com*" というパターンの URL を入力し、[**次へ**] をクリックします。

    ![Configure App URL](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configure App URL")

4.  [**AnswerHub でのシングル サインオン構成**] ページで、証明書をダウンロードするには、[**証明書のダウンロード**] をクリックし、コンピューターにローカルで証明書ファイルを保存します。

    ![Configure single sign-on](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configure single sign-on")

5.  別の Web ブラウザーのウィンドウで、管理者として AnswerHub 企業サイトにログインします。

6.  [**Administration**] に移動します。

7.  [**User and Group**] タブをクリックします。

8.  左側にあるナビゲーション ウィンドウの [**Social Settings**] セクションで、[**SAML Setup**] をクリックします。

9.  [**IDP Config**] タブをクリックします。

10. [**IDP Config**] タブで、次の手順を実行します。

    ![SAML Setup](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")

    1.  Azure ポータルの [**AnswerHub でのシングル サインオンの構成**] ダイアログ ページで、[**リモート ログイン URL**] 値をコピーして、[**IDP Login URL**] テキスト ボックスに貼り付けます。
    2.  Azure ポータルの [**AnswerHub でのシングル サインオンの構成**] ダイアログ ページで、[**リモート ログアウト URL**] 値をコピーし、[**IDP Logout URL**] テキスト ボックスに貼り付けます。
    3.  Azure ポータルの [**Gigya でのシングル サインオンの構成**] ダイアログ ページで、[**名前識別子形式**] の値をコピーし、[**IDP Name Identifier Format**] テキスト ボックスに貼り付けます。
    4.  [**Keys and Certificates**] をクリックします。

11. [Keys and Certificates] タブでは、次の手順を実行します。

    ![Keys and Certificates](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")

    1.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

		>[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    2.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、[**IDP Public Key (x509 Format)**] テキストボックスに貼り付けます。
    3.  [**Save**] をクリックします。

12. [**IDP Config**] タブで、[**Save**] をクリックします。

13. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、[**完了**] をクリックして [**シングル サインオンの構成**] ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configure single sign-on")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが AnswerHub にログインできるようにするには、そのユーザーを AnswerHub にプロビジョニングする必要があります。AnswerHub の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **AnswerHub** 企業サイトに管理者としてログインします。

2.  [**Administration**] に移動します。

3.  [**Users & Groups**] タブをクリックします。

4.  左側にあるナビゲーション ウィンドウの [**Manage Users**] で、[**Create or import users**] をクリックします。

    ![Users & Groups](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")

5.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザー アカウントの [**Email address**]、 [**Username**]、および [**Password**] を入力してから [**Save**] をクリックします。

>[AZURE.NOTE]他の AnswerHub ユーザー アカウントの作成ツールまたは AnswerHub から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを AnswerHub に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  AnswerHub アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-answerhub-tutorial/IC785176.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0121_2016-->