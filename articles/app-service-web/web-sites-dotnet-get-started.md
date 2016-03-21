<properties
	pageTitle="Azure App Service での ASP.NET Web アプリの作成 | Microsoft Azure"
	description="このチュートリアルでは、Visual Studio 2013 で ASP.NET Web プロジェクトを作成したり、それを Azure App Service の Web アプリにデプロイしたりする方法について説明します。"
	services="app-service\web"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/02/2015"
	ms.author="tdykstra"/>

# Azure App Service での ASP.NET Web アプリの作成

> [AZURE.SELECTOR]
- [.NET](web-sites-dotnet-get-started.md)
- [Node.JS](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

## 概要

このチュートリアルでは、Visual Studio 2015 を使用して ASP.NET Web アプリケーションを [Azure App Service の Web アプリ](app-service-web-overview.md)にデプロイする方法を紹介します。このチュートリアルは、Azure を使用した経験がない ASP.NET 開発者を対象に作成されています。このチュートリアルでは、クラウドで動作する単純な Web アプリケーションを作成します。

次の図に、完成したアプリケーションを示します。

![Web app running in Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

学習内容:

* Visual Studio で新しい Web プロジェクトを作成するときに新しい App Service Web アプリを作成する方法。
* Visual Studio を使用して、App Service Web アプリに WEB プロジェクトをデプロイする方法。
* [Azure ポータル](/overview/preview-portal/)を使用して Web アプリを監視および管理する方法

このチュートリアルの最後の「[次のステップ](#next-steps)」セクションに、Azure App Service の使い方をさらに学習できる他のチュートリアルへのリンク一覧があります。

> [AZURE.NOTE] このチュートリアルの範囲と方針の向上にご協力ください。ここに記載されているトピック以外に入門用のチュートリアルとして扱ってほしいトピックがありましたら、このチュートリアルの最後にある [Disqus コメント欄](#comments)でご意見をお寄せください。

[AZURE.INCLUDE [前提条件](../../includes/app-service-web-dotnet-get-started-prereqs.md)]

## プロジェクトと Web アプリケーションを作成する

最初の手順では、Visual Studio で Web プロジェクトを作成し、Azure App Service で Web アプリケーションを作成します。作成が完了したら、インターネットで Web アプリを公開できるように、そのプロジェクトを Web アプリにデプロイします。

次の図では、作成とデプロイの手順の内容を示しています。

![Create and deploy](./media/web-sites-dotnet-get-started/Create_App.png)

1. Visual Studio 2015 を開きます。

2. **[ファイル]、[新規作成]、[プロジェクト]** の順にクリックします。

3. **[新しいプロジェクト]** ダイアログ ボックスで、**[Visual C#]、[Web]、[ASP.NET Web アプリケーション]** の順にクリックします。(好みに応じて、**[Visual Basic]** を選択してもかまいません。)

3. ターゲット フレームワークとして **[.NET Framework 4.5.2]** が選択されていることを確認します。

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) は、Web アプリの可用性、パフォーマンス、利用状況を監視します。**[Application Insights をプロジェクトに追加する]** チェック ボックスは、Visual Studio のインストール後に初めて Web プロジェクトを作成するときは既定値であるオンになっています。このチェック ボックスがオンになっているが Application Insights を使う予定がない場合は、オフにしてください。

4. アプリケーションに「**MyExample**」という名前を付けます。

5. **[OK]** をクリックします。

	![New Project dialog box](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択します。

7. **[認証の変更]** をクリックします。

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. **[認証の変更]** ダイアログ ボックスで、**[認証なし]** をクリックし、**[OK]** をクリックします。

	![[認証なし]](./media/web-sites-dotnet-get-started/GS13noauth.png)

	このチュートリアルで作成するアプリケーションはごく単純なものであり、ユーザー ログインの機能はありません。

5. **[新しい ASP.NET プロジェクト]** の **[Microsoft Azure]** セクションで、**[クラウド内でホスト]** が選択されていることと、ドロップダウン リストで **[App Service]** が選択されていることを確認します。

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

	このように設定すると、Visual Studio によってこの Web プロジェクトの Azure Web アプリが自動的に作成されます。

6. **[OK]** をクリックします。

5. Azure にまだサインインしていない場合は、サインインを求めるメッセージが表示されます。Azure サブスクリプションを管理するために使用するアカウントの ID とパスワードを使用してサインインします。

	サインインすると、**[App Service の作成]** ダイアログ ボックスが表示され、作成するリソースの情報が求められます。

	![Signed in to Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

3. **[App Service の作成]** ダイアログ ボックスで、*azurewebsites.net* ドメインで一意となる **Web アプリの名前**を入力します。たとえば、MyExample という名前の右側に番号を付加して一意にすることができます (例: MyExample810)。既定の Web 名が作成される場合は、それが一意になるため、そのまま使用することができます。

	入力した名前が既に使用されている場合は、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別の名前を入力する必要があります。

	Azure では、この名前がアプリケーションの URL のプレフィックスとして使用されます。完全な URL は、この名前に *.azurewebsites.net* を追加したものとなります (**[Web アプリ名]** テキスト ボックスの横に表示されます)。たとえば、名前が `MyExample810` である場合、URL は `myexample810.azurewebsites.net` です。URL は一意であることが必要です。

6. **[リソース グループ]** ボックスの横の **[新規作成]** をクリックし、「MyExample」(またはその他の好みの名前) と入力します。

	このボックスでは、既存のリソース グループを選択することも、新規作成することもできます。作成するには、サブスクリプションの既存のリソース グループとは異なる名前を入力します。

	リソース グループは、API アプリ、データベース、VM など、一連の Azure リソースをひとまとめにしたものです。チュートリアルを行うときは、新しいリソース グループを作成することをお勧めします。このようにすれば、チュートリアルのために作成した Azure リソースをまとめて簡単に削除できるからです。詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。

4. **[App Service プラン]** ドロップダウンの隣にある **[新規]** ボタンをクリックします。

	![[App Service の作成] ダイアログ](./media/web-sites-dotnet-get-started/createas.png)

	スクリーン ショットの **[Web アプリ名]**、**[サブスクリプション]**、**[リソース グループ]** に指定されている値はサンプルです。実際の値を使用してください。

	以降の手順では、新しいリソース グループの App Service プランを作成します。App Service プランには、API アプリの実行環境となるコンピューティング リソースを指定します。たとえば、Free レベルを選択した場合、API アプリは共有 VM 上で実行され、一部の有料レベルを選択した場合は専用 VM で実行されます。詳細については、[App Service プランの概要に関するページ](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を参照してください。

5. **[App Service プランの構成]** ダイアログに「MyExamplePlan」(またはその他の好みの名前) と入力します。

5. **[場所]** ドロップダウン リストで、現在の所在地に最も近い場所を選択します。

	この設定によって、アプリが実行される Azure データ センターが指定されます。このチュートリアルでは、任意のリージョンを選択することができます。任意のリージョンを選択しても、大きな違いはありません。ただし、運用アプリでは、[待機時間](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)を最小限に抑えるために、アクセスするクライアントに最も近いサーバーを選択することをお勧めします。

5. **[サイズ]** ドロップダウンで、**[Free]** をクリックします。

	無料の価格レベルでこのチュートリアルに十分な性能が提供されます。

6. **[App Service プランの構成]** ダイアログで、**[OK]** をクリックします。

	![[App Service の構成] ダイアログ](./media/web-sites-dotnet-get-started/configasp.png)

7. **[App Service の作成]** ダイアログ ボックスで、**[作成]** をクリックします。

	![[App Service の作成] ダイアログ ボックス](./media/web-sites-dotnet-get-started/clickcreate.png)

	ごく短時間で (通常は 1 分未満)、Visual Studio によって Web プロジェクトと Web アプリが作成されます。

	**[ソリューション エクスプローラー]** ウィンドウには、新しいプロジェクトのファイルとフォルダーが表示されます

	![Solution Explorer](./media/web-sites-dotnet-get-started/solutionexplorer.png)

	**[Azure App Service アクティビティ]** ウィンドウには、Web アプリが作成されたことが表示されます。

	![Web app created](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

	この Web アプリは**クラウド エクスプローラー**で確認できます。

	![Web app created](./media/web-sites-dotnet-get-started/siteinse.png)

## Web アプリにプロジェクトをデプロイする

このセクションでは、図の手順 2 に示すように、Web プロジェクトを Web アプリにデプロイします。

![Create and deploy](./media/web-sites-dotnet-get-started/Create_App.png)

1. **[ソリューション エクスプローラー]** でプロジェクトを右クリックし、**[発行]** を選択します。

	![Choose Publish](./media/web-sites-dotnet-get-started/choosepublish.png)

	数秒で、**Web の発行**ウィザードが表示されます。このウィザードは、新しい Web アプリに Web プロジェクトをデプロイするための設定が含まれる*発行プロファイル*に対して開かれます。他の Web アプリにデプロイする場合は、**[プロファイル]** タブをクリックして別のプロファイルを作成します。このチュートリアルでは、以前に作成した Web アプリにデプロイする設定を使用します。

8. **Web の発行**ウィザードの **[接続]** タブで、**[次へ]** をクリックします。

	![Successfully validated connection](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

	次のタブは **[設定]** タブです。ここで、ビルド構成を変更して、デバッグ ビルドをデプロイするように設定すると、[リモート デバッグ](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)ができるようになります。このタブには他にも、次のようにさまざまな**ファイル発行オプション**があります。

	* 発行先の追加ファイルを削除する
	* 発行中にプリコンパイルする
	* App\_Data フォルダーのファイルを除外する

	このチュートリアルでは、いずれも必要ありません。これらの詳しい説明については、「[方法: Visual Studio でワンクリック発行を使用して Web アプリケーション プロジェクトを配置する](https://msdn.microsoft.com/library/dd465337.aspx)」を参照してください。

10. **[設定]** タブの **[次へ]** をクリックします。

	![Settings tab](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

	次は **[プレビュー]** タブです。ここで、どのファイルがプロジェクトから API アプリにコピーされるかを確認できます。既にデプロイ済みの API アプリにプロジェクトをデプロイした場合は、変更されたファイルだけがコピーされます。どのファイルがコピーされるかを確認するには、**[プレビューの開始]** ボタンをクリックしてください。

11. **[プレビュー]** タブで、**[発行]** をクリックします。

	![[プレビュー] タブ](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	**[発行]** をクリックすると、Visual Studio では Azure サーバーにファイルをコピーするプロセスが開始されます。これには 1 ～ 2 分ほどかかることがあります。

	**[出力]** ウィンドウと **[Azure App Service アクティビティ]** ウィンドウでは、実行されたデプロイ操作が表示され、デプロイが問題なく完了したことが報告されます。

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started/PublishOutput.png)

	デプロイが成功すると、自動的に既定のブラウザーが開き、デプロイ先の Web アプリの URL にアクセスします。これで、作成したアプリケーションはクラウドで実行されています。ブラウザー アドレス バーの URL は、インターネットから読み込まれた Web アプリを示します。

	![Web app running in Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

> [AZURE.TIP] **[Web の 1 クリック発行]** ツール バーを有効にしておくと、簡単な手順でデプロイできるようになります。**[表示]、[ツール バー]** の順にクリックし、**[Web の 1 クリック発行]** を選択します。このツール バーを使用すると、プロファイルの選択、ボタンをクリックして発行、またはボタンをクリックして **Web の発行**ウィザードを開くことができます。![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## Azure ポータルで Web アプリを監視および管理する

[Azure ポータル](/services/management-portal/)は、作成した Web アプリなどの Azure のサービスを管理および監視するために使用できる Web インターフェイスです。チュートリアルのこのセクションでは、ポータルで何ができるかを確認します。

1. ブラウザーで [https://portal.azure.com](https://portal.azure.com) に移動し、Azure アカウントの管理に使用している資格情報でサインインします。

2. **[App Services]** をクリックした後、Web アプリの名前をクリックします。

	![ポータルの [App Services]](./media/web-sites-dotnet-get-started/selinportal.png)

	**[Web アプリ]** ブレードには、Web アプリの設定と利用統計の概要が表示されます。(ポータルの右側に表示されるウィンドウを*ブレード*といいます。)

	![Web app blade](./media/web-sites-dotnet-get-started/portaldashboard.png)

	この時点では、Web アプリにはトラフィックがそれほど存在しないため、グラフには何も表示されない可能性があります。アプリケーションにアクセスし、何回かページを更新してから、ポータル ページを更新すると、統計が表示されます。

3. **[設定]** ブレードには、Web アプリを構成するための多数のオプションが表示されます。

	![[設定] ブレード](./media/web-sites-dotnet-get-started/portalconfigure1.png)

	このブレードには、このスクリーン ショットに表示されているもの以外のセクションもあります。

4. **[設定]** ブレードの **[全般]** セクションの **[アプリケーションの設定]** をクリックすると、ポータルで構成できる設定の種類の例を見ることができます。

	たとえば、Web アプリで使用される .NET のバージョンを制御したり、[WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) などの機能を有効にしたり、[接続文字列値](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)を設定することなどが可能です。

	![Web アプリ構成タブ](./media/web-sites-dotnet-get-started/portalconfigure2.png)

これらは、ポータル機能の一部にすぎません。新しい Web アプリを作成したり、既存の Web アプリを削除したり、Web アプリを停止して再起動したり、データベースや仮想マシンなど、その他の種類の Azure サービスを管理したりできます。

## 次のステップ

このチュートリアルでは、Azure の Web アプリに単純な Web アプリケーションを作成してデプロイする方法について説明しました。次に示すのは、Azure App Service についてさらに学習するための関連トピックとリソースです。

* 他の方法で Web アプリを作成する

	ここでは、Visual Studio プロジェクトを作成するときに Web アプリを作成する方法を学習しました。Azure リソースを Visual Studio でのデプロイ プロセスで作成することもでき、方法はプロジェクト作成時と同様です。Web アプリの作成は他にも、[Azure ポータル](https://portal.azure.com/)、[Windows PowerShell 用の Azure コマンドレット](../powershell-install-configure.md)、[クロスプラットフォーム コマンドライン インターフェイス](../xplat-cli.md)を使用して行うことができます。

* API アプリを作成する方法

	ここでは、主に Web サイトのホストを目的とする Azure App Service のインスタンスを作成する方法を学習しました。App Service には、API の開発、テスト、ホストを容易にするための機能もあります。詳細については、「[Azure App Service で API Apps と ASP.NET を使用する](../app-service-api/app-service-api-dotnet-get-started.md)」を参照してください。

* Web プロジェクトをデプロイする他の方法

	Visual Studio の使用、または[ソース管理システム](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)からの[デプロイの自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)による Web プロジェクトの Web アプリへのデプロイの他の方法については、[Azure Web アプリをデプロイする方法](web-sites-deploy.md)に関するページを参照してください。

	Visual Studio により、デプロイを自動化するために使用可能な Windows PowerShell スクリプトも生成できます。詳細については、「[Automate Everything (Building Real-World Cloud Apps with Azure) (すべてを自動化 - Azure での実際のクラウド アプリケーションのビルド)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)」を参照してください。

* Web アプリのトラブルシューティングの方法

	Visual Studio には、リアルタイムで生成される Azure ログの表示を容易にする機能があります。Azure では、リモートでデバッグ モードを使用して実行することもできます。詳細については、[Visual Studio での Azure Web アプリのトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)に関するページを参照してください。

* カスタム ドメイン名および SSL の追加方法

	SSL および独自のドメイン (contoso.azurewebsites.net の代わりに www.contoso.com など) の使用方法については、次のリソースを参照してください。

	* [Azure App Service のカスタム ドメイン名の構成](web-sites-custom-domain-name.md)
	* [Azure の Web サイトでの HTTPS の有効化](web-sites-configure-ssl-certificate.md)

* リアルタイム機能 (チャットなど) の追加方法

	Web アプリでリアルタイム機能 (チャット サービス、ゲーム、株価情報など) を使用する場合は、[WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) の転送方法で [ASP.NET SignalR](http://www.asp.net/signalr) を使用すると、パフォーマンスを最大限に高めることができます。詳細については、[Azure Web アプリでの SignalR の使用](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites)に関するページを参照してください。

* Web アプリケーションに対する App Service、Azure Cloud Services、および Azure Virtual Machines の選択方法

	Azure では、このチュートリアルで示したように App Service Web Apps で、または Cloud Services や Virtual Machines で、Web アプリケーションを実行することができます。詳細については、[Azure の Web アプリ、クラウド サービス、VM のうち、いつどれを使用するか](/manage/services/web-sites/choose-web-app-service/)を説明したページを参照してください。

<!---HONumber=AcomDC_0309_2016-->