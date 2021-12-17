---
title: Azure Application Insights を利用して障害と例外を診断する
description: 要求テレメトリと共に ASP.NET アプリから例外を取り込みます。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/19/2021
ms.openlocfilehash: 5ad103890d481db18c3df9633bf3b58186136e93
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431552"
---
# <a name="diagnose-exceptions-in-web-apps-with-application-insights"></a>Application Insights を使用した Web アプリの例外の診断

Web アプリケーションの例外は、[Application Insights](./app-insights-overview.md) でレポートすることができます。 要求の失敗をクライアントとサーバーの両方の例外やその他のイベントに相互に関連付け、原因をすばやく診断できます。 この記事では、例外のレポート処理を設定したり、例外を明示的にレポートしたり、障害を診断したりする方法について説明します。

## <a name="set-up-exception-reporting"></a>例外のレポートを設定する

サーバーまたはクライアントで発生した例外をレポートするように Application Insights を設定することができます。 アプリケーションが依存しているプラットフォームによっては、適切な拡張機能または SDK が必要になります。

### <a name="server-side"></a>サーバー側

サーバー側のアプリケーションから例外をレポートさせるには、次のシナリオを考慮してください。

  * **Azure Web アプリ**: [Application Insights 拡張機能](./azure-web-apps.md)を追加する
  * **Azure VM と Azure 仮想マシン スケール セットの IIS でホストされたアプリ**: [アプリケーション監視拡張機能](./azure-vm-vmss-apps.md)を追加する
  * [Application Insights SDK](./asp-net.md) をアプリ コードにインストールする
  * **IIS Web サーバー**: [Application Insights エージェント](./status-monitor-v2-overview.md)を実行する
  * **Java Web アプリ**: [Java エージェント](./java-in-process-agent.md)を有効にする

### <a name="client-side"></a>クライアント側

Web ブラウザーで発生した例外をクライアント側でレポートする機能は、JavaScript SDK によって実現されます。 クライアント側での例外レポートを設定する方法については、「[Web ページ向けの Application Insights](./javascript.md)」を参照してください。

### <a name="application-frameworks"></a>アプリケーション フレームワーク

