<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" urlDisplayName="ステップ 3: Web ロール" pageTitle="Azure を使用した ASP.NET 多層 Web アプリケーション - ステップ 3: Web ロール" metaKeywords="Azure チュートリアル, 電子メール サービス アプリケーション, ASP.NET MVC Web ロール, MVC コントローラー, Web API コントローラー, クラウド サービス プロジェクト" description="Azure クラウド サービス内で電子メール サービス アプリケーションを作成してデプロイする方法を学習する 3 番目のチュートリアル。" metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Email Service アプリケーションで使用する Web ロールの作成 - 3/5。" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

# Azure Email Service アプリケーションで使用する Web ロールの作成 - 3/5。

この 3 番目のチュートリアル (全 5 回シリーズ) では、Azure Email Service のサンプル アプリケーションを作成してデプロイする方法を説明します。このアプリケーションとチュートリアル シリーズの詳細については、[シリーズの最初のチュートリアル][firsttutorial]を参照してください。

このチュートリアルでは、次のことについて説明します。

* Web ロールとワーカー ロールを使用してクラウド サービス プロジェクトを含むソリューションを作成する方法
* MVC 4 コントローラーとビューで Azure テーブル、BLOB、およびキューを操作する方法
* Azure テーブルの操作時に同時実行の競合を処理する方法
* Azure のストレージ アカウントを使用できるように Web ロールまたは Web プロジェクトを構成する方法

 
<h2><a name="cloudproject"></a><span class="short-header">ソリューションの作成</span>Visual Studio ソリューションの作成</h2>

まず、Web フロントエンドに使用するプロジェクトと、バックエンドの Azure ワーカー ロールの 1 つに使用するプロジェクトで構成される Visual Studio ソリューションを作成します。後で、2 つ目のワーカー ロールを追加します。

(Azure クラウド サービスではなく Azure の Web サイトで Web UI を実行する場合は、このチュートリアルの「[代替アーキテクチャ][alternativearchitecture]」で、手順の変更を確認してください。)

### Web ロールとワーカー ロールを使用してクラウド サービス プロジェクトを作成する

1. 昇格された特権を使用して Visual Studio を起動します。

	>[WACOM.NOTE] Visual Studio 2013 の場合は、新しいプロジェクトは既定でコンピューティング エミュレーター Express を使用するため、昇格された特権を使用する必要はありません。
   
2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

	![[新しいプロジェクト] メニュー][mtas-file-new-project]

3. **[インストールされたテンプレート]** の **C#** を展開して **[クラウド]** を選択し、**[Azure クラウド サービス]** を選択します。

4. アプリケーションに「**AzureEmailService**」という名前を付けて、**[OK]** をクリックします。

	![[新しいプロジェクト] ダイアログ ボックス][mtas-new-cloud-project]

5. **[新しい Azure クラウド サービス]** ダイアログ ボックスで **[ASP.NET MVC 4 Web ロール]** を選択し、右矢印をクリックします。

	>[WACOM.NOTE]、このチュートリアルで使用するためにダウンロードするコードは MVC 4 形式ですが、Visual Studio 2012.  用に作成された手順を使用して、Visual Studio 2013 で MVC 4 Web ロールを作成することはできません。Visual Studio 2013 の場合は、次の手順を実行してください。(1) Web ロールを作成するためのこの手順をスキップし、ワーカー ロールに関連する手順を実行します。(2) ワーカー ロールを作成した後、**ソリューション エクスプローラー**でソリューションを右クリックし、**[追加]**、**[新しいプロジェクト]** の順にクリックします。**[新しいプロジェクトの追加]** ダイアログの左側のウィンドウで、**[Web]** を展開し、**[Visual Studio 2012]** を選択します。(3) **[ASP.NET MVC 4 Web アプリケーション]** を選択し、プロジェクトに「**MvcWebRole**」という名前を付け、**[OK]** をクリックします。(4) **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[インターネット アプリケーション]** テンプレートを選択します。(5) **ソリューション エクスプローラー**で、**AzureEmailService** の **[ロール]** を右クリックし、**[追加]**、**[ソリューション内の Web ロール プロジェクト]** の順にクリックします。(6) **[ロール プロジェクトとの関連付け]** ボックスで、**MvcWebRole** プロジェクトを選択し、**[OK]** をクリックします。

	![[新しい Azure クラウド サービス] ダイアログ ボックス][mtas-new-cloud-service-dialog]

6. 右側の列でポインターを **[MvcWebRole1]** に合わせて鉛筆のアイコンをクリックすると、Web ロールの名前を変更できます。

7. 新しい名前として「MvcWebRole」と入力し、Enter キーを押します。

	![[新しい Azure クラウド サービス] ダイアログ ボックス -  Web ロールの名前の変更][mtas-new-cloud-service-dialog-rename]

8. 同じ手順で**ワーカー ロール**を追加し、「WorkerRoleA」という名前を付けて、**[OK]** をクリックします。

	![[新しいAzure クラウド サービス] ダイアログ ボックス - ワーカー ロールの追加][mtas-new-cloud-service-add-worker-a]

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[インターネット アプリケーション]** テンプレートを選択します。

6. **[ビュー エンジン]** ボックスの一覧で **[Razor]** が選択されていることを確認し、**[OK]** をクリックします。

	![[新しいプロジェクト] ダイアログ ボックス][mtas-new-mvc4-project]

### ページのヘッダー、メニュー、フッターを設定する

このセクションでは、管理者 Web UI の全ページに表示されるヘッダー、フッター、およびメニュー項目を更新します。このアプリケーションには、Mailing Lists 用、Subscribers 用、および Messages 用として、3 セットの管理者 Web ページを用意します。

1. **ソリューション エクスプローラー**で、Views\Shared フォルダーを展開し、&#95;Layout.cshtml ファイルを開きます。

	![_ソリューション エクスプローラーに表示されている Layout.cshtml ファイル][mtas-opening-layout-cshtml]

2. **&lt;title&gt;** 要素で、"My ASP.NET MVC Application" を "Azure Email Service" に変更します。

3. "site-title" というクラス属性を持つ **&lt;p&gt;** 要素で、"your logo here" を "Azure Email Service" に変更し、"Home" を "MailingList" に変更します。

	![_Layout.cshtml のタイトルとヘッダー][mtas-title-and-logo-in-layout]

4. 次の menu セクションを削除します。

	![_Layout.cshtml の menu セクション][mtas-menu-in-layout]

4. 削除した箇所に、新しい menu セクションを挿入します。

        <ul id="menu">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

4. **&lt;footer&gt;** 要素で、"My ASP.NET MVC Application" を "Azure Email Service" に変更します。<br/>

	![_Layout.cshtml のフッター][mtas-footer-in-layout]

### ローカルでアプリケーションを実行する

1. Ctrl + F5 キーを押して、アプリケーションを実行します。

	アプリケーションのホーム ページが既定のブラウザーに表示されます。

	![ホーム ページ][mtas-home-page-before-adding-controllers]

	アプリケーションは Azure コンピューティング エミュレーターで実行されます。Windows システム トレイに、コンピューティング エミュレーター アイコンが表示されます。

	![システム トレイ内のコンピューティング エミュレーター][mtas-compute-emulator-icon]


<h2><a name="tracing"></a><span class="short-header">トレースの構成</span>トレースの構成</h2>

トレース データの保存を有効にするには、*WebRole.cs* ファイルを開き、次に示す `ConfigureDiagnostics` メソッドを追加します。`OnStart` メソッド内に、この新しいメソッドを呼び出すコードを追加します。

>[WACOM.NOTE] Visual Studio 2013 の場合は、*WebRole.cs* 内のコードを手動で変更する次の手順の代わりに、MvcWebRole プロジェクトを右クリックし、**[既存項目の追加]** をクリックして、ダウンロードしたプロジェクトから *WebRole.cs* ファイルを追加します。

    private void ConfigureDiagnostics()
    {
        DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
        config.Logs.BufferQuotaInMB = 500;
        config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
        config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

        DiagnosticMonitor.Start(
            "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
            config);
    }

    public override bool OnStart()
    {
        ConfigureDiagnostics();
        return base.OnStart();
    }

`ConfigureDiagnostics` メソッドについては、[2 番目のチュートリアル][tut2]で説明しています。





<h2><a name="restarts"></a><span class="short-header">再起動</span>再起動を効率的に処理するコードの追加</h2>

Azure クラウド サービス アプリケーションは、オペレーティング システムの更新に伴い、月に約 2 回再起動されます (OS の更新の詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)」を参照してください)。Web アプリケーションがシャットダウンされる際には、`OnStop` イベントが発生します。Visual Studio によって作成された Web ロール ボイラー プレートでは `OnStop` メソッドをオーバーライドしていないため、アプリケーションが HTTP 要求の処理を完了するための時間はシャットダウンまでに数秒しかありません。シャットダウンが正常に処理されるようにするには、コードを追加して `OnStop` メソッドをオーバーライドすることができます。

シャットダウンと再起動を処理するには、*WebRole.cs* ファイルを開き、次に示す `OnStop` メソッドのオーバーライドを追加します。

      public override void OnStop()
      {
          Trace.TraceInformation("OnStop called from WebRole");
          var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
          while (rcCounter.NextValue() > 0)
          {
              Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
              System.Threading.Thread.Sleep(1000);
          }           
      }

このコードには、次に示す追加の `using` ステートメントが必要です。

      using System.Diagnostics;

`OnStop` メソッドが終了するための時間は、アプリケーションのシャットダウンまでに最長で 5 分間あります。5 分間の Sleep 呼び出しを `OnStop` メソッドに追加することにより、アプリケーションが現在の要求を処理するための時間として最長時間を設定することもできますが、アプリケーションが正しく拡張されていれば、残っている要求を 5 分よりずっと短い時間で処理できます。望ましいのは、アプリケーションができるだけ早く再起動し、要求の処理を続行できるように、できるだけ早く停止することです。

