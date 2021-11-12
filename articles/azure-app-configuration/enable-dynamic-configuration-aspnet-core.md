---
title: チュートリアル:ASP.NET Core で App Configuration の動的な構成を有効にする
titleSuffix: Azure App Configuration
description: このチュートリアルでは、ASP.NET Core アプリの構成データを動的に更新する方法を学習します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4e7ce4f07e49b7efc0f62ff668e72542fc3def77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302009"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリで動的な構成を使用する

ASP.NET Core には、さまざまなソースから構成データを読み取ることができるプラグ可能な構成システムがあります。 アプリケーションを再起動せずに、動的に変更を処理できます。 ASP.NET Core では、厳密に型指定された .NET クラスへの構成設定のバインドがサポートされています。 `IOptionsSnapshot<T>` を使用して、それがコードに挿入されます。これにより、基になるデータが変更された場合にアプリケーションの構成が自動的に再読み込みされます。

このチュートリアルでは、自分が作成するコードに、構成の動的更新を実装する方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration ストアへの変更に合わせて構成を更新するようにアプリケーションを設定する。
> * アプリケーションのコントローラーに最新の構成を挿入する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

## <a name="add-a-sentinel-key"></a>センチネル キーを追加する

''*センチネル*'' キーは、他のすべてのキーの変更を完了した後に更新する特殊なキーです。 センチネル キーはアプリケーションによって監視されます。 変更が検出されると、アプリケーションによって構成の値がすべて更新されます。 このアプローチは、アプリケーションの構成の一貫性を確保するのに役立ち、すべてのキーの変更を監視する場合と比べると、App Configuration に対して行う全体的な要求の数が少なくなります。

1. Azure portal で **[構成エクスプローラー] > [作成] > [キー値]** の順に選択します。
1. **[キー]** に「*TestApp:Settings:Sentinel*」と入力します。 **[値]** には、「1」と入力します。 **[ラベル]** と **[コンテンツの種類]** は空にしておきます。
1. **[適用]** を選択します。

> [!NOTE]
> センチネル キーを使用していない場合は、監視するすべてのキーを手動で登録する必要があります。

## <a name="reload-data-from-app-configuration"></a>App Configuration からデータを再度読み込む

1. 次のコマンドを実行して、`Microsoft.Azure.AppConfiguration.AspNetCore` NuGet パッケージへの参照を追加します。

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. *Program.cs* を開き、`config.AddAzureAppConfiguration()` メソッドを追加して `CreateWebHostBuilder` メソッドを更新します。

   #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    `ConfigureRefresh` メソッドでは、変更を監視するキーを App Configuration ストア内に登録します。 `Register` メソッドの `refreshAll` パラメーターは、登録されているキーが変更された場合に、すべての構成値を更新する必要があることを示します。 `SetCacheExpiration` メソッドでは、構成の変更を確認するために新しい要求が App Configuration に対して行われるまでに経過する必要がある最小時間を指定します。 この例では、代わりに 5 分を指定し、既定の有効期限である 30 秒をオーバーライドしています。 これにより、App Configuration ストアに対して行われる可能性のある要求の数が少なくなります。

    > [!NOTE]
    > テスト目的で、キャッシュ更新の有効期限をもっと短くすることもできます。

    構成の更新を実際にトリガーするには、App Configuration ミドルウェアを使用します。 この点については、後続の手順で説明します。

1. 新しい `Settings` クラスを定義および実装する *Settings.cs* ファイルを Controllers ディレクトリに追加します。 名前空間を自分のプロジェクト名に置き換えます。 

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

1. *Startup.cs* を開き、`ConfigureServices` メソッドを更新します。 `Configure<Settings>` を呼び出して、構成データを `Settings` クラスにバインドします。 `AddAzureAppConfiguration` を呼び出して、App Configuration コンポーネントをアプリケーションのサービス コレクションに追加します。

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---

1. `Configure` メソッドを更新し、`UseAzureAppConfiguration` への呼び出しを追加します。 これにより、アプリケーションで App Configuration ミドルウェアを使用して、自動的に構成の更新を処理できます。

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    > [!NOTE]
    > App Configuration ミドルウェアによって、前の手順の `ConfigureRefresh` 呼び出しで更新するために登録したセンチネル キーまたは他のキーが監視されます。 ミドルウェアは、アプリケーションへの受信要求ごとにトリガーされます。 ただし、ミドルウェアでは、設定したキャッシュの有効期限が過ぎたときに App Configuration で値を確認する要求のみを送信します。 変更が検出されると、センチネル キーが使用されている場合はすべての構成が更新されるか、登録済みのキーの値のみが更新されます。
    > - App Configuration に対する変更の検出の要求が失敗した場合、アプリケーションではキャッシュされた構成が引き続き使用されます。 構成されたキャッシュの有効期限が再び経過し、アプリケーションへの新しい受信要求があるときに、別の確認が行われます。
    > - 構成の更新は、アプリケーションの受信要求の処理に対して非同期的に行われます。 更新をトリガーした受信要求をブロックしたり、速度が低下することはありません。 更新をトリガーした要求では、更新された構成値が取得されない場合がありますが、それ以降の要求では行われます。
    > - ミドルウェアが確実にトリガーされるようにするには、要求パイプラインでできるだけ早く `app.UseAzureAppConfiguration()` を呼び出し、アプリケーションで別のミドルウェアがそれを迂回しないようにします。

## <a name="use-the-latest-configuration-data"></a>最新の構成データを使用する

1. Controllers ディレクトリにある *HomeController.cs* を開いて、`Microsoft.Extensions.Options` パッケージへの参照を追加します。

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. `HomeController` クラスを更新して、依存関係の挿入を通じて `Settings` を受け取り、その値を利用するようにします。

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```
    ---
    > [!Tip]
    > 構成値を読み取る際のオプション パターンの詳細については、「[ASP.NET Core のオプション パターン](/aspnet/core/fundamentals/configuration/options)」を参照してください。

3. Views の Home ディレクトリにある *Index.cshtml* を開いて、内容を次のスクリプトに置き換えます。

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```console
        dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```console
        dotnet run
    ```

1. ブラウザー ウィンドウを開いて、`dotnet run` 出力に表示される URL に移動します。

    ![クイックスタート アプリをローカルで起動する](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[構成エクスプローラー]** を選択し、次のキーの値を更新します。 最後に必ずセンチネル キーを更新してください。

    | キー | 値 |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Data from Azure App Configuration - now with live updates! |
    | TestApp:Settings:Sentinel | 2 |

1. ブラウザー ページを最新の情報に更新して新しい構成設定を確認します。 変更を反映するために複数回の更新が必要になる場合があります。または、キャッシュの有効期限を 5 分未満に変更します。 

    ![更新されたクイックスタート アプリをローカルで起動する](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように ASP.NET Core Web アプリを設定しました。 App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
