<properties pageTitle="チュートリアル: Azure Active Directory と Boomi の統合 | Microsoft Azure" description="Azure Active Directory で Boomi を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Boomi の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=529589)をクリックしてください。

このチュートリアルでは、Azure と Boomi の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Boomi でのシングル サインオンが有効なサブスクリプション

このチュートリアルを終了すると、Boomi に割り当てた Azure AD ユーザーは、Boomi 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Boomi のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-boomi-tutorial/IC791134.png "シナリオ")
##Boomi のアプリケーション統合の有効化

このセクションでは、Boomi のアプリケーション統合を有効にする方法について説明します。

###Boomi のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-boomi-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-boomi-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-boomi-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Boomi**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-boomi-tutorial/IC790822.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Boomi]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Boomi に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **Boomi** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configure Single Sign-On")

2.  **[ユーザーの Boomi へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configure Single Sign-On")

3.  **[アプリの URL の構成]** ページで、**[Boomi 応答 URL]** ボックスに **Boomi AtomSphere ログイン URL** (例: "*https://platform.boomi.com/sso/AccountName/saml*”) を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-boomi-tutorial/IC790826.png "アプリケーション URL の構成")

4.  **[Boomi でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、コンピューターで証明書ファイルをローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Boomi 企業サイトに管理者としてログインします。

6.  上部のツール バーで会社名をクリックし、**[Setup]** をクリックします。

    ![セットアップ](./media/active-directory-saas-boomi-tutorial/IC790828.png "セットアップ")

7.  **[SSO Options]** をクリックします。

    ![SSO Options](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")

8.  **[Single Sign-On Options]** セクションで、次の手順を実行します。

    ![Single Sign-On Options](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")

    1.  **[Enable SAML Single Sign-On]** を選択します。
    2.  **[Import]** をクリックして、ダウンロードした証明書をアップロードします。
    3.  Azure ポータルで、**[Boomi でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[Identity Provider Login URL]** ボックスに貼り付けます。
    4.  **[Federation Id Location]** で、**[Federation Id is in NameID element of the Subject]** を選択します。
    5.  **[保存]** をクリックします。

9.  Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Boomi にログインできるようにするには、ユーザーを Boomi にプロビジョニングする必要があります。Boomi の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Boomi** 企業サイトに管理者としてログインします。

2.  **[User Management]、[Users]** の順にクリックします。

    ![ユーザー](./media/active-directory-saas-boomi-tutorial/IC790831.png "ユーザー")

3.  **[Add User]** をクリックします。

    ![ユーザーの追加](./media/active-directory-saas-boomi-tutorial/IC790832.png "ユーザーの追加")

4.  **[Add User Roles]** ダイアログ ページで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-boomi-tutorial/IC790833.png "ユーザーの追加")

    1.  対応するボックスに、プロビジョニングする有効な AAD アカウントの**名**、**姓**、**電子メール**を入力します。
    2.  [OK] をクリックします。

>[AZURE.NOTE]Boomi から提供されている他の Boomi ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、ユーザーにアプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Boomi に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Boomi** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-boomi-tutorial/IC790834.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確認します。

    ![あり](./media/active-directory-saas-boomi-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->