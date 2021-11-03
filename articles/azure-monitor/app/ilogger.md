---
title: .NET を使用した Application Insights のログ
description: .NET の ILogger インターフェイスで Application Insights を使用する方法について説明します。
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 63ffa404c5f36bbb9bddfd86fd275f4bb740a66a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079037"
---
# <a name="application-insights-logging-with-net"></a>.NET を使用した Application Insights のログ

この記事では、いくつかの NuGet パッケージを使用して .NET アプリの Application Insights でログをキャプチャする方法について説明します。

- **コア パッケージ:**
  - [`Microsoft.Extensions.Logging.ApplicationInsights`][nuget-ai]
- **ワークロード パッケージ:**
  - [`Microsoft.ApplicationInsights.AspNetCore`][nuget-ai-anc]
  - [`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`][nuget-ai-ws-tc]

[nuget-ai]: https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights
[nuget-ai-anc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore
[nuget-ai-ws]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService
[nuget-ai-ws-tc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel

> [!TIP]
> [`Microsoft.ApplicationInsights.WorkerService`][nuget-ai-ws] NuGet パッケージは、この記事の範囲外です。 これは、バックグラウンド サービスの Application Insights を有効にするために使用できます。 詳細については、[ワーカー サービス アプリケーション向け Application Insights](./worker-service.md) に関するページを参照してください。

使用する Application Insights ログ パッケージに応じて、`ApplicationInsightsLoggerProvider` を登録するためのさまざまな方法があります。 `ApplicationInsightsLoggerProvider` は <xref:Microsoft.Extensions.Logging.ILoggerProvider> の実装であり、<xref:Microsoft.Extensions.Logging.ILogger> および <xref:Microsoft.Extensions.Logging.ILogger%601> の実装を提供する役割を担っています。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

Application Insights テレメトリをに ASP.NET Core アプリケーションに追加するには、`Microsoft.ApplicationInsights.AspNetCore` NuGet パッケージを使用します。 これは、[Visual Studio を介して接続済みサービスとして](/visualstudio/azure/azure-app-insights-add-connected-service)構成することも、手動で構成することもできます。

既定では、[コード](./asp-net-core.md)または[コードレス](./azure-web-apps-net-core.md#enable-agent-based-monitoring)のアプローチを使用して構成された ASP.NET Core アプリケーションには、Application Insights ログ プロバイダーが登録されています。 登録されているプロバイダーは、重大度が <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 以上のログ イベントを自動的にキャプチャするように構成されています。 重大度とカテゴリをカスタマイズできます。 詳細については、「[ログ記録レベル](#logging-level)」を参照してください。

1. NuGet パッケージがインストールされていることを確認してください。

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.17.0" />
    </ItemGroup>
   ```

1. `Startup.ConfigureServices` メソッドが `services.AddApplicationInsightsTelemetry` を呼び出していることを確認します。

    ```csharp
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Microsoft.Extensions.Configuration;
    
    namespace WebApplication
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddApplicationInsightsTelemetry();
                // Configure the Connection String/Instrumentation key in appsettings.json
            }

            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                // omitted for brevity
            }
        }
    }
    ```

NuGet パッケージがインストールされ、プロバイダーが依存関係の挿入によって登録されているので、アプリをログに記録する準備ができました。 コンストラクターの挿入では、<xref:Microsoft.Extensions.Logging.ILogger> またはジェネリック型の代替である <xref:Microsoft.Extensions.Logging.ILogger%601> のいずれかが必要になります。 これらの実装が解決されると、`ApplicationInsightsLoggerProvider` によってこれらが提供されるようになります。 ログに記録されたメッセージや例外は、Application Insights に送信されます。 

たとえば、次のコントローラーの例を考えてみましょう。

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");

        return new string[] { "value1", "value2" };
    }
}
```

詳細については、[ASP.NET Core のログ記録](/aspnet/core/fundamentals/logging)に関する記事をご覧ください。

### <a name="capture-logs-within-aspnet-core-startup-code"></a>ASP.NET Core スタートアップ コード内のログをキャプチャする