Azure がロールをオフラインにすると、ロール インスタンスへの要求の送信がロード バランサーによって停止され、その後で、`OnStop` メソッドが呼び出されます。ロール インスタンスが他になければ、ロールのシャットダウンが完了して再起動されるまで (通常は数分かかります)、要求は処理されません。Azure サービス レベル アグリーメントでアップタイム保証を利用するためには、ロールごとに 2 つ以上のインスタンスが要求されていますが、その理由の 1 つはこの点です。

`OnStop` メソッド用に示したコードの中で、ASP.NET パフォーマンス カウンター `Requests Current` が作成されています。`Requests Current` カウンターの値には、現在の要求数が設定されます。これには、キューに登録済み、現在実行中、またはクライアントへの書き込み待ちの要求が含まれています。`Requests Current` 値は毎秒チェックされ、ゼロになると、`OnStop` メソッドが制御を返します。`OnStop` が制御を返した時点で、ロールがシャットダウンされます。

[オンデマンド転送](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433075.aspx)を実行せずに `OnStop` メソッドから呼び出された場合、トレース データは保存されません。リモート デスクトップ接続で [dbgview](http://technet.microsoft.com/ja-jp/sysinternals/bb896647.aspx) ユーティリティを使用すると、`OnStop` トレース情報をリアルタイムで確認できます。

<h2><a name="updatescl"></a><span class="short-header">ストレージ クライアント ライブラリの更新</span>ストレージ クライアント ライブラリ NuGet パッケージの更新</h2>

>[WACOM.NOTE] この手順が必要ない場合があります。Azure ストレージ NuGet パッケージが [更新] の一覧に表示されていない場合は、インストール済みのバージョンは最新版です。

Azure のストレージのテーブル、キュー、および BLOB を操作するために使用する API フレームワークは、ストレージ クライアント ライブラリ (SCL) です。この API は、クラウド サービス プロジェクト テンプレート内の NuGet パッケージに含まれています。ただし、このチュートリアルの作成時点で、プロジェクト テンプレートに含まれているのは現在の 2.0 バージョンではなく 1.7 バージョンの SCL です。このため、コードの記述を開始する前に、NuGet パッケージを更新する必要があります。

1. Visual Studio の **[ツール]** メニューの **[ライブラリ パッケージ マネージャー]** にポインターを合わせて、**[ソリューションの NuGet パッケージの管理]** をクリックします。

	![メニュー内の [ソリューションの NuGet パッケージの管理]][mtas-manage-nuget-for-solution]

2. **[NuGet パッケージの管理]** ダイアログ ボックスの左側のウィンドウで、**[更新プログラム]** を選択して **[Azure のストレージ]** パッケージまで下方向へスクロールし、**[更新]** をクリックします。

	![[NuGet パッケージの管理] ダイアログ ボックスに表示された Azure のストレージ パッケージ][mtas-update-storage-nuget-pkg]

3. **[プロジェクトの選択]** ダイアログ ボックスで、両方のプロジェクトが選択されていることを確認し、**[OK]** をクリックします。

	![[プロジェクトの選択] ダイアログ ボックスで両方のプロジェクトが選択されている][mtas-nuget-select-projects]
 
4. ライセンス条項に同意してパッケージのインストールを完了し、**[NuGet パッケージの管理]** ダイアログ ボックスを閉じます。

5. WorkerRoleA プロジェクトの *WorkerRoleA.cs* で、次に示す `using` ステートメントは不要になったため、削除します。

		using Microsoft.WindowsAzure.StorageClient;


1.7 バージョンの SCL には LINQ プロバイダーが含まれており、これを使用するとテーブル クエリのコーディングが容易になります。このチュートリアルの作成時点では、2.0 テーブル サービス レイヤー (TSL) に LINQ プロバイダーは含まれていません。LINQ を使用する場合は、[Microsoft.WindowsAzure.Storage.Table.DataServices](http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.storage.table.dataservices.aspx) 名前空間で SCL 1.7 LINQ プロバイダーにアクセスできます。2.0 TSL はパフォーマンスを向上する設計になっていますが、1.7 LINQ プロバイダーにはパフォーマンス向上によるメリットはありません。サンプル アプリケーションでは 2.0 TSL を使用しているため、クエリに LINQ は使用されません。SCL および TSL 2.0 の詳細については、[このシリーズの最終チュートリアル][tut5]の末尾で紹介されているリソースを参照してください。

>[WACOM.NOTE] ストレージ クライアント ライブラリ 2.1 では LINQ サポートが再度追加されましたが、このチュートリアルでは、ストレージ テーブルのクエリで LINQ を使用しません。現在の SCL では非同期プログラミングもサポートしていますが、このチュートリアルでは非同期コードを示しません。非同期プログラミングに関する詳細情報と Azure SCL でリースを使用するコードの例については、E-Book の次の章と、付属のダウンロード可能なプロジェクトを参照してください。[ブロッキング呼び出しを回避するための .NET 4.5 の非同期サポートの使用に関するページ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async)。


<h2><a name="addref2"></a><span class="short-header">SCL 1.7 参照の追加</span>SCL 1.7 アセンブリへの参照の追加</h2>

>[WACOM.NOTE] SCL 2.1 以降 (最新の SDK が付属している Visual Studio 2013) を使用している場合は、このセクションをスキップします。

ストレージ クライアント ライブラリ (SCL) Version 2.0 には、診断に必要なすべての機能が備わっていないため、1.7 アセンブリへの参照を追加する必要があります。

4. MvcWebRole プロジェクトを右クリックして、**[参照の追加]** を選択します。

5. ダイアログ ボックスの下部にある **[参照]** ボタンをクリックします。

6. 次のフォルダーへ移動します。

        C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

7. *Microsoft.WindowsAzure.StorageClient.dll* を選択して、**[追加]** をクリックします。

8. **[参照マネージャー]** ダイアログ ボックスで **[OK]** をクリックします。

1. WorkerRoleA プロジェクトについても、同じプロセスを繰り返します。



<h2><a name="createifnotexists"></a><span class="short-header">App_Start コード</span>Application_Start メソッドに対する、テーブル、キュー、および BLOB コンテナーを作成するコードの追加</h2>

この Web アプリケーションでは、`MailingList` テーブル、`Message` テーブル、`azuremailsubscribequeue` キュー、および `azuremailblobcontainer` BLOB コンテナーを使用します。Azure ストレージ エクスプローラーなどのツールを使用してこれらを手動で作成することもできますが、新しいストレージ アカウントでアプリケーションの使用を開始するたびに、その処理を手動で行う必要が生じます。このセクションでは、アプリケーションの開始時に実行され、必要なテーブル、キュー、および BLOB コンテナーが存在するかどうかを確認し、存在しない場合はこれらを作成するコードを追加します。

この 1 回限りのスタートアップ コードは、*WebRole.cs* ファイル内の `OnStart` メソッドまたは *Global.asax* ファイルに追加できます。このチュートリアルでは、Azure の Web サイトおよび Azure クラウド サービス Web ロールを操作する *Global.asax* ファイルで Azure のストレージを初期化します。

1. **ソリューション エクスプローラー**で、*Global.asax* を展開し、*Global.asax.cs* を開きます。

2. 次の例に示されているように、新しい `CreateTablesQueuesBlobContainers` 
メソッドを `Application_Start` メソッドの後に追加し、新しい
メソッドを `Application_Start` メソッドから呼び出します。

        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            WebApiConfig.Register(GlobalConfiguration.Configuration);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
            AuthConfig.RegisterAuth();
            // Verify that all of the tables, queues, and blob containers used in this application
            // exist, and create any that don't already exist.
            CreateTablesQueuesBlobContainers();
        }

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
            //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();
            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }
	
3. `RoleEnvironment` の下の波線を右クリックし、**[解決]**、**[using Microsoft.WindowsAzure.ServiceRuntime]** の順に選択します。

	![右クリック][mtas-4]

1. `CloudStorageAccount` の下の波線を右クリックし、**[解決]**、**[using Microsoft.WindowsAzure.Storage]** の順に選択します。

1. もう 1 つの方法として、次に示す using ステートメントを手動で追加することもできます。

	    using Microsoft.WindowsAzure.ServiceRuntime;
	    using Microsoft.WindowsAzure.Storage;
	
1. アプリケーションをビルドします。これにより、ファイルが保存され、コンパイル エラーがないか確認が行われます。

次の各セクションでは、Web アプリケーションの各コンポーネントを構築します。これらは、開発用ストレージまたは自分のストレージ アカウントを使用して、テーブル、キュー、または BLOB コンテナーを手動であらかじめ作成することなくテストすることができます。



<h2><a name="mailinglist"></a><span class="short-header">Mailing List</span>Mailing List コントローラーおよびビューの作成とテスト</h2>

**Mailing List** Web UI は、"Contoso University History Department announcements" や "Fabrikam Engineering job postings" などのメーリング リストを作成、編集、および表示するために、管理者によって使用されます。

### MailingList エンティティ クラスを Models フォルダーに追加する

`MailingList` エンティティ クラスは、メーリング リストの説明やメーリング リストに送信される "差出人" の電子メール アドレスなど、メーリング リストに関する情報を格納する `MailingList` テーブル内の行に使用されます。

1. **ソリューション エクスプローラー**で MVC プロジェクトの `[Models]` フォルダーを右クリックし、**[既存項目の追加]** を選択します。

	![既存の項目を Models フォルダーに追加][mtas-add-existing-item-to-models]

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、`[Models]` フォルダー内の *MailingList.cs* ファイルを選択して、**[追加]** をクリックします。

