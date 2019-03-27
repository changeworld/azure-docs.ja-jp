---
title: ILogger を使用して Azure Application Insights 内の .NET トレース ログを探索する | Microsoft Docs
description: ASP.NET Core とコンソール アプリケーションでの Azure Application Insights の ILogger の実装の使用例。
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: c456f8f7f08fdbd0020bfc49ceeec262fa0ac773
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458179"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core では、さまざまな組み込みのサードパーティ製のログ記録プロバイダーと連携するログ記録 API がサポートされています。 この記事では、コンソールと ASP.NET Core アプリケーションの両方で Application Insights の ILogger の実装を使用してログ記録を処理する方法を示します。 ILogger ベースのログ記録の詳細については、[こちらの記事](https://docs.microsoft.com/aspnet/core/fundamentals/logging)をご覧ください。

## <a name="console-application"></a>コンソール アプリケーション

ILogger のトレースを Application Insights に送信するように構成されたコンソール アプリケーションの例を次に示します。

インストール済みパッケージ:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>ASP.NET Core アプリケーション

ILogger のトレースを Application Insights に送信するように構成された ASP.NET Core アプリケーションの例を次に示します。 この例は、Program.cs、Startup.cs、またはその他のコントローラー/アプリケーション ロジックから ILogger トレースを送信するために使用できます。

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up up by Application Insights.   
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked upby Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

上記の両方の例で、スタンドアロン パッケージである Microsoft.Extensions.Logging.ApplicationInsights が使用されています。 既定では、この構成では、Application Insights にデータを送信するために、必要最小限の `TelemetryConfiguration` が使用されます。 必要最小限とは、使用されるチャネルは `InMemoryChannel` であり、サンプリングも標準 TelemetryInitializer も実行されないことを意味します。 この動作は、コンソール アプリケーションでは、次の例に示すようにオーバーライドできます。

次の追加パッケージをインストールします。

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

次のセクションで、既定の `TelemetryConfiguration` をオーバーライドする方法を示します。 次の例では、`ServerTelemetryChannel`、サンプリング、およびカスタム `ITelemetryInitializer` を構成しています。

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {                            
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

上記の方法を ASP.NET Core アプリケーションで使用できますが、より一般的な方法は、次に示すように、通常のアプリケーションの監視 (要求や依存関係など) と ILogger のキャプチャを組み合わせることです。

次の追加パッケージをインストールします。

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

以下を `ConfigureServices` メソッドに追加します。 このコードによって、通常のアプリケーションの監視を既定の構成 (ServerTelemetryChannel、LiveMetrics、要求/依存関係、相関関係など) で実行できます。

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

この例では、`ApplicationInsightsLoggerProvider` によって使用される構成は、通常のアプリケーションの監視で使用されるものと同じです。 そのため、`ILogger` のトレースと他のテレメトリ (要求や依存関係など) の両方で `TelemetryInitializers`、`TelemetryProcessors`、および `TelemetryChannel` の同じセットが実行されます。 これらは相互に関連付けられ、同じ方法でサンプリングされるか、またはサンプリングされません。

ただし、この動作には例外があります。 `Program.cs` または `Startup.cs` 自体から何らかのログが記録される場合、既定の `TelemetryConfiguration` は完全に設定されないため、これらのログには既定の構成は設定されません。 ただし、それ以外のすべてのログ (たとえば、コントローラーやモデルからのログ) では、構成が共有されます。

## <a name="next-steps"></a>次の手順

各項目の詳細情報

- [ILogger ベースのログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [.NET トレース ログ](../../azure-monitor/app/asp-net-trace-logs.md)