いくつかのシナリオでは、アプリの起動ルーチンの一部として、要求 - 応答パイプラインで要求を受け入れる準備ができる前に、ログをキャプチャする必要があります。 しかし、`ILogger` の実装は、*Program.cs* や *Startup.cs* の依存関係の挿入からは簡単に利用できません。 詳細については、「[.NET でのログの記録: Main でログを作成する](/dotnet/core/extensions/logging?tabs=command-line#create-logs-in-main)」を参照してください。

*Program.cs* と *Startup.cs* からログを記録する場合に制限がいくつかあります。

* テレメトリは、[InMemoryChannel](./telemetry-channels.md) テレメトリ チャネルを介して送信されます。
* テレメトリには[サンプリング](./sampling.md)は適用されません。
* 標準の[テレメトリ初期化子またはプロセッサ](./api-filtering-sampling.md)は使用できません。

次の例では、*Program.cs* と *Startup.cs* を明示的にインスタンス化して構成することで、これを示しています。

#### <a name="example-programcs"></a>Program.cs の例

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;

namespace WebApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();

            var logger = host.Services.GetRequiredService<ILogger<Program>>();
            logger.LogInformation("From Program, running the host now.");

            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                })
                .ConfigureLogging((context, builder) =>
                {
                    // Providing an instrumentation key is required if you're using the
                    // standalone Microsoft.Extensions.Logging.ApplicationInsights package,
                    // or when you need to capture logs during application startup, such as
                    // in Program.cs or Startup.cs itself.
                    builder.AddApplicationInsights(
                        context.Configuration["APPLICATIONINSIGHTS_CONNECTION_STRING"]);

                    // Capture all log-level entries from Program
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Program).FullName, LogLevel.Trace);

                    // Capture all log-level entries from Startup
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Startup).FullName, LogLevel.Trace);
                });
    }
}
```

前のコードでは、`ApplicationInsightsLoggerProvider` は `"APPLICATIONINSIGHTS_CONNECTION_STRING"` 接続文字列を使用して構成されています。 フィルターが適用され、ログ レベルが <xref:Microsoft.Extensions.Logging.LogLevel.Trace?displayProperty=nameWithType> に設定されます。

> [!IMPORTANT]
> インストルメンテーション キーに対して[接続文字列](./sdk-connection-string.md?tabs=net)をお勧めします。 新しい Azure リージョンでは、インストルメンテーション キーの代わりに接続文字列を使用する *必要* があります。 
>
> 接続文字列により、テレメトリ データと関連付けるリソースが識別されます。 また、リソースでテレメトリの宛先として使用するエンドポイントを変更することもできます。 接続文字列をコピーし、アプリケーションのコードまたは環境変数に追加する必要があります。

#### <a name="example-startupcs"></a>Startup.cs の例

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace WebApplication
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddApplicationInsightsTelemetry();
            // Configure the Connection String/Instrumentation key in appsettings.json
        }

        // The ILogger<Startup> is resolved by dependency injection
        // and available in Startup.Configure.
        public void Configure(
            IApplicationBuilder app, IWebHostEnvironment env, ILogger<Startup> logger)
        {
            logger.LogInformation(
                "Configuring for {Environment} environment",
                env.EnvironmentName);

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGet("/", async context =>
                {
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }
    }
}
```

## <a name="console-application"></a>コンソール アプリケーション

インストールされているパッケージは次のとおりです。

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="5.0.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.17.0"/>
</ItemGroup>
```

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new InMemoryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(config => config.TelemetryChannel = channel);
                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by Delay, as required in console apps.
                // This ensures that even if the application terminates, telemetry is sent to the back end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}

```

前の例では、`Microsoft.Extensions.Logging.ApplicationInsights`パッケージが使用されています。 既定では、この構成により、Application Insights の `InMemoryChannel`チャネルにデータを送信するために、"必要最小限" の `TelemetryConfiguration` 設定が使用されます。 サンプリングも標準の `TelemetryInitializer` インスタンスもありません。 この動作は、コンソール アプリケーションでは、次の例に示すようにオーバーライドできます。

