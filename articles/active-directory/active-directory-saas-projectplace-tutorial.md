<properties pageTitle="チュートリアル: Azure Active Directory と Projectplace の統合 | Microsoft Azure" description="Azure Active Directory で Projectplace を使用してシングル サインオンや自動化されたプロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Projectplace の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=529080)をクリックしてください。
  
このチュートリアルでは、Azure と Projectplace の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Projectplace でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを終了すると、Projectplace に割り当てた Azure AD ユーザーは、Projectplace の企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Projectplace のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-projectplace-tutorial/IC790217.png "シナリオ")
##Projectplace のアプリケーション統合の有効化
  
このセクションでは、Projectplace のアプリケーション統合を有効にする方法を説明します。

###Projectplace のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-projectplace-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-projectplace-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に「**Projectplace**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-projectplace-tutorial/IC790218.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Projectplace]** を選び、**[完了]** をクリックしてアプリケーションを追加します。

    ![Projectplace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "Projectplace")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Projectplace に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Projectplace** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-projectplace-tutorial/IC790220.png "シングル サインオンの構成")

2.  **[ユーザーの Projectplace へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選び、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-projectplace-tutorial/IC790221.png "シングル サインオンの構成")

3.  **[アプリの URL の構成]** ページで、**[Projectplace サインオン URL]** ボックスに Projectplace テナント URL (例: "**http://company.projectplace.com*") を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-projectplace-tutorial/IC790222.png "アプリケーション URL の構成")

4.  **[Projectplace でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-projectplace-tutorial/IC790223.png "シングル サインオンの構成")

5.  Projectplace サポート チームにメタデータ ファイルを送信します。

    >[AZURE.NOTE]シングル サインオンの構成は、Projectplace サポート チームが実行する必要があります。構成が完了すると、サポート チームから通知が届きます。

6.  Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-projectplace-tutorial/IC790227.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Projectplace にログインできるようにするには、ユーザーを Projectplace にプロビジョニングする必要があります。Projectplace の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **Projectplace** 企業サイトに管理者としてログインします。

2.  **[People]** 、に移動し、**[Members]** をクリックします。

    ![ユーザー](./media/active-directory-saas-projectplace-tutorial/IC790228.png "ユーザー")

3.  **[Add Member]** をクリックします。

    ![メンバーの追加](./media/active-directory-saas-projectplace-tutorial/IC790232.png "メンバーの追加")

4.  **[Add Member]** セクションで、次の手順に従います。

    ![新しいメンバー](./media/active-directory-saas-projectplace-tutorial/IC790233.png "新しいメンバー")

    1.  **[New Members]** テキストボックスに、プロビジョニングする有効な AAD アカウントのメール アドレスを入力します。
    2.  **[Send]** をクリックします。

	    >[AZURE.NOTE]Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含むメールが送信されます。
    
>[AZURE.NOTE]Projectplace から提供されている他の Projectplace ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Projectplace に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Projectplace** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-projectplace-tutorial/IC790234.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-projectplace-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->