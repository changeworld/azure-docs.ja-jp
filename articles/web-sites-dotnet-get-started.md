<properties pageTitle="ASP.NET に対応した Azure Websites の概要" metaKeywords="" description="このチュートリアルでは、Visual Studio 2013 で ASP.NET Web プロジェクトを作成し、それを Azure Web サイトにデプロイする方法について説明します。15 分以内に、クラウドでアプリケーションを稼働状態にできます。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="11/12/2014" ms.author="tdykstra" />

# Azure Websites と ASP.NET を使用する

このチュートリアルでは、Visual Studio 2013 または Visual Studio 2013 for Web Express を使用して、ASP.NET Web アプリケーションを作成し、Azure の Web サイトにデプロイする方法を示します。このチュートリアルは、Azure または ASP.NET を使用した経験がない読者を対象に作成されています。このチュートリアルでは、クラウドで動作する単純な Web アプリケーションを作成します。

学習内容:

* Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
* Visual Studio の ASP.NET Web プロジェクトを作成して Azure の Web サイトにデプロイする方法
* Web プロジェクトを変更して、アプリケーションを再デプロイする方法
* Azure の管理ポータルを使用して Web サイトを監視および管理する方法

このチュートリアルを完了するには、Azure アカウントが必要です。

* [無料で Azure アカウントを開く]ことができます (/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F) - Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。
* [MSDN サブスクライバーの特典を有効にする]こともできます (/ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。</li>

> [WACOM.NOTE]
> アカウントにサインアップする前に Azure Websites を試してみたい場合は、 <a href="https://trywebsites.azurewebsites.net">https://trywebsites.azurewebsites.net</a> で、有効期限が短い ASP.NET スターター サイトを Azure Websites に短時間で作成できます。

次の図に、完成したアプリケーションを示します。

![Web site home page](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

### チュートリアル セグメント

* [Microsoft Azure にサインアップする (ビデオ)](#video)
* [開発環境を設定する](#set-up-the-development-environment)
* [Visual Studio で ASP.NET Web アプリケーションを作成する](#create-an-aspnet-web-application)
* [Azure にアプリケーションをデプロイする](#deploy-the-application-to-azure)
* [変更を加えて、再デプロイする](#make-a-change-and-redeploy)
* [管理ポータルでサイトを監視および管理する](#monitor-and-manage-the-site-in-the-management-portal)
* [次のステップ](#next-steps)

##<a name="video"></a>Microsoft Azure にサインアップする (ビデオ)

このビデオでは、Scott Hanselman が Microsoft Azure の無料評価版に簡単にサインアップできることを説明します。(時間:1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## ASP.NET Web アプリケーションを作成する

最初の手順は、Web アプリケーション プロジェクトを作成することです。Visual Studio は後でプロジェクトをデプロイする Azure の Web サイトを自動的に作成します。次の図では、この 2 つの手順について示します。

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1. Visual Studio 2013 または Visual Studio 2013 Express for Web を開きます。

2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

3. **[新しいプロジェクト]** ダイアログ ボックスで、**[C#]**、**[Web]**、**[ASP.NET Web アプリケーション]** を順にクリックします。必要に応じて、**[Visual Basic]** を選択することもできます。

3. ターゲット フレームワークとして **.NET Framework 4.5** が選択されていることを確認します。

4. アプリケーションに「**MyExample**」という名前を付けて、**[OK]** をクリックします。

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**MVC** テンプレートを選択します。ASP.NET Web フォームを使用する場合は、**[Web フォーム]** テンプレートを選択できます。 

	[MVC と Web フォーム](http://www.asp.net/get-started/websites) は Web サイトを作成するための ASP.NET のフレームワークです。このチュートリアルでは、いずれかを選択できますが、Web フォームを選択した場合は、後で *Default.aspx* を編集する必要があります。その場合 *Index.cshtml* を編集するように指示されます。

7. **[認証の変更] ** をクリックします。 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. **[認証の変更]** ダイアログ ボックスで、**[認証なし]** をクリックし、**[OK]** をクリックします。

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	作成しているサンプル アプリケーションでは、ユーザーはログインできません。「[次のステップ](#next-steps) 」セクションは、認証と承認を実装するチュートリアルにリンクしています。

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[Azure]** の設定はそのままにして、**[OK]** をクリックします。 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	既定の設定は、Visual Studio が Web プロジェクトのための Azure の Web サイトを作成することを指定しますチュートリアルの次のセクションでは、Web プロジェクトを新しく作成した Web サイトにデプロイします。

	(チェック ボックスのキャプションは、**[クラウドでのホスト]** または **[リモート リソースの作成]** です。いずれの場合も効果は同じです。)
	
5. Azure にまだサインインしていない場合は、サインインを求めるメッセージが表示されます。**[サインイン]** をクリックします。

	![Sign in to Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. **[Azure へのサインイン]** ダイアログ ボックスで、Azure サブスクリプションを管理する ID とパスワードを入力します。
	
	サインインすると、**[Azure のサイトの設定を構成する]** ダイアログ ボックスが表示され、作成するリソースが求められます。

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. Visual Studio には既定の**サイト名**が用意されており、Azure ではアプリケーションの URL のプレフィックスとして使用されます。必要に応じて、別のサイト名を入力します。

	URL 全体は、ここに入力した内容に *.azurewebsites.net*を追加して構成されます (**[サイト名]** ボックスの横を参照)。たとえば、サイト名が `MyExample6442` である場合、URL は  `MyExample6442.azurewebsites.net` です。URL は一意であることが必要です。入力した URL が既に使用されていると、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別のサイト名を入力する必要があります。

4. **[リージョン]** ボックスの一覧で、現在の所在地に最も近い場所を選択します。

	この設定では、Web サイトが実行される Azure データ センターを指定します。このチュートリアルでは、任意のリージョンを選択でき、選択による違いはありません。ただし、運用サイトでは、サイトにアクセスするブラウザーに最も近い Web サーバーを選択して[待機時間](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)を最小にしてください。

5. データベース フィールドは変更せずそのままにします。

	このチュートリアルでは、データベースは使用しません。このチュートリアルの最後にある「[次のステップ](#next-steps) セクションには、データベースの使用方法を示したチュートリアルへのリンクがあります。

6. **[OK]** をクリックします。

	数秒で、Visual Studio により、指定したフォルダーに Web プロジェクトが作成され、指定した Azure リージョンに Web サイトが作成されます。  

	**[ソリューション エクスプローラー]** ウィンドウに、新しいプロジェクトのファイルとフォルダーが表示されます。

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	**[Web 発行アクティビティ]** ウィンドウに、サイトが作成されたことが表示されます。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	サーバー エクスプローラーにサイトが表示されます。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## Azure にアプリケーションを展開する

7. **[Web 発行アクティビティ]** ウィンドウで、**[今すぐ MyExample をこのサイトに発行する]** をクリックします。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	数秒で、**Web の発行**ウィザードが表示されます。 

	Visual Studio がプロジェクトを Azure にデプロイするために必要な設定が、*発行プロファイル*に保存されます。このウィザードではこれらの設定を確認し、変更できます。

8. **Web の発行**ウィザードの **[接続]** タブで、**[接続の検証]** をクリックして、Web プロジェクトをデプロイするために Visual Studio が Azure に接続できることを確認します。

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	接続が検証されると、**[接続の検証]** ボタンの横に緑色のチェック マークが表示されます。 

9. **[次へ]** をクリックします。

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. **[設定]** タブの **[次へ]** をクリックします。

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	**[構成]** および **[ファイル発行オプション]** には既定値を使用できます。

	**[構成]** ドロップダウンでは、リモート デバッグ用のデバッグ ビルドをデプロイできます。このチュートリアルの最後にある「[次のステップ](#next-steps) 」セクションは、リモートのデバッグ モードで Visual Studio を実行する方法を示したチュートリアルにリンクしています。

	**[ファイル発行オプション]** を展開すると、このチュートリアルでは使用していないシナリオを使用できる複数の設定が表示されます。
 
	* デプロイ先で追加ファイルを削除する。
	  
		プロジェクトに存在しないファイルをサーバーから削除します。以前に別のプロジェクトをデプロイしたサイトにプロジェクトをデプロイした場合に、削除が必要になります。

	* 発行中にプリコンパイルする。 
	 
		大規模なサイトに対して初回要求の準備時間を短くできます。

	* App_Data フォルダーからファイルを除外する。 
	 
		テストでは、運用環境にはデプロイしない App_Data の SQL Server データベース ファイルを使用する場合があります。
	
11. **[プレビュー]** タブで、**[プレビュー開始]** をクリックします。

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	このタブに、サーバーにコピーされるファイルの一覧が表示されます。プレビューの表示は、アプリケーションの発行に必要ではありませんが、知っておくと便利な機能です。

12. **[発行]**をクリックします。

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	Azure サーバーにファイルをコピーする処理が開始されます。

	**[出力]** ウィンドウと **[Web 発行アクティビティ]** ウィンドウでは、実行されたデプロイメント操作が表示され、デプロイメントが問題なく完了したことが報告されます。

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	デプロイメントが成功すると、自動的に既定のブラウザーが開き、デプロイ先の Web サイトの URL にアクセスします。これで、作成したアプリケーションはクラウドで実行されています。ブラウザー アドレス バーの URL は、インターネットから読み込まれているサイトを示します。

	![Web site running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. ブラウザーを閉じます。

## 変更を加えて、再展開する

チュートリアルのこのセクションでは、ホーム ページの **h1** 見出しを変更し、開発用コンピューターでローカルでプロジェクトを実行して変更を確認して、その変更を Azure にデプロイします。

2. **Solution Explorer** の *Views/Home/Index.cshtml* または *.vbhtml* ファイルを開き、**h1** 見出しを「ASP.NET」から「ASP.NET および Azure」に変更して、ファイルを保存します。 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. Ctrl + F5 キーを押してローカル コンピューターでサイトを実行し、更新した見出しを確認します。

	![Web site running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	URL `http://localhost` は、ローカル コンピューターで実行中であることを示しています。既定では、IIS Express で実行されます。これは、Web アプリケーションの開発での使用を想定した、IIS の軽量バージョンです。


1. ブラウザーを閉じます。

1. **ソリューション エクスプローラー**で目的のプロジェクトを右クリックし、**[発行]** を選択します。

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	**Web の発行**ウィザードの [プレビュー] タブが表示されます。発行設定を変更する必要がある場合、異なるタブを選択できますが、今は同じ設定で再展開します。

2. **Web の発行**ウィザードの**[発行]** をクリックします。

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	Visual Studio ではプロジェクトが Azure に展開され、既定のブラウザーでサイトが開きます。

	![Changed site deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**ヒント:**簡易デプロイメントの場合も、**[Web の 1 クリック発行]** ツール バーを有効にできます。**[表示]**、**[ツール バー]** を順にクリックして、**[Web の 1 クリック発行]** を選択します。このツール バーによって、プロファイルを選択し、ボタンをクリックして発行するか、またはボタンをクリックして **Web の発行**ウィザードを開くことができます。 

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## 管理ポータルでサイトを監視および管理する

[Azure の管理ポータル](/ja-jp/services/management-portal/) は、作成した Web サイトなどの Azure のサービスを管理および監視できる Web インターフェイスです。チュートリアルのこのセクションでは、ポータルで何ができるかを確認できます。

1. ブラウザーで、[http://manage.windowsazure.com] に移動し、Azure 資格情報でサインインします。

	このポータルには、Azure サービスの一覧が表示されます。

2. Web サイトの名前をクリックします。

	![Portal home page with new web site called out](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)
  
3. **[ダッシュボード]** タブをクリックします。

	**[ダッシュボード]** タブに、使用統計の概要、および一般的に使用される複数のサイト管理機能へのリンクが表示されます。**[概要]** には、アプリケーションのホーム ページへのリンクも表示されます。

	![Portal web site dashboard tab](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)
  
	この時点では、サイトにはトラフィックがそれほど存在しないため、グラフには何も表示されない可能性があります。アプリケーションにアクセスし、何回かページを更新してから、ポータルの **[ダッシュボード]** ページを更新すると、統計が表示されます。詳細を表示するには、**[監視]** タブをクリックします。

4. **[構成]** タブをクリックします。

	[[構成]](/ja-jp/documentation/articles/web-sites-configure//) タブでは、サイトで使用する .NET バージョンを管理し、[WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) と[診断ログ](/ja-jp/documentation/articles/web-sites-enable-diagnostic-log/)などの機能を有効にして、[接続文字列](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)などを設定します。 

	![Portal web site configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)
  
5. **[スケール]** タブをクリックします。

	Websites サービスの有料レベルでは、[[スケール]](/ja-jp/documentation/articles/web-sites-scale/)  タブで Web アプリケーションで使用するコンピューターの大きさと数を管理して、トラフィックの変化に対応します。

	手動で規模を変更するか、または条件やスケジュールを設定して自動的に規模の設定ができます。

	![Portal website scale tab](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

これらは、管理ポータル機能の一部にすぎません。新しい Web サイトの作成、既存のサイトの削除、サイトの停止や再開に加え、データベースや Virtual Machines など他の種類の Azure サービスを管理することもできます。  

**ヒント:**これらは新しい管理ポータルのプレビューで、最終的には現在使用中のポータルと置き換わります。詳細については、「[Azure Preview Portal (Azure プレビュー ポータル)](/ja-jp/overview/preview-portal/)」を参照してください。

##<a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure の Web サイトに単純な Web アプリケーションを作成してデプロイする方法について説明しました。詳細についての関連トピックとリソースがあります。

* Web プロジェクトをデプロイする他の方法

	このチュートリアルでは、サイトを作成して、オールインワン操作ですべてを展開する最も簡単な方法を説明しました。Visual Studio を使用したデプロイ方法や[デプロイメントの自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)  ([ソース管理システム](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)から実行) によるデプロイ方法の概要については、「[How to Deploy an Azure Website (Azure Web サイトのデプロイ方法)](/ja-jp/documentation/articles/web-sites-deploy/"). 

	Visual Studio can also generate Windows PowerShell scripts that enable you to automate deployment. For more information, see [Automate Everything (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* How to manage a website in Visual Studio

	For information about site management functions that you can do in **Server Explorer**, see [Troubleshooting Azure Websites in Visual Studio](/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)」を参照してください。

* Web サイトのトラブルシューティングの方法

	Visual Studio には、リアルタイムで生成される Azure ログの表示を容易にする機能があります。Azure では、リモートでデバッグ モードを使用して実行することもできます。詳細については、「[Troubleshooting Azure Websites in Visual Studio (Visual Studio での Azure Websites のトラブルシューティング)](/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)」を参照してください。

* データベースと承認の機能を追加する方法

	データベースへのアクセス方法、および一部のサイト機能を権限のあるユーザーだけに制限する方法のチュートリアルについては、「[Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Website (メンバーシップ、OAuth、SQL Database を使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトにデプロイする)](/ja-jp/develop/net/tutorials/web-site-with-sql-database/)」を参照してください。

* カスタム ドメイン名および SSL の追加方法

	SSL および独自のドメイン (contoso.azurewebsites.net の代わりに www.contoso.com など) の使用方法については、次のリソースを参照してください。

	* [Azure の Web サイトのカスタム ドメイン名の構成](/ja-jp/documentation/articles/web-sites-custom-domain-name/)。 
	* [Azure の Web サイトでの HTTPS の有効化](http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/)

* アイドル時間終了後のウェイクアップの待機時間を回避する方法 

	既定では、アイドル状態がしばらく続いている Web サイトはアンロードされます。アイドル状態後の初回要求は、サイトが再度読み込まれるまで待機する必要があります。この待機時間を回避するために、AlwaysOn 機能を有効にすることができます。詳細については、「[Web サイトの構成方法](http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure/)」の構成オプションを参照してください。

* リアルタイム機能 (チャットなど) の追加方法

	Web サイトでリアルタイム機能 (チャット サービス、ゲーム、株価情報など) を使用する場合は、[ASP.NET SignalR](http://www.asp.net/signalr)  を [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) の転送方法で使用すると、パフォーマンスが最大になります。詳細については、「[Using SignalR with Windows Azure Websites (Microsoft Azure Websites で SignalR を使用する)](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites)」を参照してください。 

* Web アプリケーションに対する Azure Websites、Cloud Services、および Virtual Machines の選択方法

	Azure では、このチュートリアルで示したように Websites で、または Cloud Services や Virtual Machines で、Web アプリケーションを実行することができます。詳細については、「[Azure Execution Models (Azure 実行モデル)](/ja-jp/develop/net/fundamentals/compute/) 」および「[Azure Websites, Cloud Services, and VMs:When to use which? (Azure Websites、Azure Cloud Services、Azure Virtual Machines: いつ、どれを使用するか)](/ja-jp/manage/services/web-sites/choose-web-app-service/)」を参照してください。

<!--HONumber=35.1-->
