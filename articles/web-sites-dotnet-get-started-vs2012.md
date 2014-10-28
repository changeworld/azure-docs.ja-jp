<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure Websites and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Azure Websites と ASP.NET を使用する

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>注</strong><p><a href="/en-us/develop/net/tutorials/get-started/">このチュートリアルの新しいバージョン</a>が提供されています。Visual Studio 2012 を使用する場合はこのバージョンを引き続き利用できますが、最新の Azure SDK の機能の中には、取り上げられていないものもあります。</p></div>

このチュートリアルでは、Visual Studio 2012 または Visual Studio 2012 for Web Express の Web の発行ウィザードを使用して、ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする方法を示します。必要に応じて、Visual Studio 2010 または Visual Web Developer Express 2010 を使用して、チュートリアルの手順に従うこともできます。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2012 を持っていない場合は、SDK によって Visual Studio 2012 for Web Express が自動的にインストールされます。これで、Azure 向けの開発を完全に無料で始めることができます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、クラウドで動作する単純な Web アプリケーションを作成します。

学習内容:

-   Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
-   Visual Studio の ASP.NET MVC 4 プロジェクトを作成して Azure の Web サイトに発行する方法

次の図に、完成したアプリケーションを示します。

![Web サイトの例][Web サイトの例]

<div class="dev-callout"><p><strong>注</strong> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントを持っていない場合は、<a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN サブスクライバーの特典を有効にする</a>か、<a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">無料評価版にサインアップ</a>してください。</p></div>

### チュートリアル セグメント

1.  [開発環境を設定する][開発環境を設定する]
2.  [Azure に Web サイトを作成する][Azure に Web サイトを作成する]
3.  [ASP.NET MVC 4 アプリケーションを作成する][ASP.NET MVC 4 アプリケーションを作成する]
4.  [Azure にアプリケーションを展開する][Azure にアプリケーションを展開する]
5.  [次のステップ][次のステップ]

[WACOM.INCLUDE [install-sdk-2012-only][install-sdk-2012-only]]

## <a name="setupwindowsazure"></a><span class="short-header">サイトの作成</span>Web サイトを作成する

次の手順では、Azure の Web サイトを作成します。

1. [Azure の管理ポータル][Azure の管理ポータル]で、**[Web サイト]** をクリックし、**[新規作成]** をクリックします。

 ![新しい Web サイト][新しい Web サイト]

2. **[簡易作成]** をクリックします。

	![簡易作成][簡易作成]

3. ウィザードの **[Web サイトの作成]** 手順で、アプリケーションの一意の URL として使用する文字列を **[URL]** ボックスに入力します。URL 全体は、ここで入力した内容に、テキスト ボックスの横に表示されるサフィックスを追加して構成されます。図には "**example1**" と表示されていますが、URL 用にその文字列が既に取得されている場合は、別の値を入力する必要があります。

4. **[リージョン]** ボックスの一覧で、現在の所在地に最も近いリージョンを選択します。
    この設定では、Web サイトが実行されるデータ センターを指定します。

5. **[Web サイトの作成]** 矢印をクリックします。

 ![新しい Web サイトの作成][新しい Web サイトの作成]

 管理ポータルが [Web サイト] ページに戻り、**[状態]** 列にサイトが作成中であることが示されます。しばらくすると (通常は 1 分未満)、サイトの作成に成功したことが **[状態]** 列に示されます。左側にあるナビゲーション バーに、アカウントで所有するサイトの数が **[Web サイト]** アイコンの横に表示されます。

 ![Web サイト作成後の管理ポータルの Web サイト ページ][Web サイト作成後の管理ポータルの Web サイト ページ]

## <a name="createmvc4app"></a><span class="short-header">アプリの作成</span>ASP.NET MVC 4 アプリケーションを作成する

Azure の Web サイトを作成しましたが、まだその中にコンテンツがありません。次の手順では、Azure に発行する Visual Studio Web アプリケーション プロジェクトを作成します。

### プロジェクトを作成する

