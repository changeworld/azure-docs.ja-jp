<properties pageTitle="チュートリアル: Azure Active Directory と Benefitsolver の統合 | Microsoft Azure" description="Azure Active Directory で Benefitsolver を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Benefitsolver の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=615070)をクリックしてください。

このチュートリアルでは、Azure と Benefitsolver の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Benefitsolver でのシングル サインオンが有効なサブスクリプション

このチュートリアルを終了すると、Benefitsolver に割り当てた Azure AD ユーザーは、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Benefitsolver のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "シナリオ")
##Benefitsolver のアプリケーション統合の有効化

このセクションでは、Benefitsolver のアプリケーション統合を有効にする方法を説明します。

###Benefitsolver のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Benefitsolver**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Benefitsolver]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Benefitsolver に対する認証を行えるようにする方法を説明します。Benefitsolver アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを **saml トークン属性**構成に追加する必要があります。次のスクリーンショットにこの例を示します。

![属性](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "属性")

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **Benefitsolver** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configure Single Sign-On")

2.  **[ユーザーの Benefitsolver へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configure Single Sign-On")

3.  **[アプリケーション設定の構成]** ページで、次の手順を実行します。

    ![Configure App Settings](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configure App Settings")

    1.  **[サインオン URL]** ボックスに、ユーザーが Benefitsolver アプリケーションへのサインオンに使用する URL を入力します (例: "*http://azure-dev.benefitsolver.com*)。
    2.  **[応答 URL]** ボックスに、Benefitsolver AssertionConsumerService URL を入力します (例: "*https://dev.benefitsolver.com/benefits/BenefitSolverView?page\_name=single\_signon\_saml*”))。  

        >[AZURE.NOTE]ご使用の環境の実際の値は、Benefitsolver サポート チームから入手できます。

    3.  **[次へ]** をクリックします。

4.  **[Benefitsolver でのシングル サインオンの構成]** ページで、メタデータをダウンロードするために、**[メタデータのダウンロード]** をクリックし、コンピューターでメタデータ ファイルをローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configure Single Sign-On")

5.  ダウンロードしたメタデータ ファイルを Benefitsolver サポート チームに送信します。

    >[AZURE.NOTE]Benefitsolver サポート チームが、実際に SSO を構成する必要があります。ご使用のサブスクリプションで SSO が有効になると通知されます。

6.  Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configure Single Sign-On")

7.  上部のメニューで、**[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。

    ![属性](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "属性")

8.  必要な属性マッピングを追加するには、次の手順を実行します。

    ![属性](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "属性")

	|属性名|属性値|
    |---|---|
    |ClientID|この値は、Benefitsolver サポート チームから入手する必要があります。|
    |ClientKey|この値は、Benefitsolver サポート チームから入手する必要があります。|
    |LogoutURL|この値は、Benefitsolver サポート チームから入手する必要があります。|
    |EmployeeID|この値は、Benefitsolver サポート チームから入手する必要があります。|

    1.  上記の表の各データ行で、**[ユーザー属性の追加]** をクリックします。
    2.  **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    3.  **[属性値]** ボックスで、その行に対して表示される属性値を選択します。
    4.  **[完了]** をクリックします。

9.  **[変更の適用]** をクリックします。
##ユーザー プロビジョニングの構成

Azure AD ユーザーが Benefitsolver にログインできるようにするには、ユーザーを Benefitsolver にプロビジョニングする必要があります。Benefitsolver の場合、Benefitsolver サポート チームがユーザーを手動で作成する必要があります。

>[AZURE.NOTE]Benefitsolver から提供されている他の Benefitsolver ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、ユーザーにアプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Benefitsolver に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Benefitsolver** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確認します。

    ![あり](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=August15_HO7-->