<properties pageTitle="チュートリアル: Azure Active Directory と Adobe EchoSign の統合 | Microsoft Azure" description="Azure Active Directory で Adobe EchoSign を使用して、シングル サインオンや自動化されたプロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Adobe EchoSign の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=528195)をクリックしてください。

このチュートリアルでは、Azure と Adobe EchoSign の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Adobe EchoSign でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Adobe EchoSign に割り当てた Azure AD ユーザーは、Adobe EchoSign 企業サイト (サービス プロバイダーが開始したサインオン) で、または[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Adobe EchoSign のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "シナリオ")
##Adobe EchoSign のアプリケーション統合の有効化

このセクションでは、Adobe EchoSign のアプリケーション統合を有効にする方法を説明します。

###Adobe EchoSign のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **[検索]** ボックスに、「**Adobe EchoSign**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Adobe EchoSign]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Adobe EchoSign に対する認証を行えるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Adobe EchoSign** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configure Single Sign-On")

2.  **[ユーザーの Adobe EchoSign へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configure Single Sign-On")

3.  **[アプリの URL の構成]** ページで、**[Adobe EchoSig サインオン URL]** テキスト ボックスに、"*https://company.echosign.com/*" のパターンで URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "アプリケーション URL の構成")

4.  **[Adobe EchoSign でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、管理者として Adobe EchoSign 企業サイトにログインします。

6.  上部のメニューで、**[アカウント]** をクリックしてから、左側のナビゲーション ウィンドウの **[アカウント設定]** 内にある **[SAML 設定]** をクリックします。

    ![アカウント](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "アカウント")

7.  [SAML 設定] セクションで、次の手順に従います。

    ![SAML 設定](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML 設定")

    1.  [**SAML モード**] として [**SAML Mandatory**] を選択します。
    2.  [**EchoSign 資格情報を使用して、EchoSign アカウント管理者のログインを許可する**] を選択します。
    3.  [**ユーザーの作成**] として、[**SAML を使用して認証されたユーザーを自動的に追加**] を選択します。

8.  次に、以下の手順に進みます。

    ![SAML 設定](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML 設定")

    1.  Azure ポータルの [**Adobe EchoSign でのシングル サインオンの構成**] ダイアログ ページで、 [**エンティティ ID**] の値をコピーし、[**IdP エンティティ ID**] テキスト ボックスに貼り付けます。
    2.  Azure ポータルの [**Adobe EchoSign でのシングル サインオンの構成**] ダイアログ ページで、[**リモート ログイン URL**] 値をコピーし、[**IdP ログイン URL**] テキスト ボックスに貼り付けます。
    3.  Azure ポータルの [**Adobe EchoSign でのシングル サインオンの構成**] ダイアログ ページで、[**リモート ログイン URL**] 値をコピーし、[**IdP ログアウト URL**] テキスト ボックスに貼り付けます。
    4.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

		>[AZURE.TIP]詳細については、[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o) をご覧ください。
    5.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、[**IdP 証明書**] テキストボックスに貼り付けます。
    6.  **[変更を保存]** をクリックします。

9.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、[**完了**] をクリックして [**シングル サインオンの構成**] ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Adobe EchoSign にログインできるようにするには、そのユーザーを Adobe EchoSign にプロビジョニングする必要があります。Adobe EchoSign の場合、プロビジョニングは手動で実行します。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **Adobe EchoSign** 企業サイトに管理者としてログインします。

2.  上部のメニューで、[**アカウント**] をクリックしてから、左側のナビゲーション ウィンドウの [**ユーザーとグループ**] 内にある [**新しいユーザーの作成**] をクリックします

    ![アカウント](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "アカウント")

3.  [**新しいユーザーの作成**] セクションで、次の手順に従います。

    ![ユーザーの作成](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Create User")

    1.  対応するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの [**電子メール アドレス**]、[**名**]、[**姓**] を入力します。
    2.  [**ユーザーの作成**] をクリックします。

		>[AZURE.NOTE]Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE]他の Adobe EchoSign ユーザー アカウント作成ツールまたは Adobe EchoSign から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###Adobe EchoSign にユーザーを割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  Adobe EchoSign アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を参照してください。

<!---HONumber=Oct15_HO3-->