1.  Visual Studio 2012 または Visual Studio 2012 for Web Express を起動します。

2.  **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

 ![ファイル メニューの 新しいプロジェクト][ファイル メニューの 新しいプロジェクト]

3.  **[新しいプロジェクト]** ダイアログ ボックスで、**[インストールされているテンプレート]** の **[C\#]** を展開して **[Web]** を選択し、**[ASP.NET MVC 4 Web アプリケーション]** を選択します。

4.  ターゲット フレームワークとして **[.NET Framework 4.5]** が選択されていることを確認します。

5.  アプリケーションに「**MyExample**」という名前を付けて、**[OK]** をクリックします。

 ![新しいプロジェクト ダイアログ ボックス][新しいプロジェクト ダイアログ ボックス]

6.  **[新しい ASP.NET MVC 4 プロジェクト]** ダイアログ ボックスで、**[インターネット アプリケーション]** テンプレートを選択し、**[OK]** をクリックします。

 ![新しい ASP.NET MVC 4 プロジェクト ダイアログ ボックス][新しい ASP.NET MVC 4 プロジェクト ダイアログ ボックス]

### ローカルでアプリケーションを実行する

1. **Ctrl** キーを押しながら **F5** キーを押してアプリケーションを実行します。アプリケーションのホーム ページが既定のブラウザーに表示されます。

 ![ローカルで動作中の Web サイト][ローカルで動作中の Web サイト]

 これで、Azure にデプロイする単純なアプリケーションを作成するために必要な操作が完了しました。

## <a name="deploytowindowsazure"></a><span class="short-header">アプリケーションのデプロイ</span> Azure にアプリケーションをデプロイする

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

    ![プロジェクトのコンテキスト メニューの 発行][プロジェクトのコンテキスト メニューの 発行]

 **Web の発行**ウィザードが開きます。

2.  **Web の発行**ウィザードの **[プロファイル]** タブで、**[インポート]** をクリックします。

    ![発行設定のインポート][発行設定のインポート]

 **[発行プロファイルのインポート]** ダイアログ ボックスが表示されます。

3.  以前に Visual Studio で Azure サブスクリプションを追加していない場合は、次の手順に従います。これらの手順で、**[Azure の Web サイトからインポート]** のドロップダウン リストに Web サイトが含まれるようにサブスクリプションを追加します。

    -   **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure の Web サイトからインポート]**、**[Azure サブスクリプションの追加]** の順にクリックします。

    ![add Azure subscription][add Azure subscription]

    -   **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[サブスクリプション ファイルのダウンロード]** をクリックします。

    ![サブスクリプション ファイルのダウンロード][サブスクリプション ファイルのダウンロード]

    -   ブラウザー ウィンドウで、*.publishsettings* ファイルを保存します。

    ![download .publishsettings file][download .publishsettings file]

    -   **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[参照]** をクリックし、*.publishsettings* ファイルに移動します。

    ![サブスクリプションのダウンロード][サブスクリプションのダウンロード]

    -   **[インポート]** をクリックします。

    ![インポート][インポート]

    > [WACOM.NOTE]
    >  .publishsettings ファイルには、Azure のサブスクリプションとサービスの管理に使用される資格情報 (デコード済み) が保存されています。このファイルのセキュリティに関するベスト プラクティスは、このファイルをソース ディレクトリの外 (Libraries\\Documents フォルダーなど) に一時的に保存し、インポートが完了したらそのファイルを削除することです。悪意のあるユーザーが .publishsettings ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。

2.  **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure の Web サイトからインポート]** をクリックし、ドロップダウン リストから Web サイトを選択して **[OK]** をクリックします。

    ![発行プロファイルのインポート][発行プロファイルのインポート]

3.  **[接続]** タブの **[接続の検証]** をクリックして、設定が正しいことを確認します。

    ![接続の検証][接続の検証]

4.  接続が検証されると、**[接続の検証]** ボタンの横に緑色のチェック マークが表示されます。**[次へ]** をクリックします。

    ![検証が成功した接続][検証が成功した接続]

