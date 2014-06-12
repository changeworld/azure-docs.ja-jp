<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Azure と ASP.NET を使ってみる" authors=""  solutions="" writer="tdykstra" manager="wpickett" editor="mollybos"  />



# Azure と ASP.NET を使ってみる

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/net/tutorials/get-started/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/ja-jp/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>注</strong><p><a href="/ja-jp/develop/net/tutorials/get-started/">このチュートリアルの新しいバージョン</a>が提供されています。Visual Studio 2012 を使用する場合はこのバージョンを引き続き利用できますが、最新の Azure SDK の機能の中には、取り上げられていないものもあります。</p></div>

このチュートリアルでは、Visual Studio 2012 または Visual Studio 2012 for Web Express の Web の発行ウィザードを使用して、ASP.NET Web アプリケーションを Azure の Web サイトに展開する方法を示します。必要に応じて、Visual Studio 2010 または Visual Web Developer Express 2010 を使用して、チュートリアルの手順に従うこともできます。

Azure アカウントは無料で開くことができます。まだ Visual Studio 2012 を持っていない場合は、SDK によって Visual Studio 2012 for Web Express が自動的にインストールされます。これで、Azure 向けの開発を完全に無料で始めることができます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、クラウドで動作する単純な Web アプリケーションを作成します。
 
学習内容: 

* Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
* Visual Studio の ASP.NET MVC 4 プロジェクトを作成して Azure の Web サイトに発行する方法

次の図に、完成したアプリケーションを示します。

![Web サイトの例][DeployedWebSite]

<div class="dev-callout"><p><strong>注</strong> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントを持っていない場合は、<a href="/ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN サブスクライバーの特典を有効にする</a>か、<a href="/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">無料評価版にサインアップ</a>してください。</p></div>
 
### チュートリアル セグメント

1. [開発環境を設定する][]
2. [Azure に Web サイトを作成する][]
3. [ASP.NET MVC 4 アプリケーションを作成する][]
4. [Azure にアプリケーションを展開する][]
5. [次のステップ][]

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

<h2><a name="setupwindowsazure"></a><span class="short-header">サイトの作成</span>Web サイトを作成する</h2>

次の手順では、Azure の Web サイトを作成します。

1. [Azure の管理ポータル][PreviewPortal]で、**[Web サイト]** をクリックし、**[新規]** をクリックします。

![新しい Web サイト][WebSiteNew]

2. **[簡易作成]** をクリックします。
	
	![簡易作成][ClickQuickCreate]

3. ウィザードの **[Web サイトの作成]** 手順で、アプリケーションの一意の URL として使用する文字列を **[URL]** ボックスに入力します。ここに入力した文字列と、このテキスト ボックスの右側に表示されている文字列を組み合わせたものが実際の URL になります。図には "**example1**" と表示されていますが、URL 用にその文字列が既に取得されている場合は、別の値を入力する必要があります。

4. **[リージョン]** ボックスの一覧で、現在の所在地に最も近いリージョンを選択します。
この設定によって、使用する Web サイトが実行されるデータ センターが指定されます。

5. **[Web サイトの作成]** の矢印をクリックします。

	![新しい Web サイトの作成](./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png)

	管理ポータルが [Web サイト] ページに戻り、**[状態]** 列にサイトが作成中であることが示されます。しばらくすると (通常は 1 分未満)、サイトの作成に成功したことが **[状態]** 列に示されます。左側にあるナビゲーション バーで、アカウントで所有するサイトの数が **[Web サイト]** アイコンの横に表示されます。
	
	![Web サイト作成後の管理ポータルの [Web サイト] ページ][WebSiteStatusRunning]

<h2><a name="createmvc4app"></a><span class="short-header">アプリの作成</span>ASP.NET MVC 4 アプリケーションを作成する</h2>

Azure の Web サイトを作成しましたが、まだその中にコンテンツがありません。次の手順では、Azure に発行する Visual Studio Web アプリケーション プロジェクトを作成します。

### プロジェクトを作成する

1. Visual Studio 2012 または Visual Studio 2012 for Web Express を起動します。

2. **[ファイル]** メニューの **[新規作成]** クリックし、**[プロジェクト]** をクリックします。

![[ファイル] メニューの [新しいプロジェクト]][NewVSProject]

3. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストールされているテンプレート]** の下にある [**C#**] を展開して **[Web]** を選択し、**[ASP.NET Web アプリケーション]** を選択します。

3. ターゲット フレームワークとして **[.NET Framework 4.5]** が選択されていることを確認します。

4. アプリケーションに **MyExample** という名前を付けて、**[OK]** をクリックします。
	
![[新しいプロジェクト] ダイアログ ボックス][NewMVC4WebApp]

5. **[新しい ASP.NET MVC 4 プロジェクト]** ダイアログ ボックスで、**[インターネット アプリケーション]** テンプレートを選択し、**[OK]** をクリックします。

![[新しい ASP.NET MVC 4 プロジェクト] ダイアログ ボックス][InternetAppTemplate]

### ローカルでアプリケーションを実行する

1. **Ctrl** キーを押しながら **F5** キーを押してアプリケーションを実行します。アプリケーションのホーム ページが既定のブラウザーに表示されます。

![ローカルで動作中の Web サイト][AppRunningLocally]

これで、Azure にデプロイする単純なアプリケーションを作成するために必要な操作が完了しました。

<h2><a name="deploytowindowsazure"></a><span class="short-header">アプリケーションの展開</span>Azure にアプリケーションを展開する</h2>

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![プロジェクトのコンテキスト メニューの [発行]][PublishVSSolution]

**Web の発行**ウィザードが開きます。

2. **Web の発行**ウィザードの **[プロファイル]** タブで、**[インポート]** をクリックします。

	![発行設定のインポート][ImportPublishSettings]

**[発行プロファイルのインポート]** ダイアログ ボックスが表示されます。

3. 以前に Visual Studio で Azure サブスクリプションを追加していない場合は、次の手順に従います。これらの手順で、**[Azure の Web サイトからインポート]** の下のドロップダウン リストに Web サイトが含まれるようにサブスクリプションを追加します。
    
	- **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure の Web サイトからインポート]**、**[Azure サブスクリプションの追加]** の順にクリックします。
    
	![add Azure subscription](./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png)
    
	- **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[サブスクリプション ファイルのダウンロード]** をクリックします。
    
	![サブスクリプション ファイルのダウンロード](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png)
    
	- ブラウザー ウィンドウで、*.publishsettings* ファイルを保存します。
    
	![.publishsettings ファイルのダウンロード](./media/web-sites-dotnet-get-started-vs2012/rzDown2.png)

	- **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[参照]** をクリックし、*.publishsettings* ファイルに移動します。

	![サブスクリプションのダウンロード](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png)

	- **[インポート]** をクリックします。
    
	![インポート](./media/web-sites-dotnet-get-started-vs2012/rzImp.png)

	> [WACOM.NOTE]
	> .publishsettings ファイルには、Azure のサブスクリプションとサービスの管理に使用される資格情報 (デコード済み) が保存されています。このファイルのセキュリティに関するベスト プラクティスは、このファイルをソース ディレクトリの外 (Libraries\Documents フォルダーなど) に一時的に保存し、インポートが完了したらそのファイルを削除することです。これは、悪意のあるユーザーが .publishsettings ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。

4. **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure の Web サイトからインポート]** をクリックし、ドロップダウン リストから Web サイトを選択して **[OK]** をクリックします。

	![発行プロファイルのインポート][ImportPublishProfile]

5. **[接続]** タブの **[接続の検証]** をクリックし、設定が正しいことを確認します。

	![接続の検証][ValidateConnection]

6. 接続が検証されると、**[接続の検証]** ボタンの横に緑色のチェック マークが表示されます。**[次へ]** をクリックします。

	![検証が成功した接続][ValidateConnectionSuccess]

7. **[設定]** タブで、**[この接続文字列を実行時に使用する]** チェック ボックスをオフにします。このアプリケーションではデータベースを使用しないためです。このページでは、残りの項目については既定の設定をそのまま使用できます。リリース ビルド構成を展開しているため、展開先サーバーでファイルを削除したり、アプリケーションをプリコンパイルしたり、App_Data フォルダーでファイルを除外したりする必要はありません。
**[次へ]** をクリックします。

	![[設定] タブ][PublishWebSettingsTab]

8. **[プレビュー]** タブで、**[プレビューの開始]** をクリックします。

	![[プレビュー] タブの [プレビューの開始] ボタン][PublishWebStartPreview]

	このタブに、サーバーにコピーされるファイルの一覧が表示されます。プレビューの表示は、アプリケーションの発行に必要ではありませんが、知っておくと便利な機能です。この場合、表示されるファイルの一覧で操作を行う必要はありません。

	![StartPreview ファイルの出力][PublishWebStartPreviewOutput]

9. **[発行]** をクリックします。Azure サーバーにファイルをコピーする処理が開始されます。

10. **出力**ウィンドウでは、実行された展開操作が表示され、展開が問題なく完了したことが報告されます。

	![デプロイが正常に完了したことを示す出力ウィンドウ][PublishOutput]

