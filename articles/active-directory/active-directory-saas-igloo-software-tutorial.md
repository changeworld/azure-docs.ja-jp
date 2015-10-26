<properties pageTitle="チュートリアル: Azure Active Directory と Igloo Software の統合 | Microsoft Azure" description="Azure Active Directory で Igloo Software を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Igloo Software の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=526509)をクリックしてください。
  
このチュートリアルでは、Azure と Igloo Software の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   シングル サインオン対応の [Igloo Software](http://www.igloosoftware.com/) サブスクリプション
  
このチュートリアルを完了すると、Igloo Software に割り当てた Azure AD ユーザーは、Igloo Software 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Igloo Software のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "シナリオ")
##Igloo Software のアプリケーション統合の有効化
  
このセクションでは、Igloo Software のアプリケーション統合を有効にする方法を説明します。

###Igloo Software のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に「**Igloo Software**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Igloo Software]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Igloo Software に対する認証を行うことができるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書を Central Desktop テナントにアップロードする必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **[Igloo Software]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[**シングル サインオンの構成**] ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configure Single Sign-On")

2.  **[ユーザーの Igloo Software へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Microsoft Azure AD シングル サインオン](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD シングル サインオン")

3.  **[アプリケーション URL の構成]** ページの **[Igloo Software サインイン URL]** ボックスに、"**https://company.igloocommunities.com/?signin*"" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリ URL の構成](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "アプリケーション URL の構成")

4.  **[Igloo Software でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、管理者として Igloo Software 企業サイトにログインします。

6.  **コントロール パネル**に移動します。

    ![コントロール パネル](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "コントロール パネル")

7.  **[メンバーシップ]** タブで **[サインインの設定]** をクリックします。

    ![サインインの設定](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "サインインの設定")

8.  SAML の構成のセクションで、**[SAML 認証の構成]** をクリックします。

    ![SAML の構成](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML の構成")

9.  **[全般構成]** セクションで、次の手順を実行します。

    ![全般構成](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "全般構成")

    1.  **[接続名]** テキストボックスに、構成のカスタム名を入力します。
    2.  Azure ポータルで、**[Igloo Software でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[IdP ログイン URL]** テキストボックスに貼り付けます。
    3.  Azure ポータルで、**[Igloo Software でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[IdP ログアウト URL]** テキストボックスに貼り付けます。
    4.  **[ログアウト応答と要求 HTTP のタイプ]** として、**[POST]** を選択します。
    5.  ダウンロードした証明書からテキスト ファイルを作成します。
        
		>[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    6.  最初の行 (**) と最後の行 (**) を証明書のテキスト ファイル バージョンから削除し、残りの証明書テキストをコピーして、それを **[公開証明書]** テキストボックスに貼り付けます。

10. **[応答および認証の構成]** で、次の手順を実行します。

    ![応答および認証の構成](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "応答および認証の構成")

    1.  **[ID プロバイダー]** として **[Microsoft ADFS]** を選択します。
    2.  **[識別子の種類]** として **[電子メール アドレス]** を選択します。
    3.  **[電子メール属性]** テキストボックスに、「**emailaddress**」と入力します。
    4.  **[名属性]** テキストボックスに、「**givenname**」と入力します。
    5.  **[姓属性]** テキストボックスに、「**surname**」と入力します。

11. 次の手順を実行して、構成を完成させます。

    ![サインインでユーザー作成](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "サインインでユーザー作成")

    1.  **[サインインでユーザー作成]** で **[サインイン時に新しいユーザーをサイトに作成]** を選択します。
    2.  **[サインインの設定]** で、**[[サインイン] 画面で SAML ボタンを使用する]** を選択します。
    3.  **[保存]** をクリックします。

12. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Igloo Software へのユーザー プロビジョニングを構成するときに必要な操作はありません。割り当てられたユーザーがアクセス パネルを使用して Igloo Software にログインしようとすると、そのユーザーが存在するかどうかが Igloo Software によって確認されます。使用可能なユーザー アカウントがない場合、ユーザー アカウントは Igloo Software によって自動的に作成されます。
##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Igloo Software に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Igloo Software ** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->