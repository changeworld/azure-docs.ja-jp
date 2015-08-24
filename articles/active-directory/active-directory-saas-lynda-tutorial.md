<properties pageTitle="チュートリアル: Azure Active Directory と Lynda.com の統合 | Microsoft Azure" description="Azure Active Directory で Lynda.com を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Lynda.com の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=524768)をクリックしてください。
  
このチュートリアルでは、Azure と Lynda.com の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Lynda.com テナント
  
このチュートリアルを完了すると、Lynda.com に割り当てた Azure AD ユーザーは、Lynda.com の企業サイト (サービス プロバイダーが開始したサインオン) で、または[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Lynda.com のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーを割り当てる

![シナリオ](./media/active-directory-saas-lynda-tutorial/IC781046.png "シナリオ")
##Lynda.com のアプリケーション統合の有効化
  
このセクションでは、Lynda.com のアプリケーション統合を有効にする方法について説明します。

###Lynda.com のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-lynda-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-lynda-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-lynda-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Lynda.com**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-lynda-tutorial/IC777524.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Lynda.com]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Lynda.com に対する認証を行うことができるようにする方法を説明します。

>[AZURE.IMPORTANT]Lynda.com テナントでシングル サインオンを構成できるようにするには、まず Lynda.com テクニカル サポートに連絡してこの機能を有効にする必要があります。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **[Lynda.com]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-lynda-tutorial/IC777526.png "シングル サインオンの構成")

2.  **[ユーザーの Lynda.com へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-lynda-tutorial/IC777527.png "シングル サインオンの構成")

3.  **[アプリの URL の構成]** ページで、**[Lynda.com サインイン URL]** ボックスに Lynda.com テナントの URL (例: **https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*) を入力し、**[次へ]** をクリックします。

    ![アプリ URL の構成](./media/active-directory-saas-lynda-tutorial/IC781047.png "アプリ URL の構成")

4.  **[Lynda.com でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、証明書ファイルをコンピューターのローカルに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-lynda-tutorial/IC777529.png "シングル サインオンの構成")

5.  ダウンロードしたメタデータ ファイルを Lynda.com サポート チームに送信します。シングル サインオンの構成は、Lynda.com のサポート チームが行います。

6.  Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-lynda-tutorial/IC777530.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
Lynda.com へのユーザー プロビジョニングの構成にあたって必要な操作はありません。割り当て済みユーザーがアクセス パネルを使用して Lynda.com にログインしようとすると、そのユーザーが存在するかどうかが Lynda.com によって確認されます。使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lynda.com により自動的に作成されます。

>[AZURE.NOTE]Lynda.com から提供されている他の Lynda.com ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Lynda.com に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  Lynda.com アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-lynda-tutorial/IC777531.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-lynda-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=August15_HO7-->