3. *MailingList.cs* を開いてコードを確認します。

	    public class MailingList : TableEntity
	    {
	        public MailingList()
	        {
	            this.RowKey = "mailinglist";
	        }
	
	        [Required]
	        [RegularExpression(@"[\w]+",
	         ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
	        [Display(Name = "List Name")]
	        public string ListName
	        {
	            get
	            {
	                return this.PartitionKey;
	            }
	            set
	            {
	                this.PartitionKey = value;
	            }
	        }
	
	        [Required]
	        [Display(Name = "'From' Email Address")]
	        public string FromEmailAddress { get; set; }
	
	        public string Description { get; set; }
	    }
			

	Azure ストレージ TSL 2.0 API では、テーブル操作に使用するエンティティ クラスが [TableEntity][] から派生していることが求められます。このクラスでは、`PartitionKey`、`RowKey`、`TimeStamp`、および `ETag` の各フィールドが定義されます。`TimeStamp` プロパティおよび `ETag` プロパティは、システムによって使用されます。このチュートリアルの後の方で、どのように `ETag` プロパティが同時実行処理に使用されるかを説明します。

	(定義済みのモデル クラスを使用する代わりに、Dictionary コレクションのキー値ペアとしてテーブル行を操作する場合に使用する [DynamicTableEntity] クラスもあります。詳細については、[Azure のストレージ クライアント ライブラリ 2.0 テーブルの詳細に関するページ][deepdive]を参照してください。)

	`mailinglist` テーブルのパーティション キーはメーリング リスト名です。このエンティティ クラスでは、`PartitionKey` プロパティ (`TableEntity` クラスで定義されます) または `ListName` プロパティ (`MailingList` クラスで定義されます) を使用してパーティション キー値にアクセスできます。`ListName` プロパティでは、バッキング変数として `PartitionKey` が使用されます。`ListName` プロパティを定義すると、わかりやすい変数名をコード内で使用でき、Web UI のプログラミングが容易になります。これは、書式設定と検証の DataAnnotations 属性を `ListName` プロパティに追加できるためですが、DataAnnotations 属性を `PartitionKey` プロパティに直接追加することはできません。

	`ListName` プロパティに `RegularExpression` 属性が追加されている場合、MVC はユーザー入力を検証して、入力されたリスト名の値に含まれている文字が英数字またはアンダースコアのみであることを確認します。この制約は、URL のクエリ文字列に容易に使用できるように、リスト名をシンプルに保つために実装されています。

	**注:** リスト名の書式に対する制約を緩和するには、クエリ文字列で他の文字および URL エンコード リスト名の使用を許可することもできます。ただし、Azure テーブルのパーティション キーまたは行キーに使用できない特定の文字が存在するため、少なくともこれらの文字は除外する必要があります。パーティション キー フィールドまたは行キー フィールドで使用できない (使用すると問題の原因になる) 文字については、「[テーブル サービス データ モデルについて][tabledatamodel]」および [PartitionKey または RowKey にパーセント '%' 文字が含まれていると、一部の Azure テーブル API が失敗する動作に関するページ][percentinkeyfields]を参照してください。

	`MailingList` クラスで定義されている既定のコンストラクターでは、ハードコーディングされた文字列 "mailinglist" が `RowKey` に設定されます。これは、テーブル内のすべての MailingList 行で、行キーがこの値であるためです (テーブル構造の詳細については、[シリーズの最初のチュートリアル][firsttutorial]を参照してください)。この目的では、任意の定数値を選択することができます。ただし、このテーブルの Subscriber 行の行キーである電子メール アドレスと同じ値にすることはできません。

	新しい `MailingList` エンティティの作成時には、必ずリスト名と "差出人" の電子メール アドレスを入力して、`Required` 属性が指定されているフィールドの値を確保します。

	`Display` 属性は、MVC UI のフィールドに、既定のキャプションを使用することを指定します。

### MailingList MVC コントローラーを追加する

1. **ソリューション エクスプローラー**で MVC プロジェクトの [Controllers] フォルダーを右クリックし、**[既存項目の追加]** を選択します。

	![Add existing item to Controllers folder][mtas-add-existing-item-to-controllers]

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、`[Controllers]` フォルダー内の *MailingListController.cs* ファイルを選択して、**[追加]** をクリックします。

3. *MailingListController.cs* を開いてコードを確認します。

	既定のコンストラクターでは、`mailinglist` テーブルを操作するために使用する `CloudTable` オブジェクトを作成します。

	    public class MailingListController : Controller
	    {
	        private CloudTable mailingListTable;
	
	        public MailingListController()
	        {
	            var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
	            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
	            //    var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
	
	            var tableClient = storageAccount.CreateCloudTableClient();
	            mailingListTable = tableClient.GetTableReference("mailinglist");
	        }
		
	このコードでは、ストレージ アカウントに接続するために、クラウド サービス プロジェクトの設定ファイルから Azure のストレージ アカウントの資格情報を取得しています (このチュートリアルでは、コントローラーをテストする前に、これらの設定を後で構成します)。Azure の Web サイトで MVC プロジェクトを実行する場合、接続文字列は Web.config ファイルから取得できます。

	次の `FindRow` メソッドは、`MailingList` テーブルから特定のメーリング リスト エントリをコントローラーが検索する必要がある場合 (メーリング リスト エントリを編集する場合など) に呼び出されます。このメソッドは、渡されたパーティション キーおよび行キーの値を使用して、単一の `MailingList` エンティティを取得します。このコントローラーが編集する行は、行キーが "MailingList" の行であるため、"MailingList" を行キーとしてハードコーディングすることもできますが、パーティション キーと行キーの指定は、すべてのコントローラーの `FindRow` メソッドで使用されているパターンです。

        private MailingList FindRow(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }

            return mailingList;
        }

	`MailingList` コントローラーの `FindRow` メソッドでは MailingList 行が返されますが、`Subscriber` コントローラーの `FindRow` メソッドでは、同じ `mailinglist` テーブルから Subscriber 行が返されます。

        private Subscriber FindRow(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

	2 つのクエリにおける唯一の相違は、[TableOperation.Retrieve](http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) メソッドに渡されるモデルの種類です。モデルの種類は、クエリから返される 1 つまたは複数の行のスキーマ (プロパティ) を指定します。同じテーブルでも、行によって異なるスキーマを指定することができます。一般的に、行を読み取るときは、行を作成するために使用されたものと同じモデルの種類を指定します。
        
	**Index** ページには、すべての MailingList 行が表示されるため、`Index` メソッド内のクエリは、行キーが "mailinglist" であるすべての `MailingList` エンティティを返します (テーブル内の他の行では、行キーが電子メール アドレスであり、これらには登録者情報が格納されています)。

                var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();

	`Index` メソッドには、このクエリと共に、タイムアウト条件を処理するために設計されたコードが含まれています。

        public ActionResult Index()
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            List<MailingList> lists;
            try
            {
                var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();
            }
            catch (StorageException se)
            {
                ViewBag.errorMessage = "Timeout error, try again. ";
                Trace.TraceError(se.Message);
                return View("Error");
            }

            return View(lists);
        }

	タイムアウト パラメーターを指定しなかった場合は、API により指数関数的にタイムアウト制限が延長され、試行が自動的に 3 回行われます。Web インターフェイスでユーザーがページの表示を待っている場合、この待ち時間は許容できない長さになる可能性があります。そのため、このコードでは、(タイムアウトが毎回延長されないように) 直線的な値が指定され、ユーザーの待ち時間として妥当なタイムアウト制限が指定されています。

	ユーザーが **Create** ページで **[Create]** ボタンをクリックすると、MVC モデル バインダーによって、ビューでの入力に基づいて `MailingList` エンティティが作成され、`HttpPost Create` メソッドにより、このエンティティがテーブルに追加されます。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                mailingListTable.Execute(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

 **Edit** ページでは、`HttpGet Edit` メソッドにより行の検索が行われ、`HttpPost` メソッドにより行が更新されます。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, MailingList editedMailingList)
        {
            if (ModelState.IsValid)
            {
                var mailingList = new MailingList();
                UpdateModel(mailingList);
                try
                {
                    var replaceOperation = TableOperation.Replace(mailingList);
                    mailingListTable.Execute(replaceOperation);
                    return RedirectToAction("Index");
                }
                catch (StorageException ex)
                {
                    if (ex.RequestInformation.HttpStatusCode == 412)
                    {
                        // Concurrency error
                        var currentMailingList = FindRow(partitionKey, rowKey);
                        if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                        {
                            ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                        }
                        if (currentMailingList.Description != editedMailingList.Description)
                        {
                            ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                        }
                        ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                            + "was modified by another user after you got the original value. The "
                            + "edit operation was canceled and the current values in the database "
                            + "have been displayed. If you still want to edit this record, click "
                            + "the Save button again. Otherwise click the Back to List hyperlink.");
                         ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
                    }
                    else
                    {
                        throw; 
                    }
                }
            }
            return View(editedMailingList);
        }

	try-catch ブロックでは、同時実行エラーが処理されます。ユーザーが編集用にメーリング リストを選択し、ブラウザーに **Edit** ページが表示されている間に、別のユーザーが同じメーリング リストを編集すると、同時実行例外が発生します。この状況が発生すると、このコードでは警告メッセージが表示され、別のユーザーによって変更されたフィールドが示されます。TSL API では、`ETag` を使用して同時実行の競合チェックが行われます。テーブル行が更新されるたびに、`ETag` 値が変更されます。編集用に行を取得する際には `ETag` 値が保存され、更新操作または削除操作を実行する際には、保存した `ETag` 値が渡されます (`Edit` ビューには、ETag 値用の非表示フィールドがあります)。更新操作時に、更新対象のレコード上の `ETag` 値が、更新操作に渡された `ETag` 値と異なることが検出されると、同時実行例外が発生します。同時実行の競合が発生してもかまわない場合は、更新操作に渡すエンティティの ETag フィールドをアスタリスク ("*") に設定しておくと、競合が無視されます。

	注: HTTP 412 エラーは、同時実行エラーに特有のものではありません。SCL API から他のエラーを対象にして発生することもあります。

	**Delete** ページでは、`HttpGet Delete` メソッドにより、内容を表示するために行の検索が行われ、`HttpPost` メソッドにより、`MailingList` 行と、`MailingList` テーブル内の関連する `Subscriber` 行が削除されます。

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            var listRows = mailingListTable.ExecuteQuery(query).ToList();
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    mailingListTable.ExecuteBatch(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                mailingListTable.ExecuteBatch(batchOperation);
            }
            return RedirectToAction("Index");
        }

	多数の登録者を削除する必要がある場合は、レコードがバッチで削除されます。1 行を削除する場合も、100 行をバッチで削除する場合も、トランザクション コストは同じです。1 バッチで実行できる操作の最大数は 100 です。

	このループでは、`MailingList` 行と `Subscriber` 行の両方が処理されますが、`Delete` 操作に必要なフィールドは `PartitionKey`、`RowKey`、および `ETag` のみであるため、これらはすべて `MailingList` エンティティ クラスに読み込まれます。

