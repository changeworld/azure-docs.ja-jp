<properties pageTitle="チュートリアル: Azure Active Directory と Zscaler の統合 | Microsoft Azure" description="Azure Active Directory で Zscaler を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Zscaler の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=521842)をクリックしてください。
  
このチュートリアルでは、Azure と Zscaler の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Zscaler のテナント
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Zscaler のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  プロキシ設定の構成
4.  ユーザー プロビジョニングの構成
5.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-zscaler-tutorial/IC769226.png "シナリオ")

##Zscaler のアプリケーション統合の有効化
  
このセクションでは、Zscaler のアプリケーション統合を有効にする方法を説明します。

###Zscaler のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-zscaler-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-zscaler-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Zscaler**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-zscaler-tutorial/IC769227.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Zscaler]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Zscaler に対する認証を行うことができるようにする方法を説明します。この手順の途中で、証明書を Zscaler にアップロードする必要があります。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Zscaler** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの有効化](./media/active-directory-saas-zscaler-tutorial/IC769229.png "シングル サインオンの有効化")

2.  **[ユーザーの Zscaler へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-tutorial/IC769230.png "シングル サインオンの構成")

3.  **[アプリの URL の構成]** ページの **[Zscaler サインイン URL]** ボックスに、Zscaler のテナント サインイン URL を入力し、**[次へ]** をクリックします。

    >[AZURE.NOTE]サインインの URL がわからない場合は、Zscaler サポート チームに問い合わせてください。

    ![アプリケーション URL の構成](./media/active-directory-saas-zscaler-tutorial/IC769231.png "アプリケーション URL の構成")

4.  **[Zscaler でのシングル サインオンの構成]** ページで、次の手順を実行します。

    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-tutorial/IC769232.png "シングル サインオンの構成")

    1.  **[証明書のダウンロード]** をクリックし、証明書ファイルを **c:\\Zscaler.cer** としてローカルに保存します。
    2.  **[認証要求 URL]** をクリップボードにコピーします。

5.  Zscaler テナントにログインします。

6.  上部のメニューで **[管理]** をクリックします。

    ![管理](./media/active-directory-saas-zscaler-tutorial/IC769486.png "管理")

7.  **[管理者とロールの管理]** をクリックし、**[ユーザーと認証の管理]** をクリックします。

    ![管理者とロールの管理](./media/active-directory-saas-zscaler-tutorial/IC769487.png "管理者とロールの管理")

8.  **[組織の認証オプションの選択]** セクションで、次の手順を実行します。

    ![認証オプションの選択](./media/active-directory-saas-zscaler-tutorial/IC769488.png "認証オプションの選択")

    1.  **[SAML シングル サインオンを使用した認証]** を選択します。
    2.  **[SAML シングル サインオン パラメーターの構成]** をクリックします。

9.  **[SAML シングル サインオン パラメーターの構成]** ダイアログ ページで、次の手順に従い、**[完了]** をクリックします。

    ![証明書のアップロード](./media/active-directory-saas-zscaler-tutorial/IC769489.png "証明書のアップロード")

    1.  **[認証のためにユーザーに表示される SAML ポータルの URL]** テキスト ボックスに、Azure ポータルの **[認証要求 URL]** フィールドの値を貼り付けます。
    2.  **[ログイン名を含む属性]** テキスト ボックスに「**NameID**」と入力します。
    3.  **[SSL パブリック証明書のアップロード]** フィールドで、Azure ポータルからダウンロードした証明書をアップロードします。
    4.  **[SAML 自動プロビジョニングを有効にする]** を選択します。

10. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![ユーザー認証の構成](./media/active-directory-saas-zscaler-tutorial/IC769490.png "ユーザー認証の構成")

    1.  **[保存]** をクリックします。
    2.  **[今すぐ認証する]** をクリックします。

11. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-tutorial/IC769491.png "シングル サインオンの構成")

##プロキシ設定の構成

###Internet Explorer でプロキシ設定を構成するには

1.  **Internet Explorer** を開始します。

2.  **[ツール]** メニューの **[インターネット オプション]** を選択し、**[インターネット オプション]** ダイアログを開きます。

    ![を開き](./media/active-directory-saas-zscaler-tutorial/IC769492.png "を開き")

3.  **[接続]** タブをクリックします。

    ![接続](./media/active-directory-saas-zscaler-tutorial/IC769493.png "接続")

4.  **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5.  [プロキシ サーバー] セクションで、次の手順を実行します。

    ![プロキシ サーバー](./media/active-directory-saas-zscaler-tutorial/IC769494.png "プロキシ サーバー")

    1.  [LAN にプロキシ サーバーを使用する] をオンにします。
    2.  [アドレス] ボックスに「**gateway.zscalertwo.net**」と入力します。
    3.  [ポート] ボックスに「**80**」と入力します。
    4.  **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。
    5.  **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

6.  **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Zscaler にログインできるようにするには、そのユーザーを Zscaler にプロビジョニングする必要があります。Zscaler の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Zscaler** テナントにログインします。

2.  **[管理]** をクリックします。

    ![管理](./media/active-directory-saas-zscaler-tutorial/IC781035.png "管理")

3.  **[ユーザー管理]** をクリックします。

    ![ユーザー管理](./media/active-directory-saas-zscaler-tutorial/IC781036.png "ユーザー管理")

4.  **[ユーザー]** タブで、**[追加]**.をクリックします。

    ![[追加] のいずれかを](./media/active-directory-saas-zscaler-tutorial/IC781037.png "[追加] のいずれかを")

5.  [ユーザーの追加] セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-zscaler-tutorial/IC781038.png "ユーザーの追加")

    1.  プロビジョニングする有効な AAD アカウントの **[ユーザー ID]**、**[ユーザー表示名]**、**[パスワード]**、**[パスワードの確認]** に入力し、**[グループ]** と **[部署]** を選択します。
    2.  **[保存]** をクリックします。

>[AZURE.NOTE]Zscaler から提供されている他の Zscaler ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Zscaler に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Zscaler** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-zscaler-tutorial/IC769495.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-zscaler-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->