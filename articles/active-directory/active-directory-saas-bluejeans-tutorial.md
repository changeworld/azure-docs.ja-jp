<properties pageTitle="チュートリアル: Azure AD と BlueJeans の統合 | Microsoft Azure" description="Azure Active Directory で BlueJeans を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure AD と BlueJeans の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=526791)をクリックしてください。

このチュートリアルでは、Azure と BlueJeans の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   BlueJeans でのシングル サインオンが有効なサブスクリプション

このチュートリアルを終了すると、BlueJeans に割り当てた Azure AD ユーザーは、BlueJeans 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  BlueJeans のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "シナリオ")
##BlueJeans のアプリケーション統合の有効化

このセクションでは、BlueJeans のアプリケーション統合を有効にする方法について説明します。

###BlueJeans のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**BlueJeans**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[BlueJeans]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで BlueJeans に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **BlueJeans** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "シングル サインオンの構成")

2.  **[ユーザーの BlueJeans へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configure Single Sign-On")

3.  **[アプリの URL の構成]** ページで、**[BlueJeans サインオン URL]** ボックスに、"**https://company.BlueJeans.com*" のパターンで URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "アプリケーション URL の構成")

4.  **[BlueJeans でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、**BlueJeans** 企業サイトに管理者としてログインします。

6.  **[管理]、[グループ設定]、[セキュリティ]** の順にクリックします。

    ![管理者](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "管理者")

7.  **[セキュリティ]** セクションで、次の手順を実行します。

    ![SAML シングル サインオン](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML シングル サインオン")

    1.  **[SAML シングル サインオン]** を選択します。
    2.  **[自動プロビジョニングの有効化]** を選択します。

8.  次の手順を実行します。

    ![証明書パス](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "証明書パス")

    1.  **[ファイルの選択]** をクリックし、ダウンロードした証明書をアップロードします。
    2.  Azure ポータルで、**[BlueJeans でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[ログイン URL]** ボックスに貼り付けます。
    3.  Azure ポータルで、**[BlueJeans でのシングル サインオンの構成]** ダイアログ ページの **[パスワード変更 URL]** の値をコピーし、**[パスワード変更の URL]** ボックスに貼り付けます。
    4.  Azure ポータルで、**[BlueJeans でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。

9.  次の手順を実行します。

    ![変更を保存](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "変更を保存")

    1.  **[ユーザー ID]** ボックスに、「****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**」と入力します。
2.  **[電子メール]** ボックスに、「****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**」と入力します。
3.  **[変更を保存]** をクリックします。

10. Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが BlueJeans にログインできるようにするには、ユーザーを BlueJeans にプロビジョニングする必要があります。BlueJeans の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **BlueJeans** 企業サイトに管理者としてログインします。

2.  **[管理]、[ユーザーの管理]、[ユーザーの追加]** の順にクリックします。

    ![管理者](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "管理者")

    >[AZURE.IMPORTANT]**[ユーザーの追加]** タブは、**[セキュリティ]** タブの **[自動プロビジョニングの有効化]** がオフになっている場合にのみ使用できます。

3.  **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "ユーザーの追加")

    1.  対応するボックスに、プロビジョニングする有効な AAD アカウントの **BlueJeans ユーザー名**、**電子メール アドレス**、**BlueJeans ミーティング ID**、**モデレーター パスコード**、**フル ネーム**、**会社**を入力します。
    2.  **[ユーザーの追加]** をクリックします。

>[AZURE.NOTE]BlueJeans から提供されている他の BlueJeans ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、ユーザーにアプリケーションへのアクセス権を付与する必要があります。

###ユーザーを BlueJeans に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **BlueJeans** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->