次の追加パッケージをインストールします。

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.17.0" />
```

次のセクションでは、<xref:Microsoft.Extensions.Options.ConfigureOptions%601.Configure(%600)> メソッドを使用して既定の `TelemetryConfiguration` 設定をオーバーライドする方法を示します。 この例では、`ServerTelemetryChannel` とサンプリングを設定します。 これによって、カスタム `TelemetryInitializer` インスタンスが `TelemetryConfiguration` に追加されます。

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new ServerTelemetryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(
                    config =>
                    {
                        config.TelemetryChannel = channel;

                        // Optional: implement your own TelemetryInitializer instance and configure it here
                        // config.TelemetryInitializers.Add(new MyTelemetryInitializer());

                        config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
                        channel.Initialize(config);
                    });

                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by Delay, as required in console apps.
                // This ensures that even if the application terminates, telemetry is sent to the back end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}
```

## <a name="logging-level"></a>ログ記録レベル

`ILogger` の実装には、[ログのフィルタリング](/dotnet/core/extensions/logging#how-filtering-rules-are-applied)を適用するための組み込みメカニズムがあります。 このフィルタリングにより、Application Insights プロバイダーなどの、各登録済みプロバイダーに送信されるログを制御できます。 フィルター処理は、構成 (たとえば、*appsettings.json* ファイルを使用) またはコードで使用できます。

次の例は、`ApplicationInsightsLoggerProvider` にフィルター ルールを適用する方法を示しています。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>appsettings.json を使用して構成でフィルター規則を作成する

`ApplicationInsightsLoggerProvider` には、"ApplicationInsights" という別名が付けられています。 *appsettings.json* の次のセクションでは、Application Insights の既定の <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> ログ レベルをオーバーライドし、"Microsoft" で始まるカテゴリを <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> 以上のレベルでログに記録するようにしています。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>コードでフィルター規則を作成する

次のコード スニペットでは、これらの項目に対して `ApplicationInsightsLoggerProvider` に送信されるログを構成します。

- すべてのカテゴリで <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 以上
- "Microsoft" で始まるカテゴリで <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> 以上

```csharp
Host.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(builder =>
    {
        builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Warning);
        builder.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Error);
    });
```


## <a name="logging-scopes"></a>ログのスコープ

`ApplicationInsightsLoggingProvider` では、[ログ スコープ](/dotnet/core/extensions/logging#log-scopes)がサポートされます。 スコープは既定で有効になっています。 

スコープの型が `IReadOnlyCollection<KeyValuePair<string,object>>` の場合、コレクション内のキーと値の各ペアが、カスタム プロパティとして、Application Insights テレメトリに追加されます。 次の例で、ログは `TraceTelemetry` としてキャプチャされ、プロパティに `("MyKey", "MyValue")` が含まれます。

```csharp
using (_logger.BeginScope(new Dictionary<string, object> { ["MyKey"] = "MyValue" }))
{
    _logger.LogError("An example of an Error level message");
}
```

他の型がスコープとして使用される場合は、Application Insights テレメトリのプロパティ `Scope` に格納されます。 次の例では、`TraceTelemetry` に、スコープを含む `Scope` と呼ばれるプロパティが設定されます。

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider の古いバージョンと新しいバージョンとは何ですか?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) には、組み込みの `ApplicationInsightsLoggerProvider` (`Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider`) インスタンスが含まれており、`ILoggerFactory` 拡張メソッドによって有効化されていました。 このプロバイダーは、バージョン 2.7.1 からは廃止とマークされています。 次のメジャー バージョンの変更で完全に削除される予定です。 

[Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) パッケージ自体は古くありません。 要求や依存関係などの項目の監視を有効にする必要があります。

代替として提案されるのは、新しいスタンドアロン パッケージ [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) です。これには、改善された `ApplicationInsightsLoggerProvider` インスタンス (`Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider`) と、有効化のために `ILoggerBuilder` に対する拡張メソッドが含まれています。

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.1 は新しいパッケージへの依存関係を取得し、`ILogger` のキャプチャを自動的に有効にします。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>一部の ILogger ログが Application Insights に 2 回表示されるのはなぜですか?

`ILoggerFactory` で `AddApplicationInsights` を呼び出して `ApplicationInsightsLoggerProvider` の以前の (古い) バージョンを有効にしている場合、重複が発生する可能性があります。 `Configure` メソッドに次のようなコードがあるかどうかを確認し、ある場合はそれを削除してください。

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio からデバッグすると二重のログ記録が発生する場合は、次のように、Application Insights を有効にするコードで `EnableDebugLogger` を `false` に設定します。 この重複と修正は、アプリケーションをデバッグするときにのみ関係します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions
    {
        EnableDebugLogger = false
    }
    services.AddApplicationInsightsTelemetry(options);
    // ...
}
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Microsoft.ApplicationInsights.AspNet SDK バージョン 2.7.1 に更新しており、ILogger からのログが自動的にキャプチャされます。 この機能を完全にオフにするにはどうすればよいですか?

