---
title: Windows ASP.NET Core アプリを構成する
description: App Service のネイティブ Windows インスタンスで ASP.NET Core アプリを構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907995"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Azure App Service 向けの Windows ASP.NET Core アプリを構成する

> [!NOTE]
> .NET Framework の ASP.NET については、「[Azure App Service 向けの ASP.NET アプリを構成する](configure-language-dotnet-framework.md)」を参照してください

ASP.NET Core アプリは、コンパイル済みバイナリとして Azure App Service にデプロイする必要があります。 Visual Studio 発行ツールではソリューションがビルドされてからコンパイル済みバイナリが直接デプロイされますが、App Service 展開エンジンではまずコード リポジトリがデプロイされ、その後にバイナリがコンパイルされます。 Linux アプリについては、「[Azure App Service 向けの Linux ASP.NET Core アプリを構成する](containers/configure-language-dotnetcore.md)」を参照してください。

このガイドでは、ASP.NET Core 開発者向けに主要な概念と手順を説明します。 Azure App Service を初めて使用する場合は、まず [ASP.NET クイックスタート](app-service-web-get-started-dotnet.md)と [SQL Database を使った ASP.NET Core のチュートリアル](app-service-web-tutorial-dotnetcore-sqldb.md)に従ってください。

## <a name="show-supported-net-core-runtime-versions"></a>サポートされている .NET Core ランタイム バージョンを表示する

App Service でサポートされているすべての .NET Core バージョンが Windows インスタンスに既にインストールされています。 使用可能な .NET Core ランタイムと SDK のバージョンを表示するには、`https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動し、ブラウザーベースのコンソールで次のコマンドを実行します。

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>.NET Core バージョンを設定する

ASP.NET Core プロジェクトのプロジェクト ファイルにターゲット フレームワークを設定します。 詳細については、.NET Core ドキュメントの「[使用する .NET Core バージョンを選択する](https://docs.microsoft.com/dotnet/core/versions/selection)」を参照してください。

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](configure-common.md#configure-app-settings)できます。 次に、標準の ASP.NET Core 依存関係の挿入パターンを使用して、任意のクラスでこれらにアクセスできます。

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

たとえば、App Service と *appsettings.json* で同じ名前のアプリ設定を構成した場合は、App Service の値が *appsettings.json* の値よりも優先されます。 ローカルの *appsettings.json* 値ではアプリをローカルでデバッグできますが、App Service の値では実稼働設定の製品内でアプリを実行できます。 接続文字列は同じように機能します。 これにより、コード リポジトリの外部にアプリケーション シークレットを保存し、コードを変更することなく適切な値にアクセスできます。

> [!NOTE]
> *appsettings. json* の[階層型の構成データ](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data)は、.NET Core に標準の `:` 区切り記号を使用してアクセスされることに注意してください。 App Service で特定の階層型構成設定をオーバーライドするには、キーにアプリ設定名を同じ区切り形式で設定します。 [Cloud Shell](https://shell.azure.com) で次の例を実行できます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>マルチ プロジェクト ソリューションをデプロイする

Visual Studio ソリューションに複数のプロジェクトが含まれている場合、Visual Studio の発行プロセスには、デプロイするプロジェクトの選択が既に含まれています。 ビルド自動化を有効にした状態で、Git や ZIP デプロイなどを使用して App Service 展開エンジンにデプロイすると、App Service 展開エンジンは、App Service アプリとして検出された最初の Web サイトまたは Web アプリケーション プロジェクトを選択します。 `PROJECT` アプリ設定を指定することで、App Service で使用するプロジェクトを指定できます。 たとえば、[Cloud Shell](https://shell.azure.com) で以下を実行します。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

ASP.NET Core には、[App Service 用の組み込みのログ プロバイダー](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service)が用意されています。 プロジェクトの *Program.cs* で、次の例に示すように、`ConfigureLogging` 拡張メソッドを使用してそのプロバイダーをアプリケーションに追加します。

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

その後、[標準の .NET Core パターン](https://docs.microsoft.com/aspnet/core/fundamentals/logging)を使用して、ログを構成して生成できます。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

App Service での ASP.NET Core アプリのトラブルシューティングの詳細については、「[Azure App Service と IIS での ASP.NET Core のトラブルシューティング](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis)」を参照してください

## <a name="get-detailed-exceptions-page"></a>例外の詳細ページを表示する

Visual Studio デバッガーで ASP.NET Core アプリの実行中に例外が発生すると、ブラウザーに例外の詳細ページが表示されますが、App Service ではそのページの代わりに汎用の **HTTP 500** エラーが表示されるか、「**要求の処理中にエラーが発生しました。** 」 メッセージが表示されます。 App Service で例外の詳細ページを表示するには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを実行してアプリ設定 `ASPNETCORE_ENVIRONMENT` をアプリに追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されるかどうかをアプリのロジックで認識する必要がある場合は、*Startup.cs* で Forwarded Headers Middleware を構成します。

- `Startup.ConfigureServices` で、`X-Forwarded-For` ヘッダーと `X-Forwarded-Proto` ヘッダーを転送するように [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) を使ってミドルウェアを構成します。
- ミドルウェアが App Service のロード バランサーを信頼できるようにするために、既知のネットワークにプライベート IP アドレス範囲を追加します。
- 他のミドルウェアを呼び出す前に、`Startup.Configure` 内で [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) メソッドを呼び出します。

3 つの要素をすべてまとめると、次の例のようなコードになります。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:ASP.NET Core アプリと SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
