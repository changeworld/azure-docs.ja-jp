<properties 
    pageTitle="チュートリアル: Azure Active Directory と Samanage の統合 | Microsoft Azure" 
    description="Azure Active Directory で Samanage を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/07/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Samanage の統合
  
このチュートリアルでは、Azure と Samanage の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Samanage テナント
  
このチュートリアルを完了すると、Samanage に割り当てた Azure AD ユーザーは、Samanage 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Samanage のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-samanage-tutorial/IC771705.png "シナリオ")
##Samanage のアプリケーション統合の有効化
  
このセクションでは、Samanage のアプリケーション統合を有効にする方法について説明します。

###Samanage のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-samanage-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-samanage-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-samanage-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Samanage**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-samanage-tutorial/IC771707.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Samanage]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Samanage](./media/active-directory-saas-samanage-tutorial/IC771708.png "Samanage")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Samanage に対する認証を行えるようにする方法について説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Samanage** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-samanage-tutorial/IC771709.png "シングル サインオンの構成")

2.  **[ユーザーの Samanage へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Microsoft Azure AD シングル サインオン](./media/active-directory-saas-samanage-tutorial/IC771710.png "Microsoft Azure AD シングル サインオン")

3.  **[アプリ URL の構成]** ページで、**[Samanage サインイン URL]** テキストボックスに、"*https://\<tenant-name>.Samanage.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-samanage-tutorial/IC771711.png "アプリケーション URL の構成")

4.  **[Samanage でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-samanage-tutorial/IC777613.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、Samanage 企業サイトに管理者としてログインします。

6.  **[Dashboard]** をクリックして、左のナビゲーション ウィンドウで **[Setup]** を選択します。

    ![ダッシュボード](./media/active-directory-saas-samanage-tutorial/IC771712.png "ダッシュボード")

7.  **[シングル サインオン]** をクリックします。

    ![シングル サインオン](./media/active-directory-saas-samanage-tutorial/IC771713.png "シングル サインオン")

8.  **[Login using SAML]** ダイアログ ページで、次の手順に従ってから、**[Save Changes]** をクリックします。

    1.  **[Enable Single Sign-On with SAML]** をクリックします。![SAML を使用してログイン](./media/active-directory-saas-samanage-tutorial/IC771719.png "SAML を使用してログイン")
    2.  Azure クラシック ポータルの **[Samanage でのシングル サインオンの構成]** ダイアログ ページで、**[ID プロバイダーの識別]** の値をコピーして、**[Identity Provider URL]** テキストボックスに貼り付けます。![Configure Single Sign-On](./media/active-directory-saas-samanage-tutorial/IC771720.png "Configure Single Sign-On")
    3.  Azure クラシック ポータルで、**[Samanage でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[Login URL]** ボックスに貼り付けます。
    4.  Azure クラシック ポータルで、**[Samanage でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[Logout URL]** ボックスに貼り付けます。
    5.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。

        >[AZURE.TIP] 詳細については、[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o) をご覧ください。

    6.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X.509 証明書]** テキストボックスに貼り付けます。
    7.  **[Samanage に存在しない場合にユーザーを作成する]** をクリックします。![更新](./media/active-directory-saas-samanage-tutorial/IC771722.png "更新")
    8.  [**更新**] をクリックします。

9.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-samanage-tutorial/IC771723.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Samanage にログインできるようにするには、そのユーザーを Samanage にプロビジョニングする必要があります。Samanage の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **Samanage** テナントにログインします。

2.  **[ダッシュボード] > [セットアップ]** に移動します。

    ![セットアップ](./media/active-directory-saas-samanage-tutorial/IC771724.png "セットアップ")

3.  **[ユーザー]** タブをクリックします。

    ![Users](./media/active-directory-saas-samanage-tutorial/IC771725.png "Users")

4.  **[新しいユーザー]** をクリックします。

    ![新しいユーザー](./media/active-directory-saas-samanage-tutorial/IC771726.png "新しいユーザー")

5.  プロビジョニングする Azure AD アカウントの** [電子メール]** と **[名前]** を入力し、**[ユーザーの作成]** をクリックします。

    >[AZURE.NOTE]AAD アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

    ![ユーザーの作成](./media/active-directory-saas-samanage-tutorial/IC771727.png "ユーザーの作成")

>[AZURE.NOTE]他の Samanage ユーザー アカウントの作成ツールまたは Samanage から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Samanage に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Samanage** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-samanage-tutorial/IC771728.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-samanage-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0713_2016-->