いくつかのアプリケーション フレームワークでは、必要な構成がもう少し多くなります。次のテクノロジを検討してください。

  * [Web フォーム](#web-forms)
  * [MVC](#mvc)
  * [Web API 1.*](#web-api-1x)
  * [Web API 2.*](#web-api-2x)
  * [WCF](#wcf)

> [!IMPORTANT]
> この記事では、コード例の観点から .NET Framework アプリに特に焦点を絞って説明します。 .NET Framework に対して機能するメソッドの一部は、.NET Core SDK では廃止されています。 .NET Core を使用してアプリを作成する場合の詳細については、[.NET Core SDK ドキュメント](./asp-net-core.md)を参照してください。

## <a name="diagnose-exceptions-using-visual-studio"></a>Visual Studio を使用して例外を診断する

Visual Studio でアプリ ソリューションを開きます。 サーバーまたは開発マシンで、<kbd>F5</kbd> キーを使用してアプリケーションを実行します。 例外を再現します。

Visual Studio で **[Application Insights の検索]** テレメトリ ウィンドウを開きます。 デバッグ中に、 **[Application Insights]** ドロップダウンを選択します。

![プロジェクトを右クリックし、[Application Insights] を選択して開きます。](./media/asp-net-exceptions/34.png)

例外レポートを選択してスタック トレースを表示します。 関連コード ファイルを開くには、スタック トレース内の明細行参照をクリックします。

CodeLens が有効になっている場合、例外に関するデータが表示されます。

![例外の CodeLens 通知。](./media/asp-net-exceptions/35.png)

## <a name="diagnose-failures-using-the-azure-portal"></a>Azure portal を使用して障害を診断する

Application Insights には、監視対象のアプリケーションの障害を診断するのに役立つ、精選されたアプリケーション パフォーマンス管理 (APM) エクスペリエンスが用意されています。 開始するには、 **[調査]** セクションにある Application Insights リソース メニューの **[失敗]** オプションを選択します。
要求の失敗率の傾向、失敗の数、影響を受けたユーザーの数が表示されます。 **[全般]** ビューには、上位 3 つの応答コード、上位 3 つの例外の種類、上位 3 つの失敗した依存関係の種類など、選択した失敗した操作に固有の有用な内訳のいくつかが表示されます。

![障害トリアージ ビュー ([操作] タブ)](./media/asp-net-exceptions/failures0719.png)

これらの操作のサブセットごとに代表的なサンプルを確認するには、対応するリンクを選択します。 たとえば、例外を診断するには、特定の例外の数を選択します。すると、その例外が **[エンドツーエンド トランザクション** の詳細] タブに表示されます。

![エンドツーエンド トランザクションの詳細タブ](./media/asp-net-exceptions/end-to-end.png)

または、特定の失敗した操作の例外を調べる代わりに、上部で **[例外]** タブに切り替えて例外の **全体** 像から開始することができます。 ここでは、監視対象のアプリケーションに関して収集されたすべての例外が表示されます。

## <a name="custom-tracing-and-log-data"></a>カスタムのトレースとログ データ

アプリに固有の診断データを取得するには、独自のテレメトリ データを送信するコードを挿入します。 カスタムのテレメトリまたはログ データは、要求、ページ ビュー、およびその他の自動収集されたデータと共に、診断検索に表示されます。

<xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient?displayProperty=fullName> を使用すると、いくつかの API が利用できます。

* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackEvent%2A?displayProperty=nameWithType> は通常、使用パターンを監視するために使用されますが、送信されるデータは診断検索の **カスタム イベント** の下にも表示されます。 イベントには名前が付けられるほか、文字列のプロパティや数値のメトリックが付与され、それらを元に[診断検索の結果をフィルター処理](./diagnostic-search.md)できます。
* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackTrace%2A?displayProperty=nameWithType> は、POST 情報などの長いデータを送信できます。
* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackException%2A?displayProperty=nameWithType> は、Application Insights に例外の詳細 (スタック トレースなど) を送信します。

これらのイベントを表示するには、左側のメニューから [[検索]](./diagnostic-search.md) を開き、 **[イベントの種類]** ドロップダウン メニューを選択し、 **[カスタム イベント]** 、 **[トレース]** 、または **[例外]** を選択します。

![ドリル スルー](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> アプリが大量のテレメトリを生成する場合は、アダプティブ サンプリング モジュールが、代表的な一部のイベントのみを送信することによって、ポータルに送信される量を自動的に削減します。 同じ操作に含まれるイベントは、グループ単位で選択または選択解除されるので、関連するイベントごとに操作できます。 詳細については、「[Application Insights におけるサンプリング](./sampling.md)」を参照してください。

### <a name="how-to-see-request-post-data"></a>要求の POST データを表示する方法

要求詳細では、POST 呼び出しでアプリに送信されたデータは含まれません。 このデータを報告するには:

* アプリケーション プロジェクトに [SDK をインストールします](./asp-net.md)。
* アプリケーションにコードを挿入し、[Microsoft.ApplicationInsights.TrackTrace()](./api-custom-events-metrics.md#tracktrace) を呼び出します。 メッセージ パラメーターで POST データを送信します。 許可されるサイズには制限があります。そのため、必要不可欠なデータだけを送信するように努めてください。
* 失敗した要求を調査するときは、関連付けられているトレースを検索します。

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> 例外と関連する診断データをキャプチャする
最初、ポータルにはアプリの障害の原因となる例外の一部しか表示されません。 ブラウザーの例外はすべて表示されます (Web ページで [JavaScript SDK](./javascript.md) を使用している場合)。 ただし、ほとんどのサーバー例外は IIS によりキャッチされます。それを確認するには、簡単なコードを記述する必要があります。

次のようにすることができます。

* **例外を明示的に記録します** 。
* **例外を自動的にキャプチャします** 。 追加しなければならないものはフレームワークの種類によって異なります。

## <a name="reporting-exceptions-explicitly"></a>例外を明示的に報告する

最も簡単な方法として、例外ハンドラーに `trackException()` の呼び出しを挿入します。

```javascript
try
{
    // ...
}
catch (ex)
{
    appInsights.trackException(ex, "handler loc",
    {
        Game: currentGame.Name,
        State: currentGame.State.ToString()
    });
}
```

```csharp
var telemetry = new TelemetryClient();

try
{
    // ...
}
catch (Exception ex)
{
    var properties = new Dictionary<string, string>
    {
        ["Game"] = currentGame.Name
    };

    var measurements = new Dictionary<string, double>
    {
        ["Users"] = currentGame.Users.Count
    };

    // Send the exception telemetry:
    telemetry.TrackException(ex, properties, measurements);
}
```

```VB
Dim telemetry = New TelemetryClient

Try
    ' ...
Catch ex as Exception
    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("Game", currentGame.Name)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Users", currentGame.Users.Count)

    ' Send the exception telemetry:
    telemetry.TrackException(ex, properties, measurements)
End Try
```

プロパティと測定値のパラメーターは省略可能ですが、[フィルター処理と、特別な情報を追加する](./diagnostic-search.md)のに便利です。 たとえば、複数のゲームを実行できるアプリケーションを使用している場合、1 つのゲームに関連する例外レポートをすべて検索できます。 必要な数だけ項目を各辞書に追加できます。

## <a name="browser-exceptions"></a>ブラウザーの例外

ほとんどのブラウザー例外が報告されます。

Web ページにコンテンツ配信ネットワークまたはその他のドメインのスクリプト ファイルが含まれる場合、スクリプト タグに属性 `crossorigin="anonymous"`があり、サーバーが [CORS ヘッダー](https://enable-cors.org/)を送信することを確認します。 これで、これらのリソースから、未処理の JavaScript 例外のスタック トレースと詳細が取得できます。

## <a name="reuse-your-telemetry-client"></a>テレメトリ クライアントを再利用する

> [!NOTE]
> `TelemetryClient` は、一度インスタンス化された後、アプリケーションの有効期間にわたって再利用されることが推奨されています。

[.NET の依存関係の挿入 (DI)](/dotnet/core/extensions/dependency-injection) と適切な .NET SDK があって、さらに Application Insights を DI 用に正しく構成すれば、コンストラクターのパラメーターとして <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient> を要求することができます。

```csharp
public class ExampleController : ApiController
{
    private readonly TelemetryClient _telemetryClient;

    public ExampleController(TelemetryClient telemetryClient)
    {
        _telemetryClient = telemetryClient;
    }
}
```

前の例では、`TelemetryClient` が `ExampleController` クラスに挿入されます。

## <a name="web-forms"></a>Web フォーム

Web フォームの場合、HTTP モジュールは、`CustomErrors` で構成されたリダイレクトがないとき、例外を回収できます。 ただし、アクティブなリダイレクトがある場合、次の行を *Global.asax.cs* の `Application_Error` 関数に追加します。

```csharp
void Application_Error(object sender, EventArgs e)
{
    if (HttpContext.Current.IsCustomErrorEnabled &&
        Server.GetLastError () != null)
    {
        _telemetryClient.TrackException(Server.GetLastError());
    }
}
```

前の例の `_telemetryClient` は、クラススコープの <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient> 型変数です。

## <a name="mvc"></a>MVC

Application Insights Web SDK バージョン 2.6 (beta3 以降) では、Application Insights は、MVC 5+ コントローラーのメソッドでスローされた未処理の例外を自動的に収集します。 このような例外を追跡するカスタム ハンドラーを以前に追加した場合は、それを削除して、例外の二重追跡を防止できます。

例外がスローされたときに例外フィルターが正しくエラーを処理できないシナリオは数多くあります。

* コントローラー コンストラクターから。
* メッセージ ハンドラーから。
* ルーティング中。
* 応答コンテンツのシリアル化中。
* アプリケーションの起動中。
* バックグラウンド タスク内。

アプリケーションによって "*処理される*" すべての例外も手動で追跡する必要があります。
コントローラーで発生した例外を処理しないと、通常は、500 [内部サーバー エラー] の応答になります。 このような応答を、処理済みの例外 (または例外なし) の結果として手動で構成した場合、`ResultCode` 500 を使用して、対応する要求テレメトリで追跡されます。ただし、Application Insights SDK は対応する例外を追跡できません。

### <a name="prior-versions-support"></a>以前のバージョンのサポート

Application Insights Web SDK 2.5 (および以前) の MVC 4 (および以前) を使用する場合は、次の例を参照して例外を追跡します。

[CustomErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) 構成が `Off` の場合、[HTTP モジュール](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85))で例外を収集できます。 ただし、`RemoteOnly` (既定) または `On` の場合、例外は消去され、Application Insights が自動回収する例外はなくなります。 これを解決するには、[System.Web.Mvc.HandleErrorAttribute クラス](/dotnet/api/system.web.mvc.handleerrorattribute)をオーバーライドし、次のようにオーバーライドしたクラスを異なる MVC バージョンに適用します ([GitHub ソース](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs))。

```csharp
using System;
using System.Web.Mvc;
using Microsoft.ApplicationInsights;

namespace MVC2App.Controllers
{
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
    public class AiHandleErrorAttribute : HandleErrorAttribute
    {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
    }
}
```

#### <a name="mvc-2"></a>MVC 2

HandleError 属性をコントローラーの新しい属性で置換します。

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
            // Omitted for brevity
        }
    }
```

[サンプル](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3

*Global.asax.cs* で `AiHandleErrorAttribute` をグローバル フィルターとして登録します。

```csharp
public class MyMvcApplication : System.Web.HttpApplication
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        filters.Add(new AiHandleErrorAttribute());
    }
}
```

[サンプル](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4、MVC5

*FilterConfig.cs* で `AiHandleErrorAttribute` をグローバル フィルターとして登録します。

```csharp
public class FilterConfig
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
    }
}
```

[サンプル](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web API

Application Insights Web SDK バージョン 2.6 (beta3 以降) では、Application Insights は、WebAPI 2+ についてコントローラーのメソッドでスローされた未処理の例外を自動的に収集します。 そのような例外 (次の例を参照) を追跡するためにカスタム ハンドラーを以前に追加した場合、例外を二重で追跡しないようにハンドラーを取り除くことができます。

例外フィルターが処理できないケースがあります。 次に例を示します。

* コントローラー コンストラクターからスローされる例外。
* メッセージ ハンドラーからスローされる例外。
* ルーティング中にスローされる例外。
* 応答コンテンツのシリアル化中にスローされる例外。
* アプリケーションの起動中にスローされる例外。
* バックグラウンド タスクでスローされる例外。

アプリケーションによって "*処理される*" すべての例外も手動で追跡する必要があります。
コントローラーで発生した例外を処理しないと、通常は、500 [内部サーバー エラー] の応答になります。 このような応答を、処理済みの例外 (または例外なし) の結果として手動で構成した場合、`ResultCode` 500 を使用して、対応する要求テレメトリで追跡されます。ただし、Application Insights SDK は対応する例外を追跡できません。

### <a name="prior-versions-support"></a>以前のバージョンのサポート

Application Insights Web SDK 2.5 (および以前) の WebAPI 1 (および以前) を使用する場合は、次の例を参照して例外を追跡します。

#### <a name="web-api-1x"></a>Web API 1.x

`System.Web.Http.Filters.ExceptionFilterAttribute` をオーバーライドします。

```csharp
using System.Web.Http.Filters;
using Microsoft.ApplicationInsights;

namespace WebAPI.App_Start
{
    public class AiExceptionFilterAttribute : ExceptionFilterAttribute
    {
    public override void OnException(HttpActionExecutedContext actionExecutedContext)
    {
        if (actionExecutedContext != null && actionExecutedContext.Exception != null)
        {  //or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();
            ai.TrackException(actionExecutedContext.Exception);
        }
        base.OnException(actionExecutedContext);
    }
    }
}
```

このオーバーライドされた属性を特定のコントローラーに追加するか、`WebApiConfig` クラスのグローバル フィルター構成に追加できます。

```csharp
using System.Web.Http;
using WebApi1.x.App_Start;

namespace WebApi1.x
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
    
            // ...
            config.EnableSystemDiagnosticsTracing();
    
            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
    }
}
```

[サンプル](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2.x

`IExceptionLogger` の実装を追加します。

```csharp
using System.Web.Http.ExceptionHandling;
using Microsoft.ApplicationInsights;