5.  **[設定]** タブで、**[この接続文字列を実行時に使用する]** チェック ボックスをオフにします。このアプリケーションではデータベースを使用しないためです。このページでは、残りの項目については既定の設定をそのまま使用できます。リリース ビルド構成をデプロイしているため、デプロイ先サーバーでのファイルの削除、アプリケーションのプリコンパイル、または App\_Data フォルダーでのファイルの除外は必要ありません。
    **[次へ]** をクリックします。

    ![設定 タブ][設定 タブ]

6.  **[プレビュー]** タブで、**[プレビューの開始]** をクリックします。

    ![プレビュー タブの プレビューの開始 ボタン][プレビュー タブの プレビューの開始 ボタン]

    このタブに、サーバーにコピーされるファイルの一覧が表示されます。プレビューの表示は、アプリケーションの発行に必要ではありませんが、知っておくと便利な機能です。この場合、表示されるファイルの一覧で操作を行う必要はありません。

    ![StartPreview ファイルの出力][StartPreview ファイルの出力]

7.  **[発行]** をクリックします。Azure サーバーにファイルをコピーする処理が開始されます。

8.  **出力**ウィンドウでは、実行された展開操作が表示され、展開が問題なく完了したことが報告されます。

    ![展開が正常に完了したことを示す出力ウィンドウ][展開が正常に完了したことを示す出力ウィンドウ]

9.  デプロイメントが正常に完了すると、既定のブラウザーがデプロイされた Web サイトの URL を自動的に開きます。
    これで、作成したアプリケーションはクラウドで実行されています。

    ![Azure で実行されている Web サイト][Web サイトの例]

## <a name="nextsteps"></a><span class="short-header">次の手順</span>次の手順

このチュートリアルでは、Azure の Web サイトに単純な Web アプリケーションをデプロイする方法について説明しました。サイトの管理、規模設定、およびトラブルシューティングの方法、データベース、認証、および承認機能の追加方法、アプリケーションを Azure の Web サイトではなく Azure のクラウド サービスで実行するかどうかの決定方法を示す、その他のリソースを利用できます。

### Web サイトの管理方法

サイトの処理を終了したら、サイトを削除することも、一時的にオフラインにすることも、サイトの設定を変更することもできます。これらの機能の一部は、Visual Studio の**サーバー エクスプローラー**から実行できます。

![サーバー エクスプローラーでの Azure の Web サイト][サーバー エクスプローラーでの Azure の Web サイト]

![Visual Studio での Web サイトの構成][Visual Studio での Web サイトの構成]

Web サイトを削除するには、Azure の管理ポータルを使用できます。次のスクリーン ショットは、管理ポータルの **[ダッシュボード]** タブの **[停止]**、**[再起動]**、および **[削除]** ボタンを示しています。

![管理ポータルの ダッシュボード タブ][管理ポータルの ダッシュボード タブ]

サイトの設定は **[構成]** タブで変更できます。詳細については、「[Web サイトの管理方法][Web サイトの管理方法]」を参照してください。

### Web サイトの規模の変更方法

サイトがパブリックの場合、トラフィックが増えると応答時間が遅くなることがあります。これを改善するために、管理ポータルの **[スケール]** タブでサーバー リソースを追加できます。

![管理ポータルの 規模の設定 タブ][管理ポータルの 規模の設定 タブ]

詳細については、「[Web サイトの規模変更方法][Web サイトの規模変更方法]」を参照してください。(サーバー リソースを追加して Web サイトの規模を変更することは無料ではありません。)

### Web サイトのトラブルシューティングの方法

トラブルシューティングのためにトレースまたはログの出力を参照することもできます。Visual Studio には、リアルタイムで生成される Azure ログの表示を容易にする組み込みツールが用意されています。

![Visual Studio のログ][Visual Studio のログ]

詳細については、「[Visual Studio での Azure の Web Sites のトラブルシューティング][Visual Studio での Azure の Web Sites のトラブルシューティング]」を参照してください。

### データベースと承認の機能を追加する方法

