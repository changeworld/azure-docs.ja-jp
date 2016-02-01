<properties 
    pageTitle="チュートリアル: Azure Active Directory と Lucidchart の統合 | Microsoft Azure" 
    description="Azure Active Directory で Lucidchart を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Lucidchart の統合
  
このチュートリアルでは、Azure と Lucidchart の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Lucidchart でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、Lucidchart に割り当てた Azure AD ユーザーは、Lucidchart 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Lucidchart のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "シナリオ")
##Lucidchart のアプリケーション統合の有効化
  
このセクションでは、Lucidchart のアプリケーション統合を有効にする方法を説明します。

###Lucidchart のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Lucidchart**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Lucidchart]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Lucidchart に対する認証を行うことができるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **[Lucidchart]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configure Single Sign-On")

2.  **[ユーザーの Lucidchart へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configure Single Sign-On")

3.  **[アプリ URL の構成]** ページの **[Lucidchart サインオン URL]** テキストボックスに、ユーザーが Lucidchart アプリケーションにサインオンするときに使用する URL (例: **https://chart2.office.lucidchart.com/saml/sso/azure*")) を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configure App URL")

4.  **[Lucidchart でのシングル サインオンの構成]** ページで、**[データのダウンロード]** をクリックしてメタデータをダウンロードし、データ ファイルをコンピューターのローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Lucidchart の企業サイトに管理者としてログインします。

6.  上部のメニューで **[チーム]** をクリックします。

    ![チーム](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "チーム")

7.  **[アプリケーション] > [SAML の管理]** をクリックします。

    ![SAML の管理](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "SAML の管理")

8.  **[SAML Authentication Settings]** ダイアログ ページで、次の手順に従います。

    1.  **[SAML 認証を有効にする]** を選んで、**[オプション]** をクリックします。![SAML 認証の設定](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML 認証の設定")
    2.  **[ドメイン]** テキストボックスで、使用するドメインを入力して、**[証明書の変更]** をクリックします。![証明書の変更](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "証明書の変更")
    3.  ダウンロードしたメタデータ ファイルを開いて内容をコピーし、**[Upload Metadata (メタデータのアップロード)]** テキストボックスに貼り付けます。![メタデータのアップロード](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "メタデータのアップロード")
    4.  **[Automatically Add new user to the team (新しいユーザーを自動的にチームに追加する)]** を選んで、**[変更を保存]** をクリックします。![変更を保存](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "変更を保存")

9.  [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Lucidchart へのユーザー プロビジョニングの構成にあたって必要な操作はありません。割り当て済みユーザーがアクセス パネルを使用して Lucidchart にログインしようとすると、そのユーザーが存在するかどうかが Lucidchart によって確認されます。使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lucidchart により自動的に作成されます。
##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Lucidchart に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Lucidchart** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0121_2016-->