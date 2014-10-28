<properties linkid="develop-net-tutorials-get-started-vs2013" pageTitle="Get started with Azure Websites for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Website. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Azure Websites と ASP.NET を使用する

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

このチュートリアルでは、Visual Studio 2013 または Visual Studio 2013 for Web Express を使用して、ASP.NET Web アプリケーションを作成し、Azure の Web サイトにデプロイする方法を示します。このチュートリアルは、Azure または ASP.NET を使用した経験がない読者を対象にしています。このチュートリアルでは、クラウドで動作する単純な Web アプリケーションを作成します。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2013 を持っていない場合は、SDK によって Visual Studio 2013 for Web Express が自動的にインストールされます。これで、Azure 向けの開発を完全に無料で始めることができます。

学習内容:

-   Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
-   Visual Studio の ASP.NET Web プロジェクトを作成して Azure の Web サイトにデプロイする方法
-   Web プロジェクトを変更して、アプリケーションを再デプロイする方法
-   Azure の管理ポータルを使用して Web サイトを監視および管理する方法

次の図に、完成したアプリケーションを示します。

![Web site home page][Web site home page]

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
<h5><a name="note"></a>このチュートリアルを完了するには、Azure アカウントが必要です。</h5>
  <ul>
<li><a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F">無料で Azure アカウントを開く</a>ことができます - Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。</li>
<li><a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">MSDN サブスクライバーの特典を有効にする</a>こともできます - MSDN サブスクリプションにより、有料の Azure のサービスを使用できるクレジットが毎月与えられます。</li>
  <ul>
</div>

### チュートリアル セグメント

-   [開発環境を設定する][開発環境を設定する]
-   [Visual Studio で ASP.NET Web アプリケーションを作成する][Visual Studio で ASP.NET Web アプリケーションを作成する]
-   [Azure にアプリケーションを展開する][Azure にアプリケーションを展開する]
-   [変更を加えて、再展開する][変更を加えて、再展開する]
-   [管理ポータルでサイトを監視および管理する][管理ポータルでサイトを監視および管理する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [install-sdk-2013-only][install-sdk-2013-only]]

## ASP.NET Web アプリケーションを作成する

最初の手順は、Web アプリケーション プロジェクトを作成することです。Visual Studio は後でプロジェクトをデプロイする Azure の Web サイトを自動的に作成します。次の図では、この 2 つの手順について示します。

![Diagram showing project creation and deployment steps][Diagram showing project creation and deployment steps]

1.  Visual Studio 2013 または Visual Studio 2013 Express for Web を開きます。

2.  **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

