<properties pageTitle="チュートリアル: Azure Active Directory と Workday の統合 | Microsoft Azure" description="Azure Active Directory で Workday を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Workday の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=330042)をクリックしてください。
  
このチュートリアルでは、Azure と Workday の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Workday のテナント
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Workday のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザー プロビジョニングの構成

![シナリオ](./media/active-directory-saas-workday-tutorial/IC782919.png "シナリオ")

##Workday のアプリケーション統合の有効化
  
このセクションでは、Workday のアプリケーション統合を有効にする方法を説明します。

###Workday のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-workday-tutorial/IC700994.png "アプリケーション")

4.  **アプリケーション ギャラリー**を開くには、**[アプリケーションの追加]**、**[組織で使用するアプリケーションを追加]** の順にクリックします。

    ![どの操作を行いますか。](./media/active-directory-saas-workday-tutorial/IC700995.png "どの操作を行いますか。")

5.  **検索ボックス**に、「**Workday**」と入力します。

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  結果ウィンドウで **[Workday]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Workday に対する認証を行うことができるようにする方法を説明します。この手順の途中で、Base-64 でエンコードされた証明書を作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  **[Workday]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782920.png "シングル サインオンの構成")

2.  **[ユーザーの Workday へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782921.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-workday-tutorial/IC782957.png "アプリケーション URL の構成")

    1.  **[サインオン URL]** ボックスに、ユーザーが Workday へのサインオンに使用する URL を入力します (例: *https://impl.workday.com/\<tenant>/login-saml2.htmld*)。
    2.  **[Workday 応答 URL]** ボックスに、Workday 応答 URL (例: *https://impl.workday.com/\<tenant>/login-saml.htmld*) を入力します。

4.  **[Workday でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782922.png "シングル サインオンの構成")

5.  別の Web ブラウザー ウィンドウで、Workday 企業サイトに管理者としてログインします。

6.  **[メニュー] > [ワークベンチ]** に移動します。

    ![ワークベンチ](./media/active-directory-saas-workday-tutorial/IC782923.png "ワークベンチ")

7.  **[アカウント管理]** に移動します。

    ![アカウント管理](./media/active-directory-saas-workday-tutorial/IC782924.png "アカウント管理")

8.  **[テナントのセットアップの編集 – セキュリティ]** に移動します。

    ![テナントのセキュリティの編集](./media/active-directory-saas-workday-tutorial/IC782925.png "テナントのセキュリティの編集")

9.  **[リダイレクト URL]** セクションで、次の手順を実行します。

    ![リダイレクト URL](./media/active-directory-saas-workday-tutorial/IC782958.png "リダイレクト URL")

    1.  **[行の追加]** をクリックします。
    2.  **[ログイン リダイレクトの URL]** ボックスと **[モバイル リダイレクトの URL]** ボックスに、Azure ポータルの **[アプリケーション URL の構成]** で **[Workday テナント URL]** に入力した URL を入力します。
    3.  **[環境]** テキスト ボックスに、環境の名前を入力します。  

        >[AZURE.NOTE][環境] 属性の値が、テナント URL の値に関連付けられます。
		>
        >-   Workday テナント URL のドメイン名が impl で始まる場合 (例: *https://impl.workday.com/\<tenant>/login-saml2.htmld*)、**[環境]** 属性を "実装" に設定する必要があります。
        >-   ドメイン名が impl 以外で始まる場合は、Workday に問い合わせて、対応する **[環境]** の値を取得してください。

10. **[SAML 設定]** セクションで、次の手順を実行します。

    ![SAML のセットアップ](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML のセットアップ")

    1.  **[SAML 認証を有効にする]** を選択します。
    2.  **[行の追加]** をクリックします。

11. [SAML ID プロバイダー] セクションで、次の手順に従います。

    ![SAML ID プロバイダー](./media/active-directory-saas-workday-tutorial/IC782927.png "SAML ID プロバイダー")

    1.  [ID プロバイダー名] テキスト ボックスに、プロバイダー名を入力します (例: *SPInitiatedSSO*)。
    2.  Azure ポータルで、**[Workday でのシングル サインオンの構成]** ダイアログ ページの **[ID プロバイダーの ID]** の値をコピーし、**[発行者]** テキスト ボックスに貼り付けます。
    3.  **[ID プロバイダーの公開鍵証明書]** をクリックし、**[作成]** をクリックします。![作成](./media/active-directory-saas-workday-tutorial/IC782928.png "作成")
    4.  **[x509 公開鍵の作成]** をクリックします。![作成](./media/active-directory-saas-workday-tutorial/IC782929.png "作成")
    5.  **[x509 公開鍵の表示]** セクションで、次の手順を実行します。![X509 公開鍵の表示](./media/active-directory-saas-workday-tutorial/IC782930.png "X509 公開鍵の表示")
        1.  **[名前]** テキスト ボックスに、証明書の名前を入力します (例: *PPE\_SP*)。
        2.  **[有効期間の開始日]** テキスト ボックスに、証明書の有効期間の開始日を示す属性の値を入力します。
        3.  **[有効期間の終了日]** テキスト ボックスに、証明書の有効期間の終了日を示す属性の値を入力します。
		
            >[AZURE.NOTE]有効期間の開始日と終了日は、ダウンロードした証明書をダブルクリックして確認できます。日付は **[詳細]** タブに表示されます。

        4.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。

			>[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

        5.  Base 64 でエンコードされた証明書をメモ帳で開き、その内容をコピーします。
        6.  **[証明書]** テキスト ボックスに、クリップボードの内容を貼り付けます。
        7.  **[OK]** をクリックします。

    6.  次の手順に従います。![SSO 構成](./media/active-directory-saas-workday-tutorial/IC792131.png "SSO 構成")
        1.  **[サービス プロバイダー ID]** テキスト ボックスに「* ***http://www.workday.com**」と入力します。
2.  **[SP によって開始された SAML 認証を有効にする]** を選択します。
        3.  Azure ポータルの **[Workday でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[IdP SSO サービス URL]** テキスト ボックスに貼り付けます。
        4.  **[SP によって開始された認証要求を圧縮しない]** を選択します。

    7.  次の手順に従います。![認証要求署名方法](./media/active-directory-saas-workday-tutorial/IC782932.png "認証要求署名方法")
        1.  **[認証要求署名方法]** として **[SHA256]** を選択します。

    8.  **[OK]** をクリックします。![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Azure AD ポータルの **[Workday でのシングル サインオンの構成]** ページで、**[完了]** をクリックしてダイアログ ボックスを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782934.png "シングル サインオンの構成")

##ユーザー プロビジョニングの構成
  
Workday にテスト ユーザーをプロビジョニングするには、Workday のサポート チームに連絡する必要があります。Workday のサポート チームにユーザーを作成してもらいます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Workday に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Workday** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-workday-tutorial/IC782935.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-workday-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=August15_HO7-->