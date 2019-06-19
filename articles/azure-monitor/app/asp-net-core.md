---
title: Azure Application Insights for ASP.NET Core | Microsoft Docs
description: ASP.NET Core Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: 2da253c058329b80c6175b9066b76816940dc3d5
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154006"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights for ASP.NET Core アプリケーション

この記事では、[ASP.NET Core](https://docs.microsoft.com/aspnet/core) アプリケーションで Application Insights を有効にする方法について説明します。 この記事の手順を完了すると、Application Insights は、ASP.NET Core アプリケーションから要求、依存関係、例外、パフォーマンス カウンター、ハートビート、ログの収集を開始します。 サンプル アプリケーションは `netcoreapp2.2` をターゲットとする [MVC Application](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) ですが、これらの手順はすべての ASP.NET Core アプリケーションに適用されます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

[Application Insights SDK (ソフトウェア開発キット) for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) により、アプリケーションを実行する場所と方法に関係なく、アプリケーションの監視が可能になります。 アプリケーションが実行されていて、Azure へのネットワーク接続がある場合は、テレメトリを収集することができます。 つまり、Application Insights の監視は、.NET Core がサポートされているすべての場所でサポートされます。 このサポートには、あらゆるオペレーティング システム (Windows、Linux、Mac)、ホスティング方法 (インプロセスまたはアウトオブプロセス)、デプロイ方法 (フレームワーク依存型または自己完結型)、Web サーバー (IIS、Kestrel)、ホスティング プラットフォーム (Azure Web アプリ、Azure VM、Docker、Azure Kubernetes Service (AKS) など)、または IDE (Visual Studio、VS Code、コマンド ライン) が含まれます。

## <a name="prerequisites"></a>前提条件

- 機能している ASP.NET Core アプリケーション。 必要な場合、[ASP.NET Core の概要ガイド](https://docs.microsoft.com/aspnet/core/getting-started/)に従って ASP.NET Core アプリケーションを作成します。
- Application Insights の有効なインストルメンテーション キー。これは Application Insights サービスにテレメトリを送信するために必要です。 必要な場合、[リソースの作成手順](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)に従って、新しい Application Insights リソースを作成し、インストルメンテーション キーを取得してください。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights のサーバー側テレメトリを有効にする (Visual Studio)

1. Visual Studio でプロジェクトを開きます。

    > [!TIP]
    > 必須の手順ではありませんが、プロジェクトのソース管理を設定すると、Application Insights によって行われたすべての変更を追跡することができるので役に立つ場合があります。 ソース管理を有効にするには、 **[ファイル]**  >  **[ソース管理に追加]** を選択します。

2. **[プロジェクト]**  >  **[Application Insights Telemetry の追加]** を選択します

3. **[開始]** を選択します。 (このテキストは、お使いの Visual Studio のバージョンに応じて多少異なる場合があります。 以前のバージョンの一部には、この代わりに **[Start Free]** (自由に開始) ボタンがあります。)

4. サブスクリプションを選択し、 **[リソース]**  >  **[登録]** を選択します。

5. Application Insights をプロジェクトに追加したら、SKD の最新の安定リリースを使用していることを確認します。 **[プロジェクト]**  >  **[NuGet パッケージの管理]**  >  **[Microsoft.ApplicationInsights.AspNetCore]** に移動し、必要であれば **[更新]** を選択します。

     ![Application Insights パッケージが更新のために選択されている、[NuGet パッケージの管理] 画面のスクリーンショット](./media/asp-net-core/update-nuget-package.png)

6. 省略可能なヒントに従ってプロジェクトをソース管理に追加した場合は、次に、 **[ビュー]**  >  **[チーム エクスプローラー]**  >  **[変更]** に移動して、Application Insights テレメトリの追加から行われた変更の差分ビュー用の個別のファイルを選択することができます。

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Application Insights のサーバー側テレメトリを有効にする (Visual Studio なし)

1. [ASP.NET Core 用の Application Insights SDK NuGet パッケージ](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)をインストールします。 常に最新の安定バージョンを使用することをお勧めします。 SDK の完全なリリース ノートは、[オープンソースの GitHub リポジトリ](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)にあります。

    次のスニペットは、プロジェクトの `.csproj` ファイルに追加される変更を示しています。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. `Startup` クラスで `ConfigureServices()` メソッドに `services.AddApplicationInsightsTelemetry();` を追加します。 完全なサンプルは下のようになります。

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. インストルメンテーション キーを設定します。

    インストルメンテーション キーを `AddApplicationInsightsTelemetry` に引数として指定することはできますが、インストルメンテーション キーは構成に指定することをお勧めします。 `appsettings.json` でインストルメンテーション キーを指定する方法は次のようになります。 公開時、`appsettings.json` がアプリケーションのルート フォルダーにコピーされるようにします。

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    あるいは、次のいずれかの環境変数にインストルメンテーション キーを指定することもできます。

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    例:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` は通常、Azure Web Apps に展開されるアプリケーションのインストルメンテーション キーを指定するために使用されます。

    > [!NOTE]
    > コードに指定されたインストルメンテーション キーは、他のオプションより優先される環境変数 `APPINSIGHTS_INSTRUMENTATIONKEY` より優先されます。

## <a name="run-your-application"></a>アプリケーションを実行する

 アプリケーションを実行し、それに要求を行います。 これで Application Insights にテレメトリが送られるはずです。 次のテレメトリは Application Insights SDK によって自動的に収集されます。

|要求/依存関係 |詳細|
|---------------|-------|
|Requests | アプリケーションへの受信 Web 要求。 |
|Http/Https | `HttpClient` で行われる呼び出し。 |
|SQL | `SqlClient` で行われる呼び出し。 |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage Client で行われる呼び出し。 |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | バージョン 1.1.0 以上。 |
|[ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| バージョン 3.0.0 以上。 |
|Azure Cosmos DB | HTTP/HTTPS が使用されている場合にのみ、自動的に追跡されます。 TCP モードは、Application Insights ではキャプチャされません。 |

### <a name="performance-counters"></a>パフォーマンス カウンター

ASP.NET Core での[パフォーマンス カウンター](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)のサポートは次に限定されています

   * SDK バージョン 2.4.1 以上では、アプリケーションが Azure Web アプリ (Windows) で実行されている場合、パフォーマンス カウンターが収集されます。
   * SDK バージョン 2.7.0-beta3 以上では、アプリケーションが Windows で実行されており、`NETSTANDARD2.0` 以降をターゲットにしている場合、パフォーマンス カウンターが収集されます。
   * .NET Framework を対象とするアプリケーションの場合、すべてのバージョンの SDK でパフォーマンス カウンターがサポートされます。
   * この記事は、Linux でパフォーマンス カウンター サポートが追加されるときに更新されます。

### <a name="ilogger-logs"></a>ILogger ログ

SDK バージョン 2.7.0-beta3 以降から、重大度が `Warning` 以上の [ILogger ログ](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)は自動的に取り込まれます。

### <a name="live-metrics"></a>ライブ メトリック

テレメトリがポータルに表示され始めるまでには数分かかることがあります。 すべてが動作しているかどうかを簡単に確認するには、実行中のアプリケーションに要求を行っているとき、[ライブ メトリック](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)を使用することをお勧めします。

## <a name="enable-client-side-telemetry-for-web-applications"></a>Web アプリケーションに対してクライアント側のテレメトリを有効にする

サーバー側テレメトリの収集を始めるには、上記の手順で十分です。 お使いのアプリケーションにクライアント側コンポーネントがある場合は、下記の手順に従って、そこから[使用状況テレメトリ](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)の収集を始めます。

1. `_ViewImports.cshtml` で、インジェクションを追加します。

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. `_Layout.cshtml` で、`<head>` セクションの終わりに (ただし、他のスクリプトの前に) HtmlHelper を挿入します。 ページから報告するカスタム JavaScript テレメトリがあれば、このスニペットの後に挿入します。

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

上で参照されている `.cshtml` ファイル名は、既定の MVC アプリケーション テンプレートからのものです。 最終的に、アプリケーションに対するクライアント側の監視を正しく有効にするためには、JavaScript スニペットが、監視するアプリケーションの各ページの `<head>` セクションにある必要があります。 このアプリケーション テンプレートでは、Javascript スニペットを `_Layout.cshtml` に追加すると、実質的にこの目標が達成されます。 プロジェクトにこの特定のファイルがない場合でも、[クライアント側の監視](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)を追加することができます。 必要なことは、アプリ内のすべてのページの `<head>` を制御する同等のファイルに JavaScript を追加するだけです。または、スニペットを複数の個別のページに追加することもできますが、これは保守が困難であり、一般的には推奨されません。

## <a name="configuring-application-insights-sdk"></a>Application Insights SDK の構成

Application Insights SDK for ASP.NET Core をカスタマイズし、既定の構成を変更できます。 Application Insights ASP.NET SDK のユーザーであれば、`ApplicationInsights.config` を使用する構成または `TelemetryConfiguration.Active` の変更に慣れている場合もあります。 ASP.NET Core の場合、構成は別の方法で行われます。 ASP.NET Core SDK はアプリケーションに追加され、ASP.NET Core の組み込み[依存関係インジェクション](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を使用して構成されます。 別途明記されていない限り、構成変更はほとんどすべて、`Startup.cs` クラスの `ConfigureServices()` メソッドで行われます。 下のセクションを進めると詳細についてご覧いただけます。

> [!NOTE]
>  `TelemetryConfiguration.Active` を変更することによる構成の変更は、ASP.NET Core アプリケーションでは推奨されません。

### <a name="configuring-using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions を使用して構成する

`ApplicationInsightsServiceOptions` を `AddApplicationInsightsTelemetry` に渡す方法でいくつかの共通設定を変更できます。 次に例を示します。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

`ApplicationInsightsServiceOptions` で構成できる設定の一覧は[こちら](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)で確認できます。

### <a name="sampling"></a>サンプリング

Application Insights SDK for ASP.NET Core では FixedRate および Adaptive サンプリングがサポートされています。 アダプティブ サンプリングは、既定で有効になっています。 ASP.NET Core アプリケーションのサンプリングを構成する方法については、[アダプティブ サンプリングのガイダンス](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)に従ってください。

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers の追加

[テレメトリ初期化子](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)は、すべてのテレメトリで送信されるグローバル プロパティを定義するときに使用されます。

新しい `TelemetryInitializer` を追加するには、下の画像のように DependencyInjection コンテナーにそれを追加します。 `TelemetryInitializer` は DependencyInjection コンテナーに追加されますが、SDK によって自動的に選択されます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers の削除

既定で存在するすべてまたは特定の TelemetryInitializers を削除するには、`AddApplicationInsightsTelemetry()` の呼び出し**後**に次のサンプル コードを使用します。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>TelemetryProcessors の追加

拡張メソッド `AddApplicationInsightsTelemetryProcessor` を `IServiceCollection` で使用することで、カスタム テレメトリ プロセッサを `TelemetryConfiguration` に追加できます。 テレメトリ プロセッサは、[高度なフィルター処理シナリオ](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)で使用されて、Application Insights サービスに送信するテレメトリに何を含めて何を除外するかを、より直接的に制御できるようにします。 次の例を使用してください。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>既定の TelemetryModules の構成または削除

Application Insights は、追加構成を必要とせずに特定のワークロードに関する[有用な情報を自動的に収集する](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies)方法としてテレメトリ モジュールを使用します。

次の自動収集モジュールは既定で有効になっており、テレメトリの自動収集を担います。 これらのモジュールは無効にしたり、既定の動作を変更したりできます。

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

既定の `TelemetryModule` を構成するには、下の画像のように、拡張メソッド `ConfigureTelemetryModule<T>` を `IServiceCollection` で使用します。

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>テレメトリ チャネルの構成

使用される既定のチャネルは `ServerTelemetryChannel` です。 これは次のサンプルに従うことでオーバーライドできます。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>自動収集されるテレメトリ以外の追加のテレメトリを追跡したいと考えています。 どうすればよいですか?

Constructor インジェクションを使用して `TelemetryClient` のインスタンスを取得し、そのインスタンスで必須の `TrackXXX()` メソッドを呼び出します。 ASP.NET Core アプリケーションで新しい `TelemetryClient` インスタンスを作成することは推奨されません。`TelemetryClient` のシングルトン インスタンスは、残りのテレメトリと `TelemetryConfiguration` を共有する DI コンテナーに既に登録されているためです。 新しい `TelemetryClient` インスタンスの作成は、残りのテレメトリとは別の構成を与える必要がある場合にのみ推奨されます。 次の例では、コントローラーから追加のテレメトリを追跡する方法を確認できます。

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Application Insights のカスタム データ レポートについては、[Application Insights カスタム メトリック API リファレンス](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)に関するページを参照してください。

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>一部の Visual Studio テンプレートでは、Application Insights を有効にする目的で UseApplicationInsights() 拡張メソッドが IWebHostBuilder で使用されていました。 この使用方法は今でも有効ですか?

このメソッドで Application Insights を有効にすることは可能であり、Visual Studio オンボーディングや Azure Web アプリ拡張で使用されています。 ただし、`services.AddApplicationInsightsTelemetry()` を使用することをお勧めします。一部の構成を制御するオーバーロードが与えられます。 いずれのメソッドでも内部的には同じことが行われます。したがって、適用するカスタム構成がない場合は、どちらを呼び出しても構いません。

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET Core アプリケーションを Azure Web アプリにデプロイしています。 Application Insights 拡張を Web アプリから有効にできますか?

この記事に示されているように、SDK がビルド時にインストールされる場合は、App Service ポータルから Application Insights の拡張機能を有効にする必要はありません。 拡張機能はインストールされていても、既に SDK がアプリケーションに追加されていることが検出されると、バックオフします。 拡張機能から Application Insights を有効にする場合は、SDK のインストールおよび更新は必要ありません。 ただし、下記の理由から、この記事のように Application Insights を有効にする方法のほうが自由度が高くなります。
   * Application Insights Telemetry は以下で引き続き機能します。
       * すべてのオペレーティング システム - Windows、Linux、Mac。
       * すべての発行モード - 自己完結型またはフレームワーク依存型。
       * 完全 .NET Framework を含む、すべてのターゲット フレームワーク。
       * すべてのホスティング オプション - Azure Web アプリ、VM、Linux、コンテナー、AKS、Azure 以外。
   * Visual Studio からデバッグしているとき、テレメトリはローカルで表示できます。
   * `TrackXXX()` API を使用し、追加のカスタム テレメトリを追跡できます。
   * 構成を完全に制御できます。

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Status Monitor などのツールを利用して Application Insights 監視を有効にできますか?

いいえ。 現在、[Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) とその後継バージョンである [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) では、ASP.NET 4.x のみがサポートされます。

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>ASP.NET Core 2.0 アプリケーションを持っています。 何もしなくても Application Insights は自動的に有効になりますか。

`Microsoft.AspNetCore.All` 2.0 メタパッケージには、Application Insights SDK (バージョン 2.1.0) が含まれていました。そして、アプリケーションを Visual Studio デバッガーの下で実行すると、Visual Studio は Application Insights を有効にし、テレメトリを IDE 自体にローカルに表示します。 テレメトリは、インストルメンテーション キーが明示的に指定されない限り、Application Insights サービスに送信されませんでした。 2\.0 アプリについても、この記事の指示に従って Application Insights を有効にすることをお勧めします。

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Linux でアプリケーションを実行しています。 Linux でもすべての機能がサポートされていますか?

* はい。 SDK の機能サポートは、次の例外を除き、すべてのプラットフォームで同じです。

    * Windows 以外ではパフォーマンス カウンターがまだサポートされていません。
    * `ServerTelemetryChannel` が既定で有効になっていても、アプリケーションが Linux または MacOS で実行されている場合は、このチャネルが、ネットワークに問題がある場合にテレメトリを一時的に保持するするために、自動的にローカル ストレージ フォルダーを作成することはありません。 この制限に起因し、ネットワークやサーバーに一時的に問題が発生すると、テレメトリが失われます。 下の画像のように、ユーザーがチャネル用のローカル フォルダーを構成することがこの問題の回避策です。

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>オープンソース SDK
[コードを読んで協力してください。](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>ビデオ

- 最初から [.NET Core と Visual Studio を使って Application Insights を構成する](https://www.youtube.com/watch?v=NoS9UhcR4gA&t)手順に関するステップ バイ ステップの外部ビデオ。
- 最初から [.NET Core と Visual Studio Code を使って Application Insights を構成する](https://youtu.be/ygGt84GDync)手順に関するステップ バイ ステップの外部ビデオ。

## <a name="next-steps"></a>次の手順
* [ユーザー フローの探索](../../azure-monitor/app/usage-flows.md): ユーザーがアプリ内をどのように移動しているかを把握します。
* [スナップショット コレクションを構成](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)して、例外がスローされたときのソース コードと変数の状態を確認します。
* [API の使用](../../azure-monitor/app/api-custom-events-metrics.md) : アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。
* [可用性テスト](../../azure-monitor/app/monitor-web-app-availability.md)の使用: 世界中からアプリを常にチェックします。