namespace ProductsAppPureWebAPI.App_Start
{
    public class AiExceptionLogger : ExceptionLogger
    {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context != null && context.Exception != null)
            {
                //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
    }
}
```

これを WebApiConfig のサービスに追加します。

```csharp
using System.Web.Http;
using System.Web.Http.ExceptionHandling;
using ProductsAppPureWebAPI.App_Start;

namespace WebApi2WithMVC
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services
    
            // Web API routes
            config.MapHttpAttributeRoutes();
    
            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });

            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
    }
}
```

[サンプル](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

代替として、次のように操作できます。

1. 唯一の ExceptionHandler を IExceptionHandler のカスタム実装で置換します。 これはフレームワークが送信する応答メッセージを選択できるときにのみ呼び出されます (たとえば、接続が中止されるときではなく)。
2. 例外フィルター (上の Web API 1.x コントローラーのセクション参照) - 場合によっては呼び出されません。

## <a name="wcf"></a>WCF

Attribute を拡張し、IErrorHandler と IServiceBehavior を実装するクラスを追加します。

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }
```

サービス実装に属性を追加します。

```csharp
namespace WcfService4
{
    [AiLogException]
    public class Service1 : IService1
    {
        // Omitted for brevity
    }
}
```

[サンプル](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>例外パフォーマンス カウンター

サーバーに [Azure Monitor Application Insights エージェントがインストール](./status-monitor-v2-overview.md)されている場合は、.NET によって測定された例外レートのグラフを取得できます。 これには、処理済みの .NET 例外と未処理の .NET 例外の両方が含まれます。

[Metric Explorer]\(メトリックス エクスプローラー\) タブを開き、新しいグラフを追加して、パフォーマンス カウンターの下に表示されている **[例外レート]** を選択します。

.NET フレームワークでは、特定の時間間隔で例外数をカウントし、それを時間間隔の長さで割り算することで、例外レートを算出します。

この値は、TrackException レポートをカウントする Application Insights ポータルで算出される "例外" 数とは異なります。 サンプリングの時間間隔が異なります。さらに、SDK では、すべての処理済みの例外と未処理の例外について TrackException レポートを送信するわけではありません。

## <a name="next-steps"></a>次のステップ

* [REST、SQL、および依存関係へのその他の呼び出しを監視する](./asp-net-dependencies.md)
* [ページの読み込み時間、ブラウザー例外、および AJAX 呼び出しを監視する](./javascript.md)
* [パフォーマンス カウンターを監視する](./performance-counters.md)
