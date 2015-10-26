<properties pageTitle="チュートリアル: Azure Active Directory と ClickTime の統合 | Microsoft Azure" description="Azure Active Directory で ClickTime を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と ClickTime の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=%20524782)をクリックしてください。

このチュートリアルでは、Azure と ClickTime の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ClickTime テナント

このチュートリアルを完了すると、ClickTime に割り当てた Azure AD ユーザーは、ClickTime 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  ClickTime のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-clicktime-tutorial/IC777274.png "シナリオ")
##ClickTime のアプリケーション統合の有効化

このセクションでは、ClickTime のアプリケーション統合を有効にする方法を説明します。

###ClickTime のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-clicktime-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-clicktime-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-clicktime-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**ClickTime**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-clicktime-tutorial/IC777275.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[ClickTime]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで ClickTime に対する認証を行うことができるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書を ClickTime テナントにアップロードする必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」を参照してください。

>[AZURE.IMPORTANT]ClickTime テナントでシングル サインオンを構成できるようにするには、まず ClickTime テクニカル サポートに連絡してこの機能を有効にする必要があります。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **ClickTime** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの有効化](./media/active-directory-saas-clicktime-tutorial/IC777277.png "シングル サインオンの有効化")

2.  **[ユーザーの ClickTime へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-clicktime-tutorial/IC777278.png "シングル サインオンの構成")

3.  **[ClickTime でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-clicktime-tutorial/IC777279.png "シングル サインオンの構成")

4.  別の Web ブラウザー ウィンドウで、ClickTime 企業サイトに管理者としてログインします。

5.  上部にあるツールバーで **[Preferences]**、**[Security Settings]** の順にクリックします。

6.  **[Single Sign-On Preferences]** 構成セクションで、次の手順を実行します。

    ![セキュリティ設定](./media/active-directory-saas-clicktime-tutorial/IC777280.png "セキュリティ設定")

    1.  **[OneLogin]** を指定し、シングル サインオン (SSO) を使用したサインインの **[許可]** を選択します。
    2.  Azure ポータルの **[ClickTime でのシングル サインオンの構成]** ダイアログ ページで、**シングル サインオン サービス URL** の値をコピーし、**[ID プロバイダー エンドポイント]** テキストボックスに貼り付けます。
    3.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」を参照してください。

    4.  base-64 でエンコードされた証明書を**メモ帳**で開き、その内容をコピーして、**[X.509 証明書]** テキストボックスに貼り付けます。
    5.  **[保存]** をクリックします。

7.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-clicktime-tutorial/IC777281.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが ClickTime にログインできるようにするには、そのユーザーを ClickTime にプロビジョニングする必要があります。ClickTime の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **ClickTime** テナントにログインします。

2.  上部のツールバーで、**[Company]** をクリックし、**[People]** をクリックします。

    ![People](./media/active-directory-saas-clicktime-tutorial/IC777282.png "People")

3.  **[Add Person]** をクリックします。

    ![Add Person](./media/active-directory-saas-clicktime-tutorial/IC777283.png "Add Person")

4.  [New Person] セクションで、次の手順を実行します。

    ![People](./media/active-directory-saas-clicktime-tutorial/IC777284.png "People")

    1.  **[email address]** テキストボックスに Azure AD アカウントの電子メール アドレスを入力します。
    2.  **[full name]** テキストボックスに、Azure AD アカウントの名前を入力します。  

        >[AZURE.NOTE]必要に応じて、新しいユーザー オブジェクトの追加プロパティを設定できます。

    3.  **[保存]** をクリックします。

>[AZURE.NOTE]ClickTime から提供されている他の ClickTime ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを ClickTime に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **ClickTime** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-clicktime-tutorial/IC777285.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-clicktime-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を参照してください。

<!---HONumber=Oct15_HO3-->