### MailingList MVC ビューを追加する

2. **ソリューション エクスプローラー**で、MVC プロジェクトの *[Views]* フォルダーの下に新しいフォルダーを作成し、「*MailingList*」という名前を付けます。

1. 新しい *Views\MailingList* フォルダーを右クリックして、**[既存項目の追加]** を選択します。

	![既存の項目を Views フォルダーに追加][mtas-add-existing-item-to-views]

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、*Views\MailingList* フォルダー内にある 4 つの .cshtml ファイルをすべて選択して、**[追加]** をクリックします。

3. *Edit.cshtml* ファイルを開いてコードを確認します。

		@model MvcWebRole.Models.MailingList
				@{
		    ViewBag.Title = "Edit Mailing List";
		}
				<h2>Edit Mailing List</h2>
              @using (Html.BeginForm()) {
              @Html.AntiForgeryToken()
              @Html.ValidationSummary(true)
              @Html.HiddenFor(model => model.ETag)
              <fieldset>
		        <legend>MailingList</legend>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ListName)
		        </div>
		        <div class="editor-field">
		            @Html.DisplayFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.Description)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.Description)
		            @Html.ValidationMessageFor(model => model.Description)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.FromEmailAddress)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.FromEmailAddress)
		            @Html.ValidationMessageFor(model => model.FromEmailAddress)
		        </div>
		        <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
				
	このコードは、MVC ビューで使用される典型的なコードです。同時実行の競合処理に使用される `ETag` 値を保持するために、非表示フィールドが含まれています。また、`ListName` フィールドでは、`EditorFor` ヘルパーではなく `DisplayFor` ヘルパーが使用されています。**Edit** ページでリスト名の変更操作は有効になっていません。これは、コントローラーに複雑なコードが必要になるためです。`HttpPost Edit` メソッドで、既存の MailingList 行およびすべての関連する Subscriber 行を削除し、これらをすべて新しいキー値で再挿入する操作が必要になります。運用アプリケーションでは、このように複雑な操作が必要になることがあります。後でわかりますが、`Subscriber` コントローラーでは、メーリング リスト名の変更を許可しています。これは、一度に影響を受ける行が 1 行のみであるためです。

	*Create.cshtml* および *Delete.cshtml* のコードは、*Edit.cshtml* と似ています。

4. *Index.cshtml* を開いてコードを確認します。

	    @model IEnumerable<MvcWebRole.Models.MailingList>
	    @{
	        ViewBag.Title = "Mailing Lists";
	    }
	    <h2>Mailing Lists</h2>
	    <p>
	        @Html.ActionLink("Create New", "Create")
	    </p>
	    <table>
	        <tr>
	            <th>
	                @Html.DisplayNameFor(model => model.ListName)
	            </th>
	            <th>
	                @Html.DisplayNameFor(model => model.Description)
	            </th>
	            <th>
	                @Html.DisplayNameFor(model => model.FromEmailAddress)
	            </th>
	            <th></th>
	        </tr>
	    @foreach (var item in Model) {
	        <tr>
	            <td>
	                @Html.DisplayFor(modelItem => item.ListName)
	            </td>
	            <td>
	                @Html.DisplayFor(modelItem => item.Description)
	            </td>
	            <td>
	                @Html.DisplayFor(modelItem => item.FromEmailAddress)
	            </td>
	            <td>
	                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
	                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
	            </td>
	        </tr>
	    }
	    </table>
	
	このコードも MVC ビューでは典型的です。**[Edit]** ハイパーリンクおよび **[Delete]** ハイパーリンクでは、行を特定するために、クエリ文字列パラメーターとしてパーティション キーおよび行キーを指定します。`MailingList` エンティティでは、行キーは常に "MailingList" であるため、実際に必要なのはパーティション キーのみですが、MVC ビューのコードをすべてのコントローラーおよびビューで統一できるように、両方のキーを残しています。

### MailingList を既定のコントローラーにする

1. *[App_Start]* フォルダー内の *Route.config.cs* を開きます。

2. 既定値を指定する行で、既定のコントローラーを "Home" から "MailingList" に変更します。

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }





<h2><a name="configurestorage"></a><span class="short-header">ストレージの構成</span>テスト用 Azure のストレージ アカウントを使用するための Web ロールの構成</h2>

プロジェクトをローカルで実行する場合に使用するテスト ストレージ アカウント用の設定を入力します。新しい設定を追加するには、クラウドとローカルの両方に追加する必要がありますが、クラウドの値は後で変更できます。同じ設定を後でワーカー ロール A にも追加します。

(Azure クラウド サービスではなく Azure の Web サイトで Web UI を実行する場合は、このチュートリアルの「[代替アーキテクチャ][alternativearchitecture]」で、手順の変更を確認してください。)

1. **ソリューション エクスプローラー**で、**AzureEmailService** クラウド プロジェクトの **[Roles]** の下にある **[MvcWebRole]** を右クリックし、**[プロパティ]** を選択します。

	![Web ロールのプロパティ][mtas-mvcwebrole-properties-menu]

2. **[サービス構成]** ボックスの一覧で **[すべての構成]** が選択されていることを確認します。

2. **[設定]** タブを選択し、**[設定の追加]** をクリックします。

3. **[名前]** 列に「StorageConnectionString」と入力します。

4. **[種類]** ボックスの一覧の **[接続文字列]** を選択します。

6. その行の右端にある **[...]** ボタンをクリックして、**[ストレージ アカウント接続文字列]** ダイアログ ボックスを開きます。

	![[プロパティ] を右クリック][mtas-elip]<br/>

7. **[ストレージ接続文字列の作成]** ダイアログ ボックスで **[お使いのサブスクリプション]** をクリックし、**[発行設定のダウンロード]** リンクをクリックします。

	>[WACOM.NOTE] 最新の SDK を使用する場合は、何もダウンロードする必要はありません。ドロップダウン リスト内で使用できるストレージ アカウントのいずれかを選択します。

	**注:** チュートリアル 2 でストレージ設定を構成し、同じコンピューターでこのチュートリアルを行っている場合は、設定を再度ダウンロードする必要はありません。**[お使いのサブスクリプション]** をクリックし、正しい **[サブスクリプション]** と **[アカウント名]** を選択するだけです。

	![[プロパティ] を右クリック][mtas-enter]<br/>

	**[発行設定のダウンロード]** リンクをクリックすると、Visual Studio により、既定のブラウザーの新しいインスタンスで Azure の管理ポータルの [発行設定のダウンロード] ページの URL が開かれます。ポータルにログインしていない場合は、ログインするように求められます。ログインすると、ブラウザーにより、発行設定を保存するように求められます。設定を保存した場所をメモしておきます。

	![発行の設定][mtas-3]

1. **[ストレージ接続文字列の作成]** ダイアログで **[インポート]** をクリックし、前の手順で保存した発行設定ファイルに移動します。

1. 使用するサブスクリプションとストレージ アカウントを選択し、**[OK]** をクリックします。

	![ストレージ アカウントの選択][mtas-5]

1. `StorageConnectionString` 接続文字列に使用した同じ手順に従って `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 接続文字列を設定します。

	発行設定ファイルを再度ダウンロードする必要はありません。`Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 接続文字列の [...] をクリックすると、**[ストレージ接続文字列の作成]** ダイアログ ボックスでは、サブスクリプション情報が記憶されていることがわかります。**[サブスクリプション]** ラジオ ボタンをクリックすると、前の手順で選択した同じ**サブスクリプション**と**アカウント名**を選択して **[OK]** をクリックするだけで済みます。

2. MvcWebRole ロールに対応する 2 つの接続文字列に使用したものと同じ手順に従って、WorkerRoleA ロールに接続文字列を設定します。

**[設定の追加]** ボタンを使用して新しい設定を追加したときに、*ServiceDefinition.csdf* ファイルの XML と、2 つの *.cscfg* 設定ファイルにそれぞれ新しい設定が追加されました。Visual Studio によって *ServiceDefinition.csdf* ファイルに追加された XML は次のとおりです。

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

各 *.cscfg* 構成ファイルに追加された XML は次のとおりです。

	   <Setting name="StorageConnectionString"
	   value="DefaultEndpointsProtocol=https;
	   AccountName=azuremailstorage;
	   AccountKey=[your account key]" />

*ServiceDefinition.csdf* ファイルと 2 つの *.cscfg* 構成ファイルに設定を手動で追加することもできますが、プロパティ エディターを使用すると、接続文字列に関して次のような利点があります。

- 新しい設定を 1 か所で追加すると、正しい設定の XML が 3 つのファイルすべてに追加される。
- 3 つの設定ファイルについて、正しい XML が生成される。*ServiceDefinition.csdf* ファイルには、各 *.cscfg* 構成ファイルに含める必要のある設定が定義されています。*ServiceDefinition.csdf* ファイルと 2 つの *.cscfg* 構成ファイルの設定に矛盾があると、Visual Studio から "現在のサービス モデルは非同期です。サービス構成ファイルおよびサービス定義ファイルが有効であることを確認してください。" というエラー メッセージが表示されることがあります。

	![無効なサービス構成ファイルおよびサービス定義ファイル エラー][mtas-er1]

このエラーが発生すると、矛盾の問題を解決するまでプロパティ エディターは動作しません。

### アプリケーションをテストします。

1. Ctrl キーを押しながら F5 キーを押してプロジェクトを実行します。

	![空の MailingList の Index ページ][mtas-mailing-list-empty-index-page]