11. 展開が成功すると、自動的に既定のブラウザーが開き、展開先の Web サイトの URL にアクセスします。
これで、作成したアプリケーションはクラウドで実行されています。

	![Azure で実行されている Web サイト][DeployedWebSite]

<h2><a name="nextsteps"></a><span class="short-header">次のステップ</span>次のステップ</h2>

このチュートリアルでは、Azure の Web サイトに単純な Web アプリケーションを展開する方法について説明しました。サイトを管理、拡張、およびトラブルシューティングする方法、データベース、認証、および承認機能を追加する方法、アプリケーションを Azure の Web サイトではなく Azure のクラウド サービスで実行するかどうかを決定する方法を示すその他のリソースを利用できます。

<h3>Web サイトの管理方法</h3>
サイトの処理を終了したら、サイトを削除することも、一時的にオフラインにすることも、サイトの設定を変更することもできます。これらの機能の一部は、Visual Studio の**サーバー エクスプローラー**から実行できます。

![サーバー エクスプローラーでの Azure の Web サイト](./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png)

![Visual Studio での Web サイトの構成](./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png)

Web サイトを削除するには、Azure の管理ポータルを使用できます。次のスクリーン ショットは、管理ポータルの **[ダッシュボード]** タブの **[停止]**、**[再起動]**、および **[削除]** ボタンを示しています。

![管理ポータルの [ダッシュボード] タブ](./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png)

サイトの設定は **[構成]** タブで変更できます。詳細については、「[Web サイトの管理方法](/ja-jp/manage/services/web-sites/how-to-manage-websites/)」を参照してください。

<h3>Web サイトの規模変更方法</h3>
サイトがパブリックの場合、トラフィックが増えると応答時間が遅くなることがあります。これを改善するために、管理ポータルの **[スケール]** タブでサーバー リソースを追加できます。

![管理ポータルの [規模の設定] タブ](./media/web-sites-dotnet-get-started-vs2012/MPScale.png)

詳細については、「[Web サイトの規模変更方法](/ja-jp/manage/services/web-sites/how-to-scale-websites/)」を参照してください。(サーバー リソースを追加して Web サイトの規模を変更することは無料ではありません。)

<h3>Web サイトのトラブルシューティングの方法</h3>
トラブルシューティングのためにトレースまたはログの出力を参照することもできます。Visual Studio には、リアルタイムで生成される Azure ログの表示を容易にする組み込みツールが用意されています。

![Visual Studio のログ](./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png)

詳細については、「[Visual Studio での Azure の Web サイトのトラブルシューティング](/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)」を参照してください。

<h3>データベースと承認の機能を追加する方法</h3>
ほとんどの運用環境の Web サイトでは、データベースを使用し、一部のサイトの機能を特定の許可されたユーザーだけに制限します。データベース アクセス、認証、および承認の基本的な処理方法を示すチュートリアルについては、「[メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトに展開する][WebWithSQL]」を参照してください。

<h3>アプリケーションをクラウド サービスで実行するかどうかを決定する方法</h3>
一部のシナリオでは、Azure の Web サイトではなく、Azure のクラウド サービスでアプリケーションを実行することもできます。詳細については、「Azure 実行モデル」、「[Azure Execution Models (Azure 実行モデル)](/ja-jp/develop/net/fundamentals/compute/)」、「[クラウド サービス、仮想マシン: いつ、どれを使用するか](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj218759.aspx)」を参照してください。多層 ASP.NET Web アプリケーションを作成してクラウド サービスに展開する方法を説明したチュートリアルについては、「[ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション](/ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/)」を参照してください。

[開発環境を設定する]: #setupdevenv
[Azure に Web サイトを作成する]: #setupwindowsazure
[ASP.NET MVC 4 アプリケーションを作成する]: #createmvc4app
[Azure にアプリケーションを展開する]: #deploytowindowsazure
[次のステップ]: #nextsteps
[PreviewPortal]: http://manage.windowsazure.com



[WebWithSQL]: /ja-jp/develop/net/tutorials/web-site-with-sql-database/

[AppRunningLocally]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
[ClickQuickCreate]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
[DeployedWebSite]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png

[ImportPublishSettings]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
[InternetAppTemplate]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
[NewMVC4WebApp]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
[NewVSProject]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
[PublishOutput]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
[PublishVSSolution]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
[PublishWebSettingsTab]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
[PublishWebStartPreview]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
[PublishWebStartPreviewOutput]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png

[ValidateConnection]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
[ValidateConnectionSuccess]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png

[WebSiteNew]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
[WebSiteStatusRunning]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png

