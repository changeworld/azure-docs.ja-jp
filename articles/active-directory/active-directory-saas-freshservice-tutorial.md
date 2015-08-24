<properties pageTitle="チュートリアル: Azure Active Directory と FreshService の統合 | Microsoft Azure" description="Azure Active Directory で FreshService を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と FreshService の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=529827)をクリックしてください。
  
このチュートリアルでは、Azure と FreshService の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   FreshService でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、FreshService に割り当てた Azure AD ユーザーは、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  FreshService のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-freshservice-tutorial/IC790807.png "シナリオ")
##FreshService のアプリケーション統合の有効化
  
このセクションでは、FreshService のアプリケーション統合を有効にする方法について説明します。

###FreshService のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-freshservice-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-freshservice-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**FreshService**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-freshservice-tutorial/IC790808.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[FreshService]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![FreshService](./media/active-directory-saas-freshservice-tutorial/IC790809.png "FreshService")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで FreshService に対する認証を行うことができるようにする方法を説明します。FreshService にシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **FreshService** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configure Single Sign-On")

2.  **[ユーザーの FreshService へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページの **[FreshService のサインオン URL]** ボックスに、ユーザーが FreshService アプリケーションにサインオンする際に使用する URL (例: **http://democompany.freshservice.com/*")) を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-freshservice-tutorial/IC790812.png "アプリケーション URL の構成")

4.  **[FreshService でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターにローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、FreshService 企業サイトに管理者としてログインします。

6.  上部のメニューで **[管理者]** をクリックします。

    ![管理者](./media/active-directory-saas-freshservice-tutorial/IC790814.png "管理者")

7.  **[カスタマー ポータル]** で **[セキュリティ]** をクリックします。

    ![セキュリティ](./media/active-directory-saas-freshservice-tutorial/IC790815.png "セキュリティ")

8.  **[セキュリティ]** セクションで、次の手順を実行します。

    ![シングル サインオン](./media/active-directory-saas-freshservice-tutorial/IC790816.png "シングル サインオン")

    1.  **[シングル サインオン]** をオンに切り替えます。
    2.  **[SAML SSO]** を選択します。
    3.  Azure ポータルの **[FreshService でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** の値をコピーし、**[SAML ログイン URL]** ボックスに貼り付けます。
    4.  Azure ポータルの **[FreshService でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** の値をコピーし、**[SAML ログアウト URL]** ボックスに貼り付けます。
    5.  エクスポートした証明書から **[拇印]** の値をコピーして、**[セキュリティ証明書フィンガープリント]** ボックスに貼り付けます。
    
        >[AZURE.TIP]詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

9.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが FreshService にログインできるようにするには、そのユーザーを FreshService にプロビジョニングする必要があります。FreshService の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **FreshService** 企業サイトに管理者としてログインします。

2.  上部のメニューで **[管理者]** をクリックします。

    ![管理者](./media/active-directory-saas-freshservice-tutorial/IC790814.png "管理者")

3.  **[ユーザー管理**] セクションで、**[要求者]** をクリックします。

    ![要求者](./media/active-directory-saas-freshservice-tutorial/IC790818.png "要求者")

4.  **[新しい要求者]** をクリックします。

    ![新しい要求者](./media/active-directory-saas-freshservice-tutorial/IC790819.png "新しい要求者")

5.  **[新しい要求者]** セクションで、次の手順を実行します。

    ![新しい要求者](./media/active-directory-saas-freshservice-tutorial/IC790820.png "新しい要求者")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの属性として**名前**および**電子メール**を入力します。
    2.  **[保存]** をクリックします。

    >[AZURE.NOTE]Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE]FreshService から提供されている他の FreshService ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを FreshService に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **FreshService** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-freshservice-tutorial/IC790821.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-freshservice-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を参照してください。

<!---HONumber=August15_HO7-->