3.  **[新しいプロジェクト]** ダイアログ ボックスで、**[C\#]** \> **[Web]** \> **[ASP.NET Web アプリケーション]** の順にクリックします。必要に応じて、**[Visual Basic]** を選択することもできます。

4.  ターゲット フレームワークとして **.NET Framework 4.5** が選択されていることを確認します。

5.  アプリケーションに「**MyExample**」という名前を付けて、**[OK]** をクリックします。

    ![New Project dialog box][New Project dialog box]

6.  **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択します。ASP.NET Web フォームを使用する場合は、**[Web フォーム]** テンプレートを選択できます。

    [MVC と Web フォーム][MVC と Web フォーム]は Web サイトを作成するための ASP.NET のフレームワークです。このチュートリアルでは、いずれかを選択できますが、Web フォームを選択した場合は、後で *Default.aspx* を編集する必要があります。その場合 *Index.cshtml* を編集するように指示されます。

7.  **[認証の変更]** をクリックします。

    ![New ASP.NET Project dialog box][New ASP.NET Project dialog box]

8.  **[認証の変更]** ダイアログ ボックスで、**[認証なし]** をクリックし、**[OK]** をクリックします。

    ![[認証なし]][]

    作成しているサンプル アプリケーションでは、ユーザーはログインできません。「[次のステップ][次のステップ]」セクションは、認証と承認を実装するチュートリアルにリンクしています。

9.  **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[Azure]** の設定はそのままにして、**[OK]** をクリックします。

    ![New ASP.NET Project dialog box][1]

    既定の設定は、Visual Studio が Web プロジェクトのための Azure の Web サイトを作成することを指定します チュートリアルの次のセクションでは、Web プロジェクトを新しく作成した Web サイトにデプロイします。

    (チェック ボックスのキャプションは、**"クラウドでのホスト"** または **"リモート リソースの作成"** です。いずれの場合も効果は同じです。)

10. Azure にまだサインインしていない場合は、サインインを求めるメッセージが表示されます。**[サインイン]** をクリックします。

    ![Sign in to Azure][Sign in to Azure]

11. **[Azure へのサインイン]** ダイアログ ボックスで、Azure サブスクリプションを管理する ID とパスワードを入力します。

    サインインすると、**[Azure のサイトの設定を構成する]** ダイアログ ボックスが表示され、作成するリソースが求められます。

    ![Signed in to Azure][Signed in to Azure]

12. Visual Studio には既定の **[サイト名]** が用意されていて、Azure ではアプリケーションの URL のプレフィックスとして使用されます。必要に応じて、別のサイト名を入力します。

    URL 全体は、ここに入力した内容に *.azurewebsites.net* を追加して構成されます (**[サイト名]** ボックスの横を参照)。たとえば、サイト名が `MyExample6442` である場合、URL は `MyExample6442.azurewebsites.net` です。URL は一意であることが必要です。入力した URL が既に使用されていると、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別のサイト名を入力する必要があります。

13. **[リージョン]** ドロップダウン リストで、最も近い場所を選択します。

    この設定では、Web サイトが実行される Azure データ センターを指定します。このチュートリアルでは、任意のリージョンを選択でき、選択による違いはありません。ただし、運用サイトでは、サイトにアクセスするブラウザーに最も近い Web サーバーを選択して[待機時間][待機時間]を最小にします。

14. データベース フィールドは変更せずそのままにします。

    このチュートリアルでは、データベースは使用しません。このチュートリアルの最後にある「[次のステップ][次のステップ]」セクションには、データベースの使用方法を示したチュートリアルへのリンクがあります。

15. **[OK]** をクリックします。

    数秒で、Visual Studio により、指定したフォルダーに Web プロジェクトが作成され、指定した Azure リージョンに Web サイトが作成されます。

    **[ソリューション エクスプローラー]** ウィンドウには、新しいプロジェクトのファイルとフォルダーが表示されます

    ![Solution Explorer][Solution Explorer]

    **[Web 発行アクティビティ]** ウィンドウには、サイトが作成されたことが表示されます。

    ![Web site created][Web site created]

    サーバー エクスプローラーにサイトが表示されます。

    ![Web site created][2]

## Azure にアプリケーションを展開する

1.  **[Web 発行アクティビティ]** ウィンドウで、**[今すぐ MyExample をこのサイトに発行する]** をクリックします。

    ![Web site created][3]

    数秒で、**Web の発行**ウィザードが表示されます。

    Visual Studio がプロジェクトを Azure にデプロイするために必要な設定が、*発行プロファイル*に保存されます。このウィザードではこれらの設定を確認し、変更できます。

2.  **Web の発行**ウィザードの **[接続]** タブで、**[接続の検証]** をクリックして、Web プロジェクトをデプロイするために Visual Studio が Azure に接続できることを確認します。

    ![Validate connection][Validate connection]

    接続が検証されると、**[接続の検証]** ボタンの横に緑色のチェック マークが表示されます。

3.  **[次へ]** をクリックします。

    ![Successfully validated connection][Successfully validated connection]

4.  **[設定]** タブの **[次へ]** をクリックします。

    ![Settings tab][Settings tab]

    **[構成]** および **[ファイル発行オプション]** には既定値を使用できます。

    **[構成]** ドロップダウンでは、リモート デバッグ用のデバック ビルドをデプロイできます。「[次のステップ][次のステップ]」セクションは、リモートのデバッグ モードで Visual Studio を実行する方法を示したチュートリアルにリンクしています。

    **[ファイル発行オプション]** を展開すると、このチュートリアルでは使用していないシナリオを使用できる複数の設定が表示されます。

    -   デプロイ先で追加ファイルを削除する。

        プロジェクトに存在しないファイルをサーバーから削除します。以前に別のプロジェクトをデプロイしたサイトにプロジェクトをデプロイした場合に、削除が必要になります。

    -   発行中にプリコンパイルする。

        大規模なサイトに対して初回要求の準備時間を短くできます。

    -   App\_Data フォルダーからファイルを除外する。

        テストでは、運用環境にはデプロイしない App\_Data の SQL Server データベース ファイルを使用する場合があります。

5.  **[プレビュー]** タブで、**[プレビューの開始]** をクリックします。

    ![StartPreview button in the Preview tab][StartPreview button in the Preview tab]

    このタブに、サーバーにコピーされるファイルの一覧が表示されます。プレビューの表示は、アプリケーションの発行に必要ではありませんが、知っておくと便利な機能です。

6.  **[発行]** をクリックします。

    ![StartPreview file output][StartPreview file output]

    Azure サーバーにファイルをコピーする処理が開始されます。

    **[出力]** ウィンドウと **[Web 発行アクティビティ]** ウィンドウでは、実行された展開操作が表示され、展開が問題なく完了したことが報告されます。

    ![Output window reporting successful deployment][Output window reporting successful deployment]

    展開が成功すると、自動的に既定のブラウザーが開き、展開先の Web サイトの URL にアクセスします。
    これで、作成したアプリケーションはクラウドで実行されています。ブラウザー アドレス バーの URL は、インターネットから読み込まれているサイトを示します。

    ![Web site running in Azure][Web site running in Azure]

7.  ブラウザーを閉じます。

## 変更を加えて、再展開する

チュートリアルのこのセクションでは、ホーム ページの **h1** 見出しを変更し、開発用コンピューターでローカルでプロジェクトを実行して変更を確認して、その変更を Azure にデプロイします。

1.  **ソリューション エクスプローラー**の *Views/Home/Index.cshtml* ファイルまたは *.vbhtml* ファイルを開き、**h1** 見出しを「ASP.NET」から「ASP.NET および Azure」に変更して、ファイルを保存します。

    ![MVC index.cshtml][MVC index.cshtml]

    ![MVC h1 change][MVC h1 change]

2.  Ctrl + F5 キーを押してローカル コンピューターでサイトを実行し、更新した見出しを確認します。

    ![Web site running locally][Web site running locally]

    `http://localhost` URL には、ローカル コンピューターで実行中であることが表示されます。既定では、IIS Express で実行されます。これは、Web アプリケーションの開発での使用を想定した、IIS の軽量バージョンです。

3.  ブラウザーを閉じます。

4.  **[ソリューション エクスプローラー]** でプロジェクトを右クリックし、**[発行]** を選択します。

    ![Chooose Publish][Chooose Publish]

    **Web の発行**ウィザードの [プレビュー] タブが表示されます。発行設定を変更する必要がある場合、異なるタブを選択できますが、今は同じ設定で再展開します。

5.  **Web の発行**ウィザードで、**[発行]** をクリックします。

    ![Click Publish][Click Publish]

    Visual Studio ではプロジェクトが Azure に展開され、既定のブラウザーでサイトが開きます。

    ![Changed site deployed][Web site home page]

**ヒント:** 簡易デプロイメントの場合も、**[Web の 1 クリック発行]** ツール バーを有効にできます。**[表示]** \> **[ツール バー]** をクリックして、**[Web の 1 クリック発行]** を選択します。このツール バーによって、プロファイルを選択し、ボタンをクリックして発行するか、またはボタンをクリックして **Web の発行**ウィザードを開くことができます。

![Web One Click Publish Toolbar][Web One Click Publish Toolbar]

## 管理ポータルでサイトを監視および管理する

[Azure の管理ポータル][Azure の管理ポータル]は、作成した Web サイトなどの Azure のサービスを管理および監視できる Web インターフェイスです。チュートリアルのこのセクションでは、ポータルで何ができるかを確認できます。

1.  ブラウザーで、[][]<http://manage.windowsazure.com></a> に移動し、Azure 資格情報でサインインします。

    このポータルには、Azure サービスの一覧が表示されます。

2.  Web サイトの名前をクリックします。

    ![Portal home page with new web site called out][Portal home page with new web site called out]

3.  **[ダッシュボード]** タブをクリックします。

    **[ダッシュボード]** タブに、使用統計の概要、および一般的に使用される複数のサイト管理機能へのリンクが表示されます。**[概要]** には、アプリケーションのホーム ページへのリンクも表示されます。

    ![Portal web site dashboard tab][Portal web site dashboard tab]

    この時点では、サイトにはトラフィックがそれほど存在しないため、グラフには何も表示されない可能性があります。アプリケーションにアクセスし、何回かページを更新してから、ポータルの **[ダッシュボード]** ページを更新すると、統計が表示されます。詳細を表示するには、**[監視]** タブをクリックします。

4.  **[構成]** タブをクリックします。

    [[構成]][] タブでは、サイトで使用する .NET バージョンを管理し、[WebSockets][WebSockets] と[診断ログ][診断ログ]などの機能を有効にして、[接続文字列][接続文字列]、その他を設定します。

    ![Portal web site configure tab][Portal web site configure tab]

5.  **[規模の設定]** タブをクリックします。

    Websites サービスの有料レベルでは、[[規模の設定]][] タブで Web アプリケーションで使用するコンピューターの大きさと数を管理して、トラフィックの変化に対応します。

    手動で規模を変更するか、または条件やスケジュールを設定して自動的に規模の設定ができます。

    ![Portal website scale tab][Portal website scale tab]

これらは、管理ポータル機能の一部にすぎません。新しい Web サイトの作成、既存のサイトの削除、サイトの停止や再開に加え、データベースや Virtual Machines など他の種類の Azure サービスを管理することもできます。

**ヒント:** これらは新しい管理ポータルのプレビューで、最終的には現在使用中のポータルと置き換わります。詳細については、「[Azure プレビュー ポータル][Azure プレビュー ポータル]」を参照してください。

## 次のステップ

このチュートリアルでは、Azure の Web サイトに単純な Web アプリケーションを作成してデプロイする方法について説明しました。詳細についての関連トピックとリソースがあります。

-   Web プロジェクトをデプロイする他の方法

    このチュートリアルでは、サイトを作成して、オールインワン操作ですべてを展開する最も簡単な方法を説明しました。Visual Studio を使用した、または[ソース管理システム][ソース管理システム]からの[デプロイメントの自動化][デプロイメントの自動化]による他のデプロイ方法の概要については、「[Azure の Web サイトをデプロイする方法][Azure の Web サイトをデプロイする方法]」を参照してください。

    Visual Studio は、デプロイメントを自動化する Windows PowerShell スクリプトも生成します。詳細については、「[Automate Everything (Building Real-World Cloud Apps with Azure) (すべてを自動化 - Azure での実際のクラウド アプリケーションのビルド)][Automate Everything (Building Real-World Cloud Apps with Azure) (すべてを自動化 - Azure での実際のクラウド アプリケーションのビルド)]」を参照してください。

-   Visual Studio での Web サイトの管理方法

    **サーバー エクスプローラー**で実行できるサイト管理機能については、「[Visual Studio での Azure の Web サイトのトラブルシューティング][Visual Studio での Azure の Web サイトのトラブルシューティング]」を参照してください。

-   Web サイトのトラブルシューティングの方法

    Visual Studio には、リアルタイムで生成される Azure ログの表示を容易にする機能があります。Azure では、リモートでデバッグ モードを使用して実行することもできます。詳細については、「[Visual Studio での Azure の Web サイトのトラブルシューティング][Visual Studio での Azure の Web サイトのトラブルシューティング]」を参照してください。

-   データベースと承認の機能を追加する方法

    データベースへのアクセス方法、および一部のサイト機能を権限のあるユーザーだけに制限する方法のチュートリアルについては、「[メンバーシップ、OAuth、SQL Database を使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトに展開する][メンバーシップ、OAuth、SQL Database を使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトに展開する]」を参照してください。

-   カスタム ドメイン名および SSL の追加方法

    SSL および独自のドメイン (contoso.azurewebsites.net の代わりに www.contoso.com など) の使用方法については、次のリソースを参照してください。

    -   [Azure の Web サイトのカスタム ドメイン名の構成][Azure の Web サイトのカスタム ドメイン名の構成]
    -   [Azure の Web サイトでの HTTPS の有効化][Azure の Web サイトでの HTTPS の有効化]
-   アイドル時間終了後のウェイクアップの待機時間を回避する方法

    既定では、アイドル状態がしばらく続いている Web サイトはアンロードされます。アイドル状態後の初回要求は、サイトが再度読み込まれるまで待機する必要があります。この待機時間を回避するために、AlwaysOn 機能を有効にすることができます。詳細については、「[Web サイトの構成方法][Web サイトの構成方法]」の構成オプションを参照してください。

-   リアルタイム機能 (チャットなど) の追加方法

    Web サイトでリアルタイム機能 (チャット サービス、ゲーム、株価情報など) を使用する場合は、[WebSockets][WebSockets] の転送方法で [ASP.NET SignalR][ASP.NET SignalR] を使用すると、パフォーマンスが最大になります。詳細については、「[Using SignalR with Windows Azure Websites (Windows Azure Websites で SignalR を使用する)][Using SignalR with Windows Azure Websites (Windows Azure Websites で SignalR を使用する)]」を参照してください。

-   Web アプリケーションに対する Azure Websites、Cloud Services、および Virtual Machines の選択方法

    Azure では、このチュートリアルで示したように Websites で、または Cloud Services や Virtual Machines で、Web アプリケーションを実行することができます。詳細については、「[Azure 実行モデル][Azure 実行モデル]」と「[Azure の Web Sites、Cloud Services、Virtual Machines: いつ、どれを使用するか][Azure の Web Sites、Cloud Services、Virtual Machines: いつ、どれを使用するか]」を参照してください。

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [Web site home page]: ./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png
  [無料で Azure アカウントを開く]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [MSDN サブスクライバーの特典を有効にする]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [開発環境を設定する]: #set-up-the-development-environment
  [Visual Studio で ASP.NET Web アプリケーションを作成する]: #create-an-aspnet-web-application
  [Azure にアプリケーションを展開する]: #deploy-the-application-to-azure
  [変更を加えて、再展開する]: #make-a-change-and-redeploy
  [管理ポータルでサイトを監視および管理する]: #monitor-and-manage-the-site-in-the-management-portal
  [次のステップ]: #next-steps
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [Diagram showing project creation and deployment steps]: ./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png
  [New Project dialog box]: ./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png
  [MVC と Web フォーム]: http://www.asp.net/get-started/websites
  [New ASP.NET Project dialog box]: ./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png
  [[認証なし]]: ./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png
  [1]: ./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png
  [Sign in to Azure]: ./media/web-sites-dotnet-get-started-vs2013/signin.png
  [Signed in to Azure]: ./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png
  [待機時間]: http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090
  [Solution Explorer]: ./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png
  [Web site created]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png
  [2]: ./media/web-sites-dotnet-get-started-vs2013/siteinse.png
  [3]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png
  [Validate connection]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png
  [Successfully validated connection]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png
  [Settings tab]: ./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png
  [StartPreview button in the Preview tab]: ./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png
  [StartPreview file output]: ./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png
  [Output window reporting successful deployment]: ./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png
  [Web site running in Azure]: ./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png
  [MVC index.cshtml]: ./media/web-sites-dotnet-get-started-vs2013/index.png
  [MVC h1 change]: ./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png
  [Web site running locally]: ./media/web-sites-dotnet-get-started-vs2013/localandazure.png
  [Chooose Publish]: ./media/web-sites-dotnet-get-started-vs2013/choosepublish.png
  [Click Publish]: ./media/web-sites-dotnet-get-started-vs2013/clickpublish.png
  [Web One Click Publish Toolbar]: ./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png
  [Azure の管理ポータル]: /en-us/services/management-portal/
  []: 
  [Portal home page with new web site called out]: ./media/web-sites-dotnet-get-started-vs2013/portalhome.png
  [Portal web site dashboard tab]: ./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png
  [[構成]]: /ja-jp/documentation/articles/web-sites-configure//
  [WebSockets]: /blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/
  [診断ログ]: /ja-jp/documentation/articles/web-sites-enable-diagnostic-log/
  [接続文字列]: /blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [Portal web site configure tab]: ./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png
  [[規模の設定]]: /ja-jp/documentation/articles/web-sites-scale/
  [Portal website scale tab]: ./media/web-sites-dotnet-get-started-vs2013/portalscale.png
  [Azure プレビュー ポータル]: /en-us/overview/preview-portal/
  [ソース管理システム]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [デプロイメントの自動化]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [Azure の Web サイトをデプロイする方法]: /ja-jp/documentation/articles/web-sites-deploy/"
  [Automate Everything (Building Real-World Cloud Apps with Azure) (すべてを自動化 - Azure での実際のクラウド アプリケーションのビルド)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Visual Studio での Azure の Web サイトのトラブルシューティング]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [メンバーシップ、OAuth、SQL Database を使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトに展開する]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Azure の Web サイトのカスタム ドメイン名の構成]: /ja-jp/documentation/articles/web-sites-custom-domain-name/
  [Azure の Web サイトでの HTTPS の有効化]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/
  [Web サイトの構成方法]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [Using SignalR with Windows Azure Websites (Windows Azure Websites で SignalR を使用する)]: http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites
  [Azure 実行モデル]: /en-us/develop/net/fundamentals/compute/
  [Azure の Web Sites、Cloud Services、Virtual Machines: いつ、どれを使用するか]: /en-us/manage/services/web-sites/choose-web-app-service/