ほとんどの運用環境の Web サイトでは、データベースを使用し、一部のサイトの機能を特定の許可されたユーザーだけに制限します。データベース アクセス、認証、および承認の基本的な処理方法を示すチュートリアルについては、「[メンバーシップ、OAuth、SQL Database を使用した安全な ASP.NET MVC アプリケーションを Azure の Web Site に展開する][メンバーシップ、OAuth、SQL Database を使用した安全な ASP.NET MVC アプリケーションを Azure の Web Site に展開する]」を参照してください。

### アプリケーションをクラウド サービスで実行するかどうかを決定する方法

一部のシナリオでは、Azure の Web サイトではなく、Azure のクラウド サービスでアプリケーションを実行することもできます。詳細については、「[Azure 実行モデル][Azure 実行モデル]」と「[Azure の Web サイト、クラウド サービス、および仮想マシンの比較][Azure の Web サイト、クラウド サービス、および仮想マシンの比較]」を参照してください。多層 ASP.NET Web アプリケーションを作成してクラウド サービスに展開する方法を説明したチュートリアルについては、「[ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション][ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション]」を参照してください。

[Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
[Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
[このチュートリアルの新しいバージョン]: /en-us/develop/net/tutorials/get-started/
[Web サイトの例]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png
[MSDN サブスクライバーの特典を有効にする]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
[無料評価版にサインアップ]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
[開発環境を設定する]: #setupdevenv
[Azure に Web サイトを作成する]: #setupwindowsazure
[ASP.NET MVC 4 アプリケーションを作成する]: #createmvc4app
[Azure にアプリケーションを展開する]: #deploytowindowsazure
[次のステップ]: #nextsteps
[install-sdk-2012-only]: ../includes/install-sdk-2012-only.md
[Azure の管理ポータル]: http://manage.windowsazure.com
[新しい Web サイト]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
[簡易作成]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
[新しい Web サイトの作成]: ./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png
[Web サイト作成後の管理ポータルの Web サイト ページ]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png
[ファイル メニューの 新しいプロジェクト]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
[新しいプロジェクト ダイアログ ボックス]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
[新しい ASP.NET MVC 4 プロジェクト ダイアログ ボックス]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
[ローカルで動作中の Web サイト]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
[プロジェクトのコンテキスト メニューの 発行]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
[発行設定のインポート]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
[add Azure subscription]: ./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png
[サブスクリプション ファイルのダウンロード]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png
[download .publishsettings file]: ./media/web-sites-dotnet-get-started-vs2012/rzDown2.png
[サブスクリプションのダウンロード]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png
[インポート]: ./media/web-sites-dotnet-get-started-vs2012/rzImp.png
[発行プロファイルのインポート]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
[接続の検証]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
[検証が成功した接続]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png
[設定 タブ]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
[プレビュー タブの プレビューの開始 ボタン]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
[StartPreview ファイルの出力]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png
[展開が正常に完了したことを示す出力ウィンドウ]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
[サーバー エクスプローラーでの Azure の Web サイト]: ./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png
[Visual Studio での Web サイトの構成]: ./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png
[管理ポータルの ダッシュボード タブ]: ./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png
[Web サイトの管理方法]: /en-us/manage/services/web-sites/how-to-manage-websites/
[管理ポータルの 規模の設定 タブ]: ./media/web-sites-dotnet-get-started-vs2012/MPScale.png
[Web サイトの規模変更方法]: /en-us/manage/services/web-sites/how-to-scale-websites/
[Visual Studio のログ]: ./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png
[Visual Studio での Azure の Web Sites のトラブルシューティング]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
[メンバーシップ、OAuth、SQL Database を使用した安全な ASP.NET MVC アプリケーションを Azure の Web Site に展開する]: /en-us/develop/net/tutorials/web-site-with-sql-database/
[Azure 実行モデル]: /en-us/develop/net/fundamentals/compute/
[Azure の Web サイト、クラウド サービス、および仮想マシンの比較]: http://azure.microsoft.com/ja-jp/documentation/articles/choose-web-site-cloud-service-vm/
[ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/