ログ記録をフィルター処理する一般的な方法については、「[ログ記録レベル](#logging-level)」セクションをご覧ください。 `ApplicationInsightsLoggerProvider` をオフにするには、ログを構成するための呼び出しで `LogLevel.None` を使用します。 次のコマンドで `builder` は <xref:Microsoft.Extensions.Logging.ILoggingBuilder> です。

```csharp
builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.None);
```

*appsettings.json* ファイルの変更内容を次に示します。

```json
{
    "Logging": {
        "ApplicationInsights": {
            "LogLevel": {
                "Default": "None"
            }
        }
    }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>一部の ILogger ログのプロパティが他と同じではないのはなぜですか?

Application Insights では、他のすべてのテレメトリに使用されるのと同じ `TelemetryConfiguration`情報を使用して、`ILogger` ログのキャプチャと送信が行われます。 ただし、例外があります。 既定では、*Program.cs* または *Startup.cs* からログを記録するとき、`TelemetryConfiguration` は完全には設定されません。 これらの場所からのログには既定の構成がないため、すべての `TelemetryInitializer` インスタンスおよび `TelemetryProcessor` インスタンスは実行されません。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>スタンドアロン パッケージ Microsoft.Extensions.Logging.ApplicationInsights を使用しており、いくつかの追加カスタム テレメトリを手動でログに記録しようと考えています。 どうすればよいですか?

スタンドアロン パッケージを使用するとき、`TelemetryClient` は依存関係挿入 (DI) コンテナーに挿入されません。 次のコードに示すように、`TelemetryClient` の新しいインスタンスを作成し、ロガー プロバイダーが使用しているのと同じ構成を使用する必要があります。 これにより、カスタム テレメトリと、`ILogger` のテレメトリのすべてに、同じ構成が使われることが保証されます。

```csharp
public class MyController : ApiController
{
   // This TelemetryClient instance can be used to track additional telemetry through the TrackXXX() API.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> `Microsoft.ApplicationInsights.AspNetCore` パッケージを使用して Application Insights を有効にする場合は、このコードを変更して、コンストラクター内で直接 `TelemetryClient` を取得します。 例については、[こちらの FAQ](./asp-net-core.md#frequently-asked-questions) をご覧ください。

### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger ログからはどのような種類の Application Insights テレメトリが生成されますか? Application Insights ではどこで ILogger ログを見ることができますか?

`ApplicationInsightsLoggerProvider` は、`ILogger` ログをキャプチャし、それから `TraceTelemetry` を作成します。 `Exception` オブジェクトが `ILogger`の `Log` メソッドに渡されると、`TraceTelemetry` の代わりに `ExceptionTelemetry` が作成されます。 

これらのテレメトリ項目は、Azure portal、分析、Visual Studio ローカル デバッガーなど、Application Insights の他の `TraceTelemetry` 項目または `ExceptionTelemetry` 項目と同じ場所で見ることができます。

常に `TraceTelemetry` を送信する場合は、このスニペットを使用します。

```csharp
builder.AddApplicationInsights(
    options => options.TrackExceptionsAsExceptionTelemetry = false);
```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK をインストールせず、Azure Web Apps 拡張機能を使用して ASP.NET Core アプリケーションの Application Insights を有効にしています。 新しいプロバイダーを使用するにはどうすればよいですか? 

Azure Web Apps の Application Insights 拡張機能は、新しいプロバイダーを使用します。 フィルタリング規則は、アプリケーションの *appsettings.json* ファイルで変更することができます。

## <a name="next-steps"></a>次のステップ

* [.NET でのログの記録](/dotnet/core/extensions/logging)
* [ASP.NET Core でのログ記録](/aspnet/core/fundamentals/logging)
* [Application Insights での .NET トレース ログ](./asp-net-trace-logs.md)
