---
title: Azure App Configuration の動的な構成を ASP.NET Core アプリで使用するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、ASP.NET Core アプリの構成データを動的に更新する方法を学習します。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 44ae922b182874eef378d4868fb278c3c76252db
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884415"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリで動的な構成を使用する

ASP.NET Core には、さまざまなソースから構成データを読み取るほか、アプリケーションを再起動することなく即座に変更を処理することができるプラグ可能な構成システムがあります。 厳密に型指定された .NET クラスに構成設定をバインドし、さまざまな `IOptions<T>` パターンを使用してそれらをコードに挿入することがサポートされています。 そうしたパターンの 1 つである `IOptionsSnapshot<T>` では、基になるデータが変化したときに、アプリケーションの構成が自動的にリロードされます。 アプリケーションのコントローラーに `IOptionsSnapshot<T>` を挿入すれば、Azure App Configuration に格納されている最新の構成にアクセスすることができます。 また、自分が定義した間隔で定期的にポーリングしてアプリ構成ストアにおける変更を常時監視して取得するように App Configuration ASP.NET Core クライアント ライブラリを設定することもできます。

このチュートリアルでは、自分が作成するコードに、構成の動的更新を実装する方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このクイック スタートの手順は、任意のコード エディターを使用して実行できます。 ただし、推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * アプリ構成ストアへの変更に合わせて構成を更新するようにアプリケーションを設定する
> * アプリケーションのコントローラーに最新の構成を挿入する

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>App Configuration からデータを再度読み込む

1. *Program.cs* を開き、`config.AddAzureAppConfiguration()` メソッドを追加して `CreateWebHostBuilder` メソッドを更新します。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    `.Watch` メソッドの第 2 パラメーターはポーリング間隔です。ASP.NET クライアント ライブラリは、この間隔でアプリ構成ストアを照会し、特定の構成設定に変更が加えられているかどうかを確認します。

2. 新しい `Settings` クラスを定義して実装する *Settings.cs* ファイルを追加します。

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

3. *Startup.cs* を開き、構成データを `Settings` クラスにバインドするよう `ConfigureServices` メソッドを更新します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>最新の構成データを使用する

1. *Controllers* ディレクトリにある *HomeController.cs* を開き、`HomeController` クラスを更新します。依存関係の挿入を通じて `Settings` を受け取り、その値を利用するようにしてください。

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

2. *Views* > *Home* ディレクトリにある *Index.cshtml* を開いて、その内容を次のように置き換えます。

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
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

        dotnet build

2. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

        dotnet run

3. ブラウザー ウィンドウを起動して `http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. [Azure portal](https://aka.ms/azconfig/portal) にサインインし、**[すべてのリソース]** をクリックして、クイック スタートで作成したアプリ構成ストアのインスタンスをクリックします。

5. **[キー/値のエクスプローラー]** をクリックして次のキーの値を更新します。

    | キー | 値 |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Data from Azure App Configuration - now with live updates! |

6. ブラウザー ページを最新の情報に更新して新しい構成設定を確認します。

    ![クイック スタートのアプリ (ローカルで最新の情報に更新)](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure マネージド サービス ID を追加して、App Configuration へのアクセスを効率化し、アプリの資格情報管理を改善しました。 App Configuration の使用の詳細については、Azure CLI のサンプルに進んでください。

> [!div class="nextstepaction"]
> [CLI のサンプル](./cli-samples.md)
