<properties
	pageTitle="Azure アプリ サービスでの ASP.NET Web アプリの作成"
	description="このチュートリアルでは、Visual Studio 2013 で ASP.NET Web プロジェクトを作成したり、それを Azure アプリ サービスの Web アプリにデプロイしたりする方法について説明します。15 分以内に、クラウドでアプリケーションを稼働状態にできます。"
	services="app-service\web"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="06/26/2015"
	ms.author="tdykstra"/>

# Azure アプリ サービスでの ASP.NET Web アプリの作成

## 概要

このチュートリアルでは、Visual Studio 2013 または Visual Studio 2013 for Web Express を使用して、ASP.NET Web アプリケーションを作成し、[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする方法を示します。このチュートリアルは、Azure または ASP.NET を使用した経験がない読者を対象に作成されています。このチュートリアルでは、クラウドで動作する単純な Web アプリケーションを作成します。

学習内容:

* Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
* Visual Studio の ASP.NET Web プロジェクトを作成して Azure Web アプリにデプロイする方法
* Web プロジェクトを変更して、アプリケーションを再デプロイする方法
* [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用して Web アプリを監視および管理する方法

このチュートリアルを完了するには、Azure アカウントが必要です。

* [無料で Azure アカウントを開く](/pricing/free-trial/?WT.mc_id=A261C142F)ことができます - Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、App Service Web Apps など無料の Azure サービスをご利用になれます。
* [MSDN サブスクライバーの特典を有効にする](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます - MSDN サブスクリプションにより、有料の Azure サービスを使用できるクレジットが毎月提供されます。</li>

次の図に、完成したアプリケーションを示します。

![Web アプリのホーム ページ](./media/web-sites-dotnet-get-started/deployedandazure.png)

##<a name="video"></a>Microsoft Azure にサインアップする (ビデオ)

このビデオでは、Scott Hanselman が Microsoft Azure の無料評価版に簡単にサインアップできることを説明します。(時間: 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

## ASP.NET Web アプリケーションを作成する

最初の手順は、Web アプリケーション プロジェクトを作成することです。Visual Studio は後でプロジェクトをデプロイする Azure Web アプリを自動的に作成します。

1. Visual Studio 2013 または Visual Studio 2013 Express for Web を開きます。

2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

3. **[新しいプロジェクト]** ダイアログ ボックスで、**[C#]** > **[Web]** > **[ASP.NET Web アプリケーション]** の順にクリックします。必要に応じて、**[Visual Basic]** を選択することもできます。

3. ターゲット フレームワークとして **.NET Framework 4.5** が選択されていることを確認します。

4.  [Application Insights](app-insights-overview.md) は、Web アプリの可用性、パフォーマンス、および利用状況を監視します。Application Insights を使わない場合は、**[Application Insights をプロジェクトに追加する]** チェック ボックスをオフにします。

4. アプリケーションに「**MyExample**」という名前を付けて、**[OK]** をクリックします。

	![New Project dialog box](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択します。ASP.NET Web フォームを使用する場合は、**[Web フォーム]** テンプレートを選択できます。

	[MVC と Web フォーム](http://www.asp.net/get-started/websites)は Web アプリを作成するための ASP.NET のフレームワークです。このチュートリアルでは、いずれかを選択できますが、Web フォームを選択した場合は、後で *Default.aspx* を編集する必要があります。その場合 *Index.cshtml* を編集するように指示されます。

7. **[認証の変更]** をクリックします。

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. **[認証の変更]** ダイアログ ボックスで、**[認証なし]** をクリックし、**[OK]** をクリックします。

	![[認証なし]](./media/web-sites-dotnet-get-started/GS13noauth.png)

	作成しているサンプル アプリケーションでは、ユーザーはログインできません。「[次のステップ](#next-steps)」セクションは、認証と承認を実装するチュートリアルにリンクしています。

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[Microsoft Azure]** の設定はそのままにして、**[OK]** をクリックします。

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

	既定の設定は、Visual Studio が Web プロジェクトのための Azure Web アプリを作成することを指定しますチュートリアルの次のセクションでは、Web プロジェクトを新しく作成した Web アプリにデプロイします。

5. Azure にまだサインインしていない場合は、サインインを求めるメッセージが表示されます。Azure サブスクリプションを管理するために使用するアカウントの ID とパスワードを使用してサインインします。

	サインインすると、**[Microsoft Azure Web アプリの設定を構成する]** ダイアログ ボックスが表示され、作成するリソースが求められます。

	![Signed in to Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

3. **[Microsoft Azure Web アプリの設定を構成する]** ダイアログ ボックスで、既定値をそのまま使用します。

	**[Web アプリ名]** には、必要に応じて別の名前を入力できますが、名前は *azurewebsites.net* ドメイン内で一意である必要があります。Visual Studio で用意される既定の名前は一意です。

	Azure では、この名前がアプリケーションの URL のプレフィックスとして使用されます。URL 全体は、この名前に *.azurewebsites.net* を追加して構成されます (**[Web アプリ名]** ボックスの横を参照)。たとえば、名前が `MyExample6442` である場合、URL は `MyExample6442.azurewebsites.net` です。URL は一意であることが必要です。入力した URL が既に使用されていると、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別の名前を入力する必要があります。

4. **[App Service プラン]** ボックスの一覧で、**[新しい App Service プランの作成]** を選択します。

	このチュートリアルの最後にある「[次のステップ](#next-steps)」セクションには、App Service プランに関する情報へのリンクがあります。

5. プラン名には、「*MyExamplePlan*」、または必要に応じて別の名前を入力します。

6. **[リソース グループ]** ボックスの一覧で、**[新規リソース グループの作成]** を選択します。

	「[次のステップ](#next-steps)」セクションには、リソース グループに関する情報へのリンクがあります。

5. リソース グループ名には、「*MyExampleResourceGroup*」、または必要に応じて別の名前を入力します。

5. **[リージョン]** ドロップダウン リストで、最も近い場所を選択します。

	この設定によって、Web アプリが実行される Azure データ センターが指定されます。このチュートリアルでは、任意のリージョンを選択でき、選択による違いはありません。ただし、運用 Web アプリでは、サイトにアクセスするブラウザーに最も近い Web サーバーを選択して[待機時間](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)を最小にします。

5. データベース フィールドは変更せずそのままにします。

	このチュートリアルでは、データベースは使用しません。このチュートリアルの最後にある「[次のステップ](#next-steps)」セクションには、データベースの使用方法を示したチュートリアルへのリンクがあります。

6. **[OK]** をクリックします。

	![Signed in to Azure](./media/web-sites-dotnet-get-started/configuresitesettings2.png)

	数秒で、Visual Studio により、指定したフォルダーに Web プロジェクトが作成され、指定した Azure リージョンに Web アプリが作成されます。

	**[ソリューション エクスプローラー]** ウィンドウには、新しいプロジェクトのファイルとフォルダーが表示されます

	![Solution Explorer](./media/web-sites-dotnet-get-started/solutionexplorer.png)

	**[Azure App Service アクティビティ]** ウィンドウには、Web アプリが作成されたことが表示されます。

	![Web アプリが作成されました](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

	サーバー エクスプローラーに Web アプリが表示されます。

	![Web アプリが作成されました](./media/web-sites-dotnet-get-started/siteinse.png)

## Azure にアプリケーションを展開する

7. **[Azure App Service アクティビティ]** ウィンドウで、**[今すぐ MyExample をこの Web アプリに発行する]** をクリックします。

	![Web アプリが作成されました](./media/web-sites-dotnet-get-started/GS13sitecreated.png)

	数秒で、**Web の発行**ウィザードが表示されます。

	Visual Studio がプロジェクトを Azure にデプロイするために必要な設定が、*発行プロファイル*に保存されます。このウィザードではこれらの設定を確認し、変更できます。

8. **Web の発行**ウィザードの **[接続]** タブで、**[接続の検証]** をクリックして、Web プロジェクトをデプロイするために Visual Studio が Azure に接続できることを確認します。

	![Validate connection](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

	接続が検証されると、**[接続の検証]** ボタンの横に緑色のチェック マークが表示されます。

9. **[次へ]** をクリックします。

	![Successfully validated connection](./media/web-sites-dotnet-get-started/GS13ValidateConnectionSuccess.png)

10. **[設定]** タブの **[次へ]** をクリックします。

	![Settings tab](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

	**[構成]** および **[ファイル発行オプション]** には既定値を使用できます。

	**[構成]** ドロップダウンでは、リモート デバッグ用のデバック ビルドをデプロイできます。「[次のステップ](#next-steps)」セクションは、リモートのデバッグ モードで Visual Studio を実行する方法を示したチュートリアルにリンクしています。

	**[ファイル発行オプション]** を展開すると、このチュートリアルでは使用していないシナリオを使用できる複数の設定が表示されます。

	* デプロイ先で追加ファイルを削除する。

		プロジェクトに存在しないファイルをサーバーから削除します。以前に別のプロジェクトをデプロイした Web アプリにプロジェクトをデプロイした場合に、削除が必要になります。

	* 発行中にプリコンパイルする。

		大規模なアプリケーションに対して初回要求の準備時間を短くできます。

	* App_Data フォルダーからファイルを除外する。

		テストでは、運用環境にはデプロイしない App_Data の SQL Server データベース ファイルを使用する場合があります。

11. **[プレビュー]** タブで、**[プレビューの開始]** をクリックします。

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started/GS13Preview.png)

	このタブに、サーバーにコピーされるファイルの一覧が表示されます。プレビューの表示は、アプリケーションの発行に必要ではありませんが、知っておくと便利な機能です。

12. **[発行]** をクリックします。

	![StartPreview file output](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	Azure サーバーにファイルをコピーする処理が開始されます。

	**[出力]** ウィンドウと **[Web 発行アクティビティ]** ウィンドウでは、実行された展開操作が表示され、展開が問題なく完了したことが報告されます。

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started/PublishOutput.png)

	デプロイメントが成功すると、自動的に既定のブラウザーが開き、デプロイ先の Web アプリの URL にアクセスします。これで、作成したアプリケーションはクラウドで実行されています。ブラウザー アドレス バーの URL は、インターネットから読み込まれている Web アプリを示します。

	![Azure で実行されている Web アプリ](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

13. ブラウザーを閉じます。

## 変更を加えて、再展開する

チュートリアルのこのセクションでは、ホーム ページの **h1** 見出しを変更し、開発用コンピューターでローカルでプロジェクトを実行して変更を確認して、その変更を Azure にデプロイします。

2. *ソリューション エクスプローラー*の *Views/Home/Index.cshtml* ファイルまたは **.vbhtml** ファイルを開き、**h1** 見出しを「ASP.NET」から「ASP.NET および Azure」に変更して、ファイルを保存します。

	![MVC index.cshtml](./media/web-sites-dotnet-get-started/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started/mvcandazure.png)

1. Ctrl + F5 キーを押してローカル コンピューターで Web アプリを実行し、更新した見出しを確認します。

	![ローカルで実行されている Web アプリ](./media/web-sites-dotnet-get-started/localandazure.png)

	`http://localhost` URL には、ローカル コンピューターで実行中であることが表示されます。既定では、IIS Express で実行されます。これは、Web アプリケーションの開発での使用を想定した、IIS の軽量バージョンです。

1. ブラウザーを閉じます。

1. **[ソリューション エクスプローラー]** でプロジェクトを右クリックし、**[発行]** を選択します。

	![Chooose Publish](./media/web-sites-dotnet-get-started/choosepublish.png)

	**Web の発行**ウィザードの [プレビュー] タブが表示されます。発行設定を変更する必要がある場合、異なるタブを選択できますが、今は同じ設定で再展開します。

2. **Web の発行**ウィザードで、**[発行]** をクリックします。

	![Click Publish](./media/web-sites-dotnet-get-started/clickpublish.png)

	Visual Studio ではプロジェクトが Azure に展開され、既定のブラウザーで Web アプリが開きます。

	![変更された Web アプリがデプロイされました](./media/web-sites-dotnet-get-started/deployedandazure.png)

**ヒント:** 簡易デプロイメントの場合も、**[Web の 1 クリック発行]** ツール バーを有効にできます。**[表示]** > **[ツール バー]** をクリックして、**[Web の 1 クリック発行]** を選択します。このツール バーによって、プロファイルを選択し、ボタンをクリックして発行するか、またはボタンをクリックして **Web の発行**ウィザードを開くことができます。

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## 管理ポータルで Web アプリを監視および管理する

[Azure の管理ポータル](/services/management-portal/)は、作成した Web アプリなどの Azure のサービスを管理および監視できる Web インターフェイスです。チュートリアルのこのセクションでは、ポータルで何ができるかを確認できます。

1. ブラウザーで、[https://portal.azure.com]() に移動し、Azure 資格情報でサインインします。

2. **[参照]、[Web Apps]** をクリックした後、Web アプリの名前をクリックします。

	Web アプリの **[Web アプリ]** ブレードには、使用統計情報の概要や、よく使用される Web アプリ管理機能のリンクが表示されます。

	![Web アプリのブレード](./media/web-sites-dotnet-get-started/portaldashboard.png)-->

	この時点では、Web アプリにはトラフィックがそれほど存在しないため、グラフには何も表示されない可能性があります。アプリケーションにアクセスし、何回かページを更新してから、ポータル ページを更新すると、統計が表示されます。

3. **[すべての設定]** をクリックすると、Web アプリの構成についてさらに多くのオプションが表示されます。

	設定のタイプの一覧が表示されます。

	![](./media/web-sites-dotnet-get-started/portalconfigure1.png)-->

4. **[アプリケーション設定]** をクリックすると、ポータルで構成可能な設定値の種類の例が表示されます。

	たとえば、Web アプリで使用される .NET のバージョンを制御したり、[WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) などの機能を有効にしたり、[接続文字列値](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)を設定したりできます。

	![ポータルの Web アプリの構成タブ](./media/web-sites-dotnet-get-started/portalconfigure2.png)

これらは、管理ポータル機能の一部にすぎません。新しい Web アプリを作成したり、既存の Web アプリを削除したり、Web アプリを停止して再起動したり、データベースや仮想マシンなど、その他の種類の Azure サービスを管理したりできます。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページをご覧ください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 次のステップ

このチュートリアルでは、Azure の Web アプリに単純な Web アプリケーションを作成して展開する方法について説明しました。詳細についての関連トピックとリソースがあります。

* Web プロジェクトをデプロイする他の方法

	このチュートリアルでは、Web アプリを作成して、オールインワン操作ですべてをデプロイする最も簡単な方法を説明しました。Visual Studio を使用した、または[ソース管理システム](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)からの[デプロイメントの自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)による他のデプロイ方法の概要については、[Azure Web アプリをデプロイする方法](web-sites-deploy.md)に関するページを参照してください。

	Visual Studio は、デプロイメントを自動化する Windows PowerShell スクリプトも生成します。詳細については、「[Automate Everything (Building Real-World Cloud Apps with Azure) (すべてを自動化 - Azure での実際のクラウド アプリケーションのビルド)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)」を参照してください。

* Visual Studio での Web アプリの管理方法

	**サーバー エクスプローラー**で実行できる Web アプリ管理機能については、[Visual Studio での Azure Web アプリのトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)に関するページを参照してください。

* Web アプリのトラブルシューティングの方法

	Visual Studio には、リアルタイムで生成される Azure ログの表示を容易にする機能があります。Azure では、リモートでデバッグ モードを使用して実行することもできます。詳細については、[Visual Studio での Azure Web アプリのトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)に関するページを参照してください。

* データベースと承認の機能を追加する方法

	データベースにアクセスして、一部のアプリケーション機能を許可されたユーザーのみに制限する方法を示すチュートリアルについては、[メンバーシップ、OAuth、および SQL Database によりセキュリティ保護された ASP.NET MVC アプリの Azure Web アプリへのデプロイ](/develop/net/tutorials/web-site-with-sql-database/)に関するページを参照してください。

* カスタム ドメイン名および SSL の追加方法

	SSL および独自のドメイン (contoso.azurewebsites.net の代わりに www.contoso.com など) の使用方法については、次のリソースを参照してください。

	* [Azure の Web サイトのカスタム ドメイン名の構成](web-sites-custom-domain-name.md)
	* [Azure の Web サイトでの HTTPS の有効化](web-sites-configure-ssl-certificate.md)

* アイドル時間終了後のウェイクアップの待機時間を回避する方法

	既定では、アイドル状態がしばらく続いている Web アプリはアンロードされます。アイドル状態後の初回要求は、Web アプリが再度読み込まれるまで待機する必要があります。この待機時間を回避するために、AlwaysOn 機能を有効にすることができます。詳細については、「[Web アプリを構成する方法](web-sites-configure.md)」の構成オプションを参照してください。

* リアルタイム機能 (チャットなど) の追加方法

	Web アプリでリアルタイム機能 (チャット サービス、ゲーム、株価情報など) を使用する場合は、[WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) の転送方法で [ASP.NET SignalR](http://www.asp.net/signalr) を使用すると、パフォーマンスが最大になります。詳細については、[Azure Web Apps での SignalR の使用](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites)に関するページを参照してください。

* Web アプリケーションに対する アプリ サービス、クラウド サービス、および VM の選択方法

	Azure では、このチュートリアルで示したように アプリ サービス Web アプリで、またはクラウド サービスや仮想マシンで、Web アプリケーションを実行することができます。詳細については、[Azure 実行モデル](/develop/net/fundamentals/compute/)に関するページと [Azure の Web アプリ、クラウド サービス、VM についていつ、どれを使用するか](/manage/services/web-sites/choose-web-app-service/)に関するページを参照してください。

* [App Service プランを選択または作成する方法](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

* [リソース グループを選択または作成する方法](../azure-preview-portal-using-resource-groups.md)



## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 以前のポータルから新しいポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。
 

<!---HONumber=July15_HO3-->