2. **Create** 機能を使用してメーリング リストを追加し、**Edit** 機能と **Delete** 機能を試して動作することを確認します。

	![行が表示されている MailingList の Index ページ][mtas-mailing-list-index-page]



<h2><a name="subscriber"></a><span class="short-header">登録者</span>Subscriber コントローラーおよびビューの作成とテスト</h2>

**登録者** Web UI は、管理者が新しい登録者をメーリング リストに追加し、既存の登録者を編集、表示、および削除するために使用されます。

### Subscriber エンティティ クラスを Models フォルダーに追加する

`Subscriber` エンティティ クラスは、メーリング リストの登録者に関する情報を格納する `MailingList` テーブル内の行に使用されます。これらの行には、登録者の電子メール アドレスやアドレスが確認済みかどうかなどの情報が格納されます。

1. **ソリューション エクスプローラー**で MVC プロジェクトの *[Models]* フォルダーを右クリックし、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、*[Models]* フォルダー内の *Subscriber.cs* ファイルを選択して、**[追加]** をクリックします。

3. *Subscriber.cs* を開いてコードを確認します。

		    public class Subscriber : TableEntity
		    {
		        [Required]
		        public string ListName
		        {
		            get
		            {
		                return this.PartitionKey;
		            }
		            set
		            {
		                this.PartitionKey = value;
		            }
		        }
		
		        [Required]
		        [Display(Name = "Email Address")]
		        public string EmailAddress
		        {
		            get
		            {
		                return this.RowKey;
		            }
		            set
		            {
		                this.RowKey = value;
		            }
		        }
		
		        public string SubscriberGUID { get; set; }
		
                public bool? Verified { get; set; }
		    }
		

	`MailingList` エンティティ クラスと同様、`Subscriber` エンティティ クラスも、`mailinglist` テーブル内の行の読み取りと書き込みに使用されます。`Subscriber` 行では、定数 "mailinglist" ではなく電子メール アドレスが行キーに使用されます (テーブル構造の詳細については、[シリーズの最初のチュートリアル][firsttutorial]を参照してください)。このため、`ListName` が `PartitionKey` をバッキング フィールドとして使用するように、`EmailAddress` プロパティは `RowKey` プロパティをバッキング フィールドとして使用することが定義されています。前に説明したように、これによって、書式設定と検証の DataAnnotations 属性をプロパティに追加できます。

	`SubscriberGUID` 値は、`Subscriber` エンティティの作成時に生成されます。この値は、許可されている人だけが電子メール アドレスを登録または登録解除できるようにするために、登録リンクと登録解除リンクで使用されます。
	新しい登録者に対して行が最初に作成されたとき、`Verified` 値は `false` です。`Verified` 値が `true` に変化するのは、新しい登録者が "ようこそ" メール内の**Confirm** ハイパーリンクをクリックした後だけです。登録者の `Verified` 値が `false` である場合にメーリング リストにメッセージを送信しても、そのサブスクライバーには電子メールが送信されません。

	`Subscriber` エンティティの `Verified` プロパティは、null 値を許容するように定義されています。クエリから `Subscriber` エンティティが返されるように指定した場合、取得した行の一部に `Verified` プロパティが設定されていない可能性もあります。このため、`Subscriber` エンティティでは、*Verified* プロパティのない行がクエリによって返された場合に行の実際の内容をより正確に反映できるように、`Verified` プロパティで null 値を許容するように定義されています。多くの人が、テーブルのすべての行に同じスキーマを使用する SQL Server テーブルの方に慣れているかもしれません。Azure のストレージ テーブルでは、各行はプロパティのコレクションに過ぎず、各行でそれぞれ異なるプロパティのセットが使用されます。たとえば、Azure Email Service サンプル アプリケーションの場合、行キーが "MailingList" である行には `Verified` プロパティがありません。`Verified` プロパティのないテーブル行がクエリから返された場合は、`Subscriber` エンティティ クラスがインスタンス化されるときに、エンティティ オブジェクト内の `Verified` プロパティが null になります。プロパティが null 許容ではない場合、`Verified` 値が `false` である行も `Verified` プロパティがない行も、同じ `false` 値になります。このため、Azure テーブルを使用する場合のベスト プラクティスは、行によって異なるエンティティ クラスまたは現在のエンティティ クラスの複数の異なるバージョンを使用して作成された行を正確に読み取ることができるように、エンティティ クラスの各プロパティを null 許容にすることです。

### Subscriber MVC コントローラーを追加する

1. **ソリューション エクスプローラー**で MVC プロジェクトの *[Controllers]* フォルダーを右クリックし、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、*[Controllers]* フォルダー内の *SubscriberController.cs* ファイルを選択して、**[追加]** をクリックします (*Subscribe.cs* ではなく *Subscriber.cs* を選択してください。*Subscribe.cs* は後で追加します)。

3. *SubscriberController.cs* を開いてコードを確認します。

	このコントローラーのコードは、`MailingList` コントローラーのコードと似ています。登録者情報は `MailingList` テーブルに保存されるため、テーブル名も同じです。`FindRow` メソッドの後に、`GetListNames` メソッドがあります。このメソッドでは、**Create** ページと **Edit** ページにある、電子メール アドレスを登録するメーリング リストを選択するためのドロップダウン リスト用のデータを取得します。

        private List<MailingList> GetListNames()
        {
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            var lists = mailingListTable.ExecuteQuery(query).ToList();
            return lists;
        }

	これは、`MailingList` コントローラーで確認したものと同じクエリです。このドロップダウン リストには、メーリング リストに関する情報が格納されている行が必要であるため、RowKey の値が "mailinglist" である行のみを選択します。

	**Index** ページ用のデータを取得するメソッドでは、登録者情報が格納されている行が必要であるため、RowKey 値が "MailingList" ではない行をすべて選択します。

        public ActionResult Index()
        {
            var query = (new TableQuery<Subscriber>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist")));
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();
            return View(subscribers);
        }

	このクエリでは、(`<Subscriber>` を指定することによって) データを `Subscriber` オブジェクトに読み込むことが指定されていますが、取得するデータは `mailinglist` テーブルから読み込みます。

	**注:** 登録者数が増加すると、このように単一のクエリで処理できなくなることがあります。このチュートリアルの今後のリリースではページング機能を実装し、継続トークンの処理方法を説明することも検討されています。返される行数が 1,000 を超えるクエリを実行する場合は、継続トークンの処理が必要になります。Azure では、1,000 行と継続トークンが返されます。これを使用すると、前のクエリが終了した箇所から継続して次のクエリを実行できます (Azure ストレージ エクスプローラーでは継続トークンが処理されません。このため、1,000 を超える行をクエリから返すことはできません)。大きな結果セットと継続トークンの詳細については、「[How to get most out of Microsoft Azure Tables (Microsoft Azure テーブルの最大活用)][howtogetthemost]」および「[Microsoft Azure Tables: Expect Continuation Tokens, Seriously (Microsoft Azure テーブル: 継続トークンの重要性)](http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously)」を参照してください。

	`HttpGet Create` メソッドでは、ドロップダウン リストのデータをセットアップし、`HttpPost` メソッドでは、新しいエンティティを保存する前に既定値を設定します。

        public ActionResult Create()
        {
            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }

                var insertOperation = TableOperation.Insert(subscriber);
                mailingListTable.Execute(insertOperation);
                return RedirectToAction("Index");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);

            return View(subscriber);
        }

	`Subscriber` ページでは、キー フィールドであるメーリング リスト名または電子メール アドレスを変更できるため、`HttpPost Edit` ページは、`MailingList` コントローラーで確認したものよりも複雑です。これらのうちいずれかのフィールドがユーザーによって変更された場合は、既存のレコードを更新するのではなく、既存のレコードを削除して新しいレコードを追加する必要があります。次のコードは、Edit メソッドのうち、キーの変更と非キーの変更で異なる手順を処理する部分を示しています。
     
            if (ModelState.IsValid)
            {
                try
                {
                    UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
                    if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
                    {
                        //Keys didn't change -- Update the row
                        var replaceOperation = TableOperation.Replace(editedSubscriber);
                        mailingListTable.Execute(replaceOperation);
                    }
                    else
                    {
                        // Keys changed, delete the old record and insert the new one.
                        if (editedSubscriber.PartitionKey != partitionKey)
                        {
                            // PartitionKey changed, can't do delete/insert in a batch.
                            var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                            mailingListTable.Execute(deleteOperation);
                            var insertOperation = TableOperation.Insert(editedSubscriber);
                            mailingListTable.Execute(insertOperation);
                        }
                        else
                        {
                            // RowKey changed, do delete/insert in a batch.
                            var batchOperation = new TableBatchOperation();
                            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                            batchOperation.Insert(editedSubscriber);
                            mailingListTable.ExecuteBatch(batchOperation);
                        }
                    }
                    return RedirectToAction("Index");

	MVC モデル バインダーが `Edit` メソッドに渡すパラメーターには、元のメーリング リスト名と電子メール アドレスの値 (`partitionKey` パラメーターと `rowKey` パラメーター) およびユーザーが入力した値 (`listName` パラメーターと `emailAddress` パラメーター) があります。

        public ActionResult Edit(string partitionKey, string rowKey, string listName, string emailAddress)

	`UpdateModel` メソッドに渡されるパラメーターには、モデル バインディングからの `PartitionKey` プロパティと `RowKey` プロパティは含まれません。

            var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            
	その理由は、`ListName` プロパティと `EmailAddress` プロパティでは、バッキング プロパティとして `PartitionKey` と `RowKey` が使用されており、ユーザーによってこれらの値のいずれかが変更されている可能性があるためです。モデル バインダーが `ListName` プロパティを設定してモデルを更新すると、`PartitionKey` プロパティも自動的に更新されます。モデル バインダーが `ListName` プロパティの更新後に `PartitionKey` プロパティを元の値で更新する場合、`ListName` プロパティで設定された新しい値は上書きされます。同様に、`EmailAddress` プロパティによって自動的に `RowKey` プロパティも更新されます。

	コードでは、`editedSubscriber` モデル オブジェクトを更新した後、パーティション キーまたは行キーが変更されているかどうかを判断します。いずれかのキーが変更されている場合は、既存の Subscriber 行を削除し、新しい Subscriber 行を挿入する必要があります。行キーのみが変更されている場合、削除と挿入はアトミック バッチ トランザクションで行うことができます。

	コードでは、新しいエンティティを作成して `Delete` 操作に渡しています。

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            mailingListTable.ExecuteBatch(batchOperation);

	各バッチ操作に渡すエンティティは、別々のエンティティである必要があります。たとえば、`Subscriber` エンティティを作成して `Delete` 操作に渡し、同じ `Subscriber` エンティティ内の値を変更したものを `Insert` 操作に渡すことはできません。このようにした場合は、プロパティの変更後のエンティティの状態は、Delete 操作でも Insert 操作でも有効になります。

	**注意:** 1 つのバッチに含まれる操作はすべて、対象パーティションが同じである必要があります。メーリング リスト名の変更はパーティション キーの変更も伴うため、1 つのトランザクションでは実行できません。



### Subscriber MVC ビューを追加する

2. **ソリューション エクスプローラー**で、MVC プロジェクトの *[Views]* フォルダーの下に新しいフォルダーを作成し、「*Subscriber*」という名前を付けます。

1. 新しい *Views\Subscriber* フォルダーを右クリックして、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、*Views\Subscriber* フォルダー内にある 5 つの .cshtml ファイルをすべて選択して、**[追加]** をクリックします。

3. *Edit.cshtml* ファイルを開いてコードを確認します。

		@model MvcWebRole.Models.Subscriber
		
		@{
		    ViewBag.Title = "Edit Subscriber";
		}
		
		<h2>Edit Subscriber</h2>
		
		@using (Html.BeginForm()) {
		    @Html.AntiForgeryToken()
		    @Html.ValidationSummary(true)
		     @Html.HiddenFor(model => model.SubscriberGUID)
             @Html.HiddenFor(model => model.ETag)
		     <fieldset>
		        <legend>Subscriber</legend>
		        <div class="display-label">
		             @Html.DisplayNameFor(model => model.ListName)
		        </div>
		        <div class="editor-field">
		            @Html.DropDownList("ListName", String.Empty)
		            @Html.ValidationMessageFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.EmailAddress)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.EmailAddress)
		            @Html.ValidationMessageFor(model => model.EmailAddress)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.Verified)
		        </div>
		        <div class="display-field">
		            @Html.EditorFor(model => model.Verified)
		        </div>
		        <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
						
	このコードは、`MailingList` の **Edit** ビューで確認したコードと似ています。`SubscriberGUID` 値は表示されないため、`HttpPost` コントローラー メソッドのフォーム フィールドに値が自動的には設定されません。このため、この値を保持するために非表示フィールドが含まれています。

	他のビューには、`MailingList` コントローラーで既に確認したものと似たコードが含まれています。

