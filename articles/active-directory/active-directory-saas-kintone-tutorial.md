<properties pageTitle="チュートリアル: Azure Active Directory と Kintone の統合 | Microsoft Azure" description="Azure Active Directory で Kintone を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Kintone の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=526796)をクリックしてください。
  
このチュートリアルの目的は、Azure と Kintone の統合を紹介することです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Kintone でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを終了すると、Kintone に割り当てた Azure AD ユーザーは、Kintone の企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Kintone のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-kintone-tutorial/IC785859.png "シナリオ")
##Kintone のアプリケーション統合の有効化
  
このセクションでは、Kintone のアプリケーション統合を有効にする方法について説明します。

###Kintone のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-kintone-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-kintone-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Kintone**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-kintone-tutorial/IC785867.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Kintone]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Kintone に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **[Kintone]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2.  **Kintone にサインオンする方法について尋ねる** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3.  **[アプリ URL の構成]** ページの **[Kintone サインオン URL]** テキストボックスに、"**https://company.kintone.com*" というパターンの URL を入力して、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-kintone-tutorial/IC785875.png "アプリケーション URL の構成")

4.  **[Kintone でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、**Kintone** の企業サイトに管理者としてログインします。

6.  **[設定]** をクリックします

    ![設定](./media/active-directory-saas-kintone-tutorial/IC785879.png "設定")

7.  **[ユーザーとシステム管理]** をクリックします。

    ![ユーザーとシステム管理](./media/active-directory-saas-kintone-tutorial/IC785880.png "ユーザーとシステム管理")

8.  **[システム管理] > [セキュリティ]** で、**[ログイン]** をクリックします。

    ![ログイン](./media/active-directory-saas-kintone-tutorial/IC785881.png "ログイン")

9.  **[SAML 認証を有効にする]** を選択します。

    ![SAML 認証](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML 認証")

10. [SAML 承認] セクションで、次の手順に従います。

    ![SAML 認証](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML 認証")

    1.  Azure ポータルで、**[Kintone でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[ログイン URL]** ボックスに貼り付けます。
    2.  Azure ポータルで、**[Kintone でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。
    3.  **[参照]** をクリックして、ダウンロードした証明書をアップロードします。
    4.  **[保存]** をクリックします。

11. Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Kintone にログインできるようにするには、そのユーザーを Kintone にプロビジョニングする必要があります。Kintone の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **Kintone** の企業サイトに管理者としてログインします。

2.  **[設定]** をクリックします。

    ![設定](./media/active-directory-saas-kintone-tutorial/IC785879.png "設定")

3.  **[ユーザーとシステム管理]** をクリックします。

    ![ユーザーとシステム管理](./media/active-directory-saas-kintone-tutorial/IC785880.png "ユーザーとシステム管理")

4.  **[ユーザー管理]** で、**[Departments & Users (部門とユーザー)]** をクリックします。

    ![部門とユーザー](./media/active-directory-saas-kintone-tutorial/IC785888.png "部門とユーザー")

5.  **[新しいユーザー]** をクリックします。

    ![新しいユーザー](./media/active-directory-saas-kintone-tutorial/IC785889.png "新しいユーザー")

6.  **[新しいユーザー]** セクションで、次の手順に従います。

    ![新しいユーザー](./media/active-directory-saas-kintone-tutorial/IC785890.png "新しいユーザー")

    1.  プロビジョニングする有効な AAD アカウントの **[表示名]**、**[ログイン名]**、**[新しいパスワード]**、**[確認パスワード]**、**[メール アドレス]**、その他の詳細を該当するボックスに入力します。
    2.  **[保存]** をクリックします。

>[AZURE.NOTE]他の Kintone ユーザー アカウント作成ツールや Kintone から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Kintone に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Kintone** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-kintone-tutorial/IC785891.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-kintone-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->