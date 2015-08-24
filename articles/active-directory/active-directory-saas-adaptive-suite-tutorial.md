<properties pageTitle="チュートリアル: Azure Active Directory と Adaptive Suite の統合 | Microsoft Azure" description="Azure Active Directory で Adaptive Suite を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Adaptive Suite の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=615749)をクリックしてください。

このチュートリアルでは、Azure と Adaptive Suite の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Adaptive Suite のテナント

このチュートリアルを完了すると、Adaptive Suite に割り当てた Azure AD ユーザーは、Adaptive Suite 企業サイト (サービス プロバイダーが開始したサインオン) で、または[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Adaptive Suite のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "シナリオ")
##Adaptive Suite のアプリケーション統合の有効化

このセクションでは、Adaptive Suite のアプリケーション統合を有効にする方法を説明します。

###Adaptive Suite のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **[検索]** ボックスに、「**Adaptive Suite**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Adaptive Suite]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Adaptive Suite に対する認証を行えるようにする方法を説明します。Adaptive Suite にシングル サインオンを構成するには、証明書の拇印の値を取得する必要があります。この手順に慣れていない場合は、[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI) を参照してください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Adaptive Suite** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "シングル サインオンの構成")

2.  **[ユーザーの Adaptive Suite へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "シングル サインオンの構成")

3.  **[アプリケーション設定の構成]** ページの **[応答 URL]** テキスト ボックスに、次のパターン ”*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" を使用して URL を入力してから、**[次へ]** をクリックします。

    >[AZURE.NOTE]この値は、Adaptive Suite の **[SAML SSO 設定]** ページから取得できます。

    ![Configure App Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configure App Settings")

4.  **[Adaptive Suite でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "シングル サインオンの構成")

5.  別の Web ブラウザーのウィンドウで、管理者として Adaptive Suite 企業サイトにログインします。

6.  **[管理者]** に移動します。

    ![管理者](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "管理者")

7.  **[ユーザーとロール]** セクションで、**[SAML SSO 設定の管理]** をクリックします。

    ![SAML SSO 設定の管理](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "SAML SSO 設定の管理")

8.  **[SAML SSO 設定]** ページで、次の手順を実行します。

    ![SAML SSO 設定](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO 設定")

    1.  **[ID プロバイダー名]** テキスト ボックスに、構成の名前を入力します。
    2.  Azure ポータルの **[Adaptive Suite でのシングル サインオンの構成]** ダイアログ ページで、**[エンティティ ID]** の値をコピーし、**[ID プロバイダー エンティティ ID]** テキスト ボックスに貼り付けます。
    3.  Azure ポータルの **[Adaptive Suite でのシングル サインオンの構成]** ダイアログ ページで、**[SAML SSO URL]** の値をコピーし、**[ID プロバイダー SSO URL]** テキスト ボックスに貼り付けます。
    4.  Azure ポータルの **[Adaptive Suite でのシングル サインオンの構成]** ダイアログ ページで、**[SAML SSO URL]** の値をコピーし、**[カスタム ログアウト URL]** テキスト ボックスに貼り付けます。
    5.  ダウンロードした証明書をアップロードするには、**[ファイルの選択]**をクリックします。
    6.  **[SAML ユーザー ID]** として、**[ユーザーの Adaptive Insights ユーザー名]** を選択します。
    7.  **[SAML ユーザー ID の場所]** として、**[サブジェクトの NameID 内のユーザー ID ]** を選択します。
    8.  **[SAML NameID 形式]** として、**[電子メール アドレス]** を選択します。
    9.  **[SAML を有効にする]** として、**[SAML SSO を許可して、Adaptive Insights に直接ログインする]** を選択します。
    10. **[保存]** をクリックします。

9.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]**をクリックして **[シングル サインオンの構成] **ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Adaptive Suite にログインできるようにするには、そのユーザーを Adaptive Suite にプロビジョニングする必要があります。Adaptive Suite の場合、プロビジョニングは手動で実行します。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Adaptive Suite** 企業サイトに管理者としてログインします。

2.  **[管理者]**に移動します。

    ![管理者](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "管理者")

3.  **[ユーザーとロール]** セクションで **[ユーザーの追加]** をクリックします。

    ![ユーザーの追加](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "ユーザーの追加")

4.  **[新しいユーザー]** セクションで、次の手順に従います。

    ![送信](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "送信")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの **[名前]**、**[ログイン]**、**[電子メール]**、**[パスワード]** を入力します。
    2.  **[ロール]** を選択します。
    3.  **[送信]** をクリックします。

>[AZURE.NOTE]他の Adaptive Suite ユーザー アカウント作成ツールまたは Adaptive Suite から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Adaptive Suite に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  Adaptive Suite アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を参照してください。

<!---HONumber=August15_HO7-->