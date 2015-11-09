<properties 
    pageTitle="チュートリアル: Azure Active Directory と Thoughtworks Mingle の統合 | Microsoft Azure" 
    description="Azure Active Directory で Thoughtworks Mingle を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と Thoughtworks Mingle の統合
  
このチュートリアルでは、Azure と Thoughtworks Mingle の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Thoughtworks Mingle テナント
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Thoughtworks Mingle のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "シナリオ")

##Thoughtworks Mingle のアプリケーション統合の有効化
  
このセクションでは、Thoughtworks Mingle のアプリケーション統合を有効にする方法を説明します。

###Thoughtworks Mingle のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**thoughtworks mingle**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Thoughtworks Mingle]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Thoughtworks Mingle に対する認証を行うことができるようにする方法を説明します。この手順の途中で、証明書を Thoughtworks Mingle にアップロードする必要があります。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **Thoughtworks Mingle** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "シングル サインオンの構成")

2.  **[ユーザーの Thoughtworks Mingle へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[Thoughtworks Mingle テナント URL]** テキストボックスに、"**http://company.mingle.thoughtworks.com*"" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "アプリケーション URL の構成")

4.  **[Thoughtworks Mingle でのシングル サインオンの構成]** ページで、[メタデータのダウンロード] をクリックしてメタデータをダウンロードし、コンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "シングル サインオンの構成")

5.  **Thoughtworks Mingle** 企業サイトに管理者としてログインします。

6.  **[Admin]** タブをクリックし、**[SSO Config]** をクリックします。

    ![SSO 構成](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO 構成")

7.  **[SSO Config]** セクションで、次の手順に従います。

    ![SSO 構成](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO 構成")

    1.  メタデータ ファイルをアップロードするには、**[Choose file]** をクリックします。
    2.  **[変更を保存]** をクリックします。

8.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "シングル サインオンの構成")

##ユーザー プロビジョニングの構成
  
AAD ユーザーがサインインできるように、Azure Active Directory ユーザー名を使用して、Thoughtworks Mingle アプリケーションにユーザーをプロビジョニングする必要があります。Thoughtworks Mingle の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  Thoughtworks Mingle 企業サイトに管理者としてログインします。

2.  **[Profile]** をクリックします。

    ![最初のプロジェクト](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "最初のプロジェクト")

3.  **[Admin]** タブをクリックし、**[Users]** をクリックします。

    ![ユーザー](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "ユーザー")

4.  **[New User]** をクリックします。

    ![新しいユーザー](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "新しいユーザー")

5.  **[New User]** ダイアログ ページで、次の手順に従います。

    ![新しいユーザー](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "新しいユーザー")

    1.  プロビジョニングする有効な AAD アカウントの **[Sign-in name]**、**[Display name]**、**[Choose password]**、**[Confirm password]** の詳細を該当するボックスに入力します。
    2.  **[User type]** として、**[Full user]** を選択します。
    3.  **[Create This Profile]** をクリックします。

>[AZURE.NOTE]Thoughtworks Mingle から提供されている他の Thoughtworks Mingle ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Thoughtworks Mingle に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Thoughtworks Mingle** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->