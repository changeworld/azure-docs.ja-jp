<properties 
    pageTitle="チュートリアル: Azure Active Directory と e-Builder の統合 | Microsoft Azure" 
    description="Azure Active Directory で e-Builder を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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

#チュートリアル: Azure Active Directory と e-Builder の統合
  
このチュートリアルでは、Azure と e-Builder の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   e-Builder テナント
  
このチュートリアルを完了すると、e-Builder に割り当てた Azure AD ユーザーは、e-Builder 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  e-Builder のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-e-builder-tutorial/IC777378.png "シナリオ")
##e-Builder のアプリケーション統合の有効化
  
このセクションでは、e-Builder のアプリケーション統合を有効にする方法について説明します。

###e-Builder のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-e-builder-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-e-builder-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-e-builder-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**e-Builder**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-e-builder-tutorial/IC777379.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[e-Builder]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![e-Builder](./media/active-directory-saas-e-builder-tutorial/IC777380.png "e-Builder")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで e-Builder に対する認証を行うことができるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **e-Builder** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configure single sign-on")

2.  **[ユーザーの e-Builder へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure single sign-on](./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configure single sign-on")

3.  **[アプリケーション URL の構成]** ページの **[e-Builder サインイン URL]** ボックスに、"https://\<テナント名>.e-Builder.com" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![Configure app URL](./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configure app URL")

4.  **[e-Builder でのシングル サインオンの構成]** ページで、メタデータをダウンロードするには、**[メタデータのダウンロード]** をクリックし、データ ファイルを **c:\\E-BuilderMetaData.xml** としてローカルに保存します。

    ![Configure single sign-on](./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configure single sign-on")

5.  メタデータ ファイルを e-Builder サポート チームに転送します。サポート チームは、シングル サインオンを構成する必要があります。

6.  [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure single sign-on](./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configure single sign-on")
##ユーザー プロビジョニングの構成
  
e-Builder へのユーザー プロビジョニングの構成にあたって必要な操作はありません。割り当てられたユーザーがアクセス パネルを使用して e-Builder にログインしようとすると、そのユーザーが存在するかどうかが e-Builder によって確認されます。使用可能なユーザー アカウントがない場合、ユーザー アカウントは自動的に作成されます。
##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを e-Builder に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **e-Builder** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-e-builder-tutorial/IC777386.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-e-builder-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0121_2016-->