### アプリケーションをテストします。

1. Ctrl キーを押しながら F5 キーを押してプロジェクトを実行し、**[Subscribers]** をクリックします。

	![空の登録者の Index ページ][mtas-subscribers-empty-index-page]

2. **Create** 機能を使用してメーリング リストを追加し、**Edit** 機能と **Delete** 機能を試して動作することを確認します。

	![行が表示されている Subscribers の Index ページ][mtas-subscribers-index-page]



<h2><a name="message"></a><span class="short-header">メッセージ</span>Message コントローラーおよびビューの作成とテスト</h2>

**メッセージ** Web UI は、メーリング リストに送信するメッセージに関する情報を管理者が作成、編集、および表示するために使用されます。

### Message エンティティ クラスを Models フォルダーに追加する

`Message` エンティティ クラスは、メーリング リストに送信するメッセージに関する情報を格納する `Message` テーブル内の行に使用されます。これらの行には、件名、メッセージの送信先リスト、送信予定日などの情報が格納されます。

1. **ソリューション エクスプローラー**で MVC プロジェクトの *[Models]* フォルダーを右クリックし、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、[Models] フォルダー内の *Message.cs* ファイルを選択して、**[追加]** をクリックします。

3. *Message.cs* を開いてコードを確認します。

	    public class Message : TableEntity
	    {
	        private DateTime? _scheduledDate;
	        private long _messageRef;
	
	        public Message()
	        {
	            this.MessageRef = DateTime.Now.Ticks;
	            this.Status = "Pending";
	        }
	
	        [Required]
	        [Display(Name = "Scheduled Date")]
	        // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
	        [DataType(DataType.Date)]
	        public DateTime? ScheduledDate 
	        {
	            get
	            {
	                return _scheduledDate;
	            }
	            set
	            {
	                _scheduledDate = value;
	                this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
	            }
	        }
	        
	        public long MessageRef 
	        {
	            get
	            {
	                return _messageRef;
	            }
	            set
	            {
	                _messageRef = value;
	                this.RowKey = "message" + value.ToString();
	            }
	        }
	
	        [Required]
	        [Display(Name = "List Name")]
	        public string ListName { get; set; }
	
	        [Required]
	        [Display(Name = "Subject Line")]
	        public string SubjectLine { get; set; }
	
	        // Pending, Queuing, Processing, Complete
	        public string Status { get; set; }
	    }
		
	`Message` クラスで定義されている既定のコンストラクターでは、メッセージを示す一意の値が `MessageRef` プロパティに設定されます。この値は行キーの一部であるため、`MessageRef` プロパティの setter は自動的に `RowKey` プロパティも設定します。`MessageRef` プロパティの setter は、"message" リテラルと `MessageRef` 値を連結し、それを `RowKey` プロパティに設定します。

	`MessageRef` 値は、`DateTime.Now` から `Ticks` 値を取得することによって作成されます。これにより、Web UI にメッセージを表示する場合は、既定で、指定の予定日 (`ScheduledDate` はパーティション キー) に対して作成された順序で表示されるようになります。GUID を使用して Message 行を一意にすることもできますが、この場合は、既定の取得順序がランダムになります。

	既定のコンストラクターでは、新しい `Message` 行に既定のステータスとして "Pending" を設定する処理も行われます。

	`Message` テーブル構造の詳細については、[シリーズの最初のチュートリアル][firsttutorial]を参照してください。

### Message MVC コントローラーを追加する

1. **ソリューション エクスプローラー**で MVC プロジェクトの [Controllers] フォルダーを右クリックし、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、*[Controllers]* フォルダー内の *MessageController.cs* ファイルを選択して、**[追加]** をクリックします。

3. *MessageController.cs* を開いてコードを確認します。

	このコントローラーのコードの大半は、`Subscriber` コントローラーで確認したものと似ています。新しい部分は、BLOB を操作するコードです。各メッセージについて、電子メールの HTML コンテンツとプレーンテキスト コンテンツが .htm ファイルと .txt ファイルの形式でアップロードされ、BLOB に保存されます。

	BLOB は BLOB コンテナーに保存されます。Azure Email Service アプリケーションでは、すべての BLOB が "azuremailblobcontainer" という名前の単一の BLOB コンテナーに格納されます。コントローラー コンストラクターのコードでは、この BLOB コンテナーの参照を取得しています。

	    public class MessageController : Controller
	    {
	        private TableServiceContext serviceContext;
	        private static CloudBlobContainer blobContainer;

		      public MessageController()
	        {
	            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
	            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
	            //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
	
	            // Get context object for working with tables and a reference to the blob container.
	            var tableClient = storageAccount.CreateCloudTableClient();
            serviceContext = tableClient.GetTableServiceContext();
	            var blobClient = storageAccount.CreateCloudBlobClient();
	            blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
	        }
	
	ユーザーが選択してアップロードする各ファイルについて、MVC ビューでは、ファイルに関する情報が格納された `HttpPostedFile` オブジェクトを提供します。ユーザーが新しいメッセージを作成すると、`HttpPostedFile` オブジェクトを使用してファイルが BLOB に保存されます。ユーザーがメッセージを編集する際に、ユーザーは置換ファイルをアップロードすることまたは BLOB を変更せずに維持することを選択できます。

	このコントローラーには、`HttpPost Create` メソッドおよび `HttpPost Edit` メソッドから、BLOB を保存するために呼び出されるメソッドが含まれています。

        private void SaveBlob(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob. 
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                blob.UploadFromStream(fileStream);
            }
        }

	`HttpPost Create` メソッドは 2 つの BLOB を保存してから、`Message` テーブル行を追加します。BLOB の名前は、`MessageRef` 値にファイル名拡張子 ".htm" または ".txt" を連結したものになります。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }

            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }

            if (ModelState.IsValid)
            {
                SaveBlob(message.MessageRef + ".htm", file);
                SaveBlob(message.MessageRef + ".txt", txtFile);

                var insertOperation = TableOperation.Insert(message);
                messageTable.Execute(insertOperation);

                return RedirectToAction("Index");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

	`HttpGet Edit` メソッドでは、ワーカー ロール B で処理が開始されたメッセージをユーザーが変更できないように、取得されたメッセージが `Pending` ステータスかどうかを検証します。`HttpPost Edit` メソッド、`Delete` メソッド、および `DeleteConfirmed` メソッドにも、同様のコードが含まれています。

        public ActionResult Edit(string partitionKey, string rowKey)
        {
            var message = FindRow(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be edited because it isn't in Pending status.");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", message.ListName);
            return View(message);
        }

	`HttpPost Edit` メソッドでは、ユーザーが新しいファイルのアップロードを選択した場合のみ、新しい BLOB を保存しています。次のコードで、メソッドの同時実行処理部分は、`MailingList` コントローラーで確認したものと同じであるため、省略されています。
 
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, Message editedMsg,
            DateTime scheduledDate, HttpPostedFileBase httpFile, HttpPostedFileBase txtFile)
        {
            if (ModelState.IsValid)
            {
                var excludePropLst = new List<string>();
                excludePropLst.Add("PartitionKey");
                excludePropLst.Add("RowKey");

                if (httpFile == null)
                {
                    // They didn't enter a path or navigate to a file, so don't update the file.
                    excludePropLst.Add("HtmlPath");
                }
                else
                {
                    // They DID enter a path or navigate to a file, assume it's changed.
                    SaveBlob(editedMsg.MessageRef + ".htm", httpFile);
                }

                if (txtFile == null)
                {
                    excludePropLst.Add("TextPath");
                }
                else
                {
                    SaveBlob(editedMsg.MessageRef + ".txt", txtFile);
                }

                string[] excludeProperties = excludePropLst.ToArray();

                try
                {
                    UpdateModel(editedMsg, string.Empty, null, excludeProperties);
                    if (editedMsg.PartitionKey == partitionKey)
                    {
                        // Keys didn't change -- update the row.
                        var replaceOperation = TableOperation.Replace(editedMsg);
                        messageTable.Execute(replaceOperation);
                    }
                    else
                    {
                        // Partition key changed -- delete and insert the row.
                        // (Partition key has scheduled date which may be changed;
                        // row key has MessageRef which does not change.)
                        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
                        messageTable.Execute(deleteOperation);
                        var insertOperation = TableOperation.Insert(editedMsg);
                        messageTable.Execute(insertOperation);
                    }
                    return RedirectToAction("Index");
                }

	予定日が変更されると、パーティション キーが変更され、行の削除と挿入が必要になります。この操作は複数のパーティションが対象になるため、1 つのトランザクションでは実行できません。

	`HttpPost Delete` メソッドは、テーブル内の行を削除する際に、BLOB を削除します。

        [HttpPost, ActionName("Delete")]
        public ActionResult DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = FindRow(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }

            DeleteBlob(message.MessageRef + ".htm");
            DeleteBlob(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }

        private void DeleteBlob(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            blob.Delete();
        }

### Message MVC ビューを追加する

2. **ソリューション エクスプローラー**で、MVC プロジェクトの *[Views]* フォルダーの下に新しいフォルダーを作成し、「`Message`」という名前を付けます。

1. 新しい *Views\Message* フォルダーを右クリックして、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、*Views\Message* フォルダー内にある 5 つの .cshtml ファイルをすべて選択して、**[追加]** をクリックします。

3. *Edit.cshtml* ファイルを開いてコードを確認します。

		@model MvcWebRole.Models.Message
		
		@{
		    ViewBag.Title = "Edit Message";
		}
		
		<h2>Edit Message</h2>
		
		@using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
		{
		    @Html.AntiForgeryToken()
		    @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
		    <fieldset>
		        <legend>Message</legend>
		        @Html.HiddenFor(model => model.MessageRef)
		        @Html.HiddenFor(model => model.PartitionKey)
		        @Html.HiddenFor(model => model.RowKey)
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ListName, "MailingList")
		        </div>
		        <div class="editor-field">
		            @Html.DropDownList("ListName", String.Empty)
		            @Html.ValidationMessageFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.SubjectLine)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.SubjectLine)
		            @Html.ValidationMessageFor(model => model.SubjectLine)
		        </div>
		        <div class="editor-label">
		            HTML Path: Leave blank to keep current HTML File.
		        </div>
		        <div class="editor-field">
		            <input type="file" name="file" />
		        </div>
		        <div class="editor-label">
		            Text Path: Leave blank to keep current Text File.
		        </div>
		        <div class="editor-field">
		            <input type="file" name="TxtFile" />
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ScheduledDate)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.ScheduledDate)
		            @Html.ValidationMessageFor(model => model.ScheduledDate)
		        </div>
		        <div class="display-label">
		            @Html.DisplayNameFor(model => model.Status)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.Status)
		        </div>
		       <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
				
	`HttpPost Edit` メソッドではパーティション キーと行キーが必要であるため、これらが非表示フィールドで提供されています。`Subscriber` コントローラーでは、非表示フィールドは不要でした。これは、(a) `Subscriber` モデルの `ListName` プロパティと `EmailAddress` プロパティにより、`PartitionKey` プロパティと `RowKey` プロパティが更新され、(b) Edit ビューには `ListName` プロパティと `EmailAddress` プロパティが `EditorFor` ヘルパーと共に含まれているためです。`Subscriber` モデルの MVC モデル バインダーが `ListName` プロパティを更新する際には `PartitionKey` プロパティが自動的に更新され、MVC モデル バインダーが `Subscriber` モデルの `EmailAddress` プロパティを更新する際には `RowKey` プロパティが自動的に更新されます。`Message` モデルでは、パーティション キーと行キーにマップされるフィールドは編集可能フィールドではないため、設定方法が異なります。

	`MessageRef` プロパティ用の非表示フィールドも含まれています。これはパーティション キーと同じ値ですが、`HttpPost Edit` メソッドのコードをわかりやすくするために含まれています。`MessageRef` 非表示フィールドを含めておくと、`HttpPost Edit` メソッドのコードは、BLOB のファイル名を作成するときに、その名前で`MessageRef` 値を参照することができます。
   
3. *Index.cshtml* ファイルを開いてコードを確認します。

		@model IEnumerable<MvcWebRole.Models.Message>
		
		@{
		    ViewBag.Title = "Messages";
		}
		
		<h2>Messages</h2>
		
		<p>
		    @Html.ActionLink("Create New", "Create")
		</p>
		<table>
		    <tr>
		        <th>
		            @Html.DisplayNameFor(model => model.ListName)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.SubjectLine)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.ScheduledDate)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.Status)
		        </th>
		        <th></th>
		    </tr>
		    @foreach (var item in Model)
		    {
		        <tr>
		            <td>
		                @Html.DisplayFor(modelItem => item.ListName)
		            </td>
		            <td>
		                @Html.DisplayFor(modelItem => item.SubjectLine)
		            </td>
		            <td>
		                @Html.DisplayFor(modelItem => item.ScheduledDate)
		            </td>
		            <td>
		                @item.Status
		            </td>
		            <td>
		                @if (item.Status == "Pending")
		                {
		                    @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
		                    @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
		                }
		                @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
		            </td>
		        </tr>
		    }
		
		</table>
				
	他の **Index** ビューと異なる点は、`Pending` ステータスのメッセージに対してのみ **[Edit]** リンクと **[Delete]** リンクが表示されることです。

        @if (item.Status == "Pending")
        {
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
        }

	これによってユーザーは、ワーカー ロール A で処理が開始されたメッセージを変更できなくなります。

	他のビューには、他のコントローラーで確認した **Edit** ビューまたはその他のビューと似たコードが含まれています。

### アプリケーションをテストします。

1. Ctrl キーを押しながら F5 キーを押してプロジェクトを実行し、**[Messages]** をクリックします。

	![空のメッセージの Index ページ][mtas-message-empty-index-page]

2. **Create** 機能を使用してメーリング リストを追加し、**Edit** 機能と **Delete** 機能を試して動作することを確認します。

	![行が表示されている Subscribers の Index ページ][mtas-message-index-page]




<h2><a name="unsubscribe"></a><span class="short-header">登録解除</span>Unsubscribe コントローラーおよびビューの作成とテスト</h2>

次は、登録解除プロセスの UI を実装します。

**注:** このチュートリアルでは、登録プロセスではなく登録解除プロセス用のコントローラーのみを構築します。[最初のチュートリアル][firsttutorial]で説明したように、登録プロセスの UI およびサービス メソッドは、サービス メソッドの適切なセキュリティを実装するまで除外されています。それまでは、**Subscriber** 管理者ページを使用して電子メール アドレスをメーリング リストに登録できます。

### Unsubscribe ビュー モデルを Models フォルダーに追加する

`UnsubscribeVM` ビュー モデルは、`Unsubscribe` コントローラーとビューとの間でデータをやり取りするために使用されます。

1. **ソリューション エクスプローラー**で MVC プロジェクトの `[Models]` フォルダーを右クリックし、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、[Models] フォルダー内の `UnsubscribeVM.cs` ファイルを選択して、**[追加]** をクリックします。

3. `UnsubscribeVM.cs` を開いてコードを確認します。


	    public class UnsubscribeVM
	    {
	        public string EmailAddress { get; set; }
	        public string ListName { get; set; }
	        public string ListDescription { get; set; }
	        public string SubscriberGUID { get; set; }
	        public bool? Confirmed { get; set; }
	    }

	登録解除リンクには、`SubscriberGUID` が含まれています。この値は、電子メール アドレス、メーリング リスト名、およびメーリング リストの説明を `MailingList` テーブルから取得するために使用されます。ビューでは、電子メール アドレスと、登録解除するメーリング リストの説明が表示され、**[Confirm]** ボタンが表示されます。ユーザーは、このボタンをクリックして登録解除プロセスを完了する必要があります。

### Unsubscribe コントローラーを追加する

1. **ソリューション エクスプローラー**で MVC プロジェクトの `[Controllers]` フォルダーを右クリックし、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、*[Controllers]* フォルダー内の *UnsubscribeController.cs* ファイルを選択して、**[追加]** をクリックします。

3. *UnsubscribeController.cs* を開いてコードを確認します。

	このコントローラーに含まれている `HttpGet Index` メソッドは登録解除用の初期ページを表示し、`HttpPost Index` メソッドは **[Confirm]** ボタンまたは **[Cancel]** ボタンを処理します。

	`HttpGet Index` メソッドはクエリ文字列内で GUID およびメーリング リスト名を使用して、登録者に対応する `MailingList` テーブル行を取得します。次に、ビューに必要なすべての情報をビュー モデルに読み込み、**登録解除用**ページを表示します。さらに、**登録解除用**ページの初期バージョンが表示されるように、`Confirmed` プロパティを null に設定しています。
 
	     public ActionResult Index(string id, string listName)
	     {
	         if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
	         {
	             ViewBag.errorMessage = "Empty subscriber ID or list name.";
	             return View("Error");
	         }
	         string filter = TableQuery.CombineFilters(
	             TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
	             TableOperators.And,
	             TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
	         var query = new TableQuery<Subscriber>().Where(filter);
	         var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
	         if (subscriber == null)
	         {
	             ViewBag.Message = "You are already unsubscribed";
	             return View("Message");
	         }
	         var unsubscribeVM = new UnsubscribeVM();
	         unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
	         unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
	         unsubscribeVM.SubscriberGUID = id;
	         unsubscribeVM.Confirmed = null;
	         return View(unsubscribeVM);
	     }

	注: SubscriberGUID がパーティション キーまたは行キーに含まれていないため、パーティション サイズ (メーリング リスト内の電子メール アドレス数) が大きくなると、このクエリのパフォーマンスは低下します。このクエリの拡張性を向上するための代替方法の詳細については、[シリーズの最初のチュートリアル][firsttutorial]を参照してください。

	`HttpPost Index` メソッドは、GUID とメーリング リスト名を使用して登録者情報を取得し、ビュー モデルのプロパティに値を設定します。次に、**[Confirm]** ボタンがクリックされた場合は、`MailingList` テーブル内の該当する Subscriber 行を削除します。**[Confirm]** ボタンが押された場合は `Confirm` プロパティを `true` に設定し、それ以外の場合は `Confirm` プロパティを `false` に設定します。`Confirm` プロパティの値は、**登録解除用**ページの確認済みバージョンを表示するか取り消し済みバージョンを表示するかを指定する値です。

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public ActionResult Index(string subscriberGUID, string listName, string action)
        {
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();

            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;

            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }

            return View(unsubscribeVM);
        }

### MVC ビューを作成する

2. **ソリューション エクスプローラー**で、MVC プロジェクトの *[Views]* フォルダーの下に新しいフォルダーを作成し、「*Unsubscribe*」という名前を付けます。

1. 新しい *Views\Unsubscribe* フォルダーを右クリックして、**[既存項目の追加]** を選択します。

2. サンプル アプリケーションをダウンロードしたフォルダーに移動し、*Views\Unsubscribe* フォルダー内の *Index.cshtml* ファイルを選択して、**[追加]** をクリックします。

3. *Index.cshtml* ファイルを開いてコードを確認します。
		
		@model MvcWebRole.Models.UnsubscribeVM
		
		@{
		    ViewBag.Title = "Unsubscribe";
		    Layout = null;
		}
		
		<h2>Email List Subscription Service</h2>
		
		@using (Html.BeginForm()) {
		    @Html.AntiForgeryToken()
		    @Html.ValidationSummary(true)
		    <fieldset>
		        <legend>Unsubscribe from Mailing List</legend>
		        @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
		        @if (Model.Confirmed == null) {
		            <p>
		                Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
		           </p>
		            <br />
		            <p>
		                <input type="submit" value="Confirm" name="action"/> 
		                &nbsp; &nbsp;
		                <input type="submit" value="Cancel" name="action"/>
		            </p>
		        }
		        @if (Model.Confirmed == false) {
		            <p>
		                @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
		            </p>
		        }
		        @if (Model.Confirmed == true) {
		            <p>
		                @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
		            </p>
		        }
		    </fieldset>
		}
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
						
	`Layout = null` 行では、このページの表示に _Layout.cshtml ファイルを使用しないことを指定しています。**登録解除用**ページには、非常にシンプルな UI が表示されます。管理ページに使用されているヘッダーやフッターはありません。

	ページの本体については、`Confirmed` プロパティによって表示内容が判断されます。このプロパティが null であれば **[Confirm]** ボタンと **[Cancel]** ボタン、プロパティが true であれば登録解除が確認されたことを示すメッセージ、プロパティが false であれば登録解除が取り消されたことを示すメッセージを表示します。

### アプリケーションをテストします。

1. Ctrl キーを押しながら F5 キーを押してプロジェクトを実行し、**[Subscribers]** をクリックします。

2. **[Create]** をクリックして、テスト時に作成したメーリング リストに対応する新しい登録者を作成します。

	ブラウザーのウィンドウで、**Subscribers** **Index** ページを開いたままにしておきます。

3. Azure ストレージ エクスプローラーを開き、テスト用のストレージ アカウントを選択します。

4. **[ストレージの種類]** の **[テーブル]** をクリックし、**MailingList** テーブルを選択して、**[クエリ]** をクリックします。

5. 追加した Subscriber 行をダブルクリックします。

	![Azure ストレージ エクスプローラー][mtas-ase-unsubscribe]

6. **[エンティティの編集]** ダイアログ ボックスで、`SubscriberGUID` の値を選択してコピーします。

	![Azure ストレージ エクスプローラー][mtas-ase-edit-entity-unsubscribe]

7. ブラウザー ウィンドウに戻ります。ブラウザーのアドレス バーで、URL に含まれる "Subscriber" を "unsubscribe?ID=[guidvalue]&listName=[listname]" ([guidvalue] は Azure ストレージ エクスプローラーからコピーした GUID、[listname] はメーリング リストの名前) に変更します。次に例を示します。

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

	確認を求めるバージョンの**登録解除用**ページが表示されます。

	![登録解除用ページ][mtas-unsubscribe-page]

2. **[Confirm]** をクリックすると、電子メール アドレスが登録解除されたという確認が表示されます。

	![登録解除完了のお知らせページ][mtas-unsubscribe-confirmed-page]

3. **Subscribers** **Index** ページに戻り、Subscriber 行が表示されていないことを確認します。




<h2><a name="alternativearchitecture"></a><span class="short-header">代替アーキテクチャ</span>(省略可能) 代替アーキテクチャの構築</h2>

次に示す手順の変更は、代替アーキテクチャを構築する場合に適用されます。その場合は、Azure クラウド サービス Web ロールではなく Azure の Web サイトで Web UI を実行します。

* ソリューションを作成する際には、まず **ASP.NET MVC 4 Web アプリケーション** プロジェクトを作成してから、ワーカー ロールで **Azure クラウド サービス** プロジェクトにソリューションを追加します。

* Azure のストレージ接続文字列はクラウド サービス設定ファイルではなく Web.config ファイルに保存します (これは、Azure の Web サイトを使用する場合のみに機能する手順です。Azure クラウド サービス Web ロールのストレージ接続文字列に Web.config ファイルを使用しようとすると、HTTP 500 エラーが発生します)。

次の例のように、`StorageConnectionString` という名前の新しい接続文字列を *Web.config* ファイルに追加します。

	       <connectionStrings>
	          <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-MvcWebRole-20121010185535;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-MvcWebRole-20121010185535.mdf" providerName="System.Data.SqlClient" />
	          <add name="StorageConnectionString" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[primarykey]" />
	       </connectionStrings>
	
接続文字列の値は、[Azure 管理ポータル][managementportal]から取得します。これには、ストレージ アカウントの **[ストレージ]** タブを選択し、ページの下部にある **[キーの管理]** をクリックします。

*コード内の `RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString")` は、すべて `ConfigurationManager.ConnectionStrings[["StorageConnectionString"]].ConnectionString` に置き換えてください。



<h2><a name="nextsteps"></a><span class="short
-header">次のステップ</span>次のステップ</h2>

[シリーズの最初のチュートリアル][firsttutorial]で説明したように、このチュートリアルでは、ASP.NET Web API サービス メソッドをセキュリティで保護するための共有シークレットが実装されるまで、登録プロセスの詳細な構築方法を示しません。ただし、サービス メソッドは IP 制限によっても保護されます。登録機能は、ダウンロードしたプロジェクトから次の各ファイルをコピーすることによって追加できます。

ASP.NET Web API サービス メソッド:

* Controllers\SubscribeAPI.cs

サービス メソッドで生成された電子メールに含まれる **Confirm** リンクを登録者がクリックしたときに表示される Web ページ:

* Models\SubscribeVM.cs
* Controllers\SubscribeController.cs
* Views\Subscribe\Index.cshtml

[次のチュートリアル][nexttutorial]では、電子メールのスケジュールを設定するワーカー ロール A の構成とプログラミングを行います。

Azure のストレージのテーブル、キュー、BLOB に関する参考情報は、[このシリーズの最終チュートリアル][tut5]の末尾に記載されています。

<div><a href="/ja-jp/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">チュートリアル 4</a></div>



[alternativearchitecture]: #alternativearchitecture


[tut5]: /ja-jp/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[tut2]: /ja-jp/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[firsttutorial]: /ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/
[nexttutorial]: /ja-jp/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/

[TableEntity]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
[DynamicTableEntity]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[managementportal]: http://manage.windowsazure.com

[percentinkeyfields]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
[tabledatamodel]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179338.aspx 
[deepdive]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
[howtogetthemost]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx

[mtas-home-page-before-adding-controllers]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
[mtas-menu-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-menu-in-layout.png
[mtas-footer-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-footer-in-layout.png
[mtas-title-and-logo-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-title-and-logo-in-layout.png
[mtas-new-cloud-service-dialog-rename]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
[mtas-new-mvc4-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
[mtas-new-cloud-service-dialog]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
[mtas-new-cloud-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
[mtas-new-cloud-service-add-worker-a]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
[mtas-mailing-list-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
[mtas-file-new-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
[mtas-opening-layout-cshtml]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-opening-layout-cshtml.png

[mtas-add-existing-item-to-models]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-models.png
[mtas-add-existing-item-to-controllers]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-controllers.png
[mtas-add-existing-item-to-views]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-views.png
[mtas-mvcwebrole-properties-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png




[mtas-subscribers-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
[mtas-message-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
[mtas-ase-edit-entity-unsubscribe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
[mtas-ase-unsubscribe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-unsubscribe.png
[mtas-unsubscribe-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
[mtas-unsubscribe-confirmed-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
[mtas-er1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
[mtas-4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-4.png
[mtas-3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-3.png
[mtas-5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-5.png
[mtas-enter]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-enter.png
[mtas-elip]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
[mtas-manage-nuget-for-solution]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
[mtas-update-storage-nuget-pkg]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
[mtas-nuget-select-projects]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
[mtas-compute-emulator-icon]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png



