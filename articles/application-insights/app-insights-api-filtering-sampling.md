<properties 
	pageTitle="Application Insights SDK におけるサンプリング、フィルター処理、および前処理" 
	description="テレメトリが Application Insights ポータルに送信される前に、SDK でフィルター処理、サンプリング、またはデータへのプロパティの追加を行うためのプラグインを記述します。" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="awills"/>

# Application Insights SDK におけるテレメトリのサンプリング、フィルター処理、および前処理

*Application Insights はプレビュー段階です。*

Application Insights SDK のプラグインを作成および構成して、Application Insights サービスに送信される前のテレメトリのキャプチャと処理の方法をカスタマイズできます。

現在これらの機能は、ASP.NET SDK で利用できます。

* [サンプリング](#sampling)は、統計に影響を与えることなくテレメトリの量を削減します。関連するデータ ポイントが一緒に保持されるので、問題の診断時にそれらのデータ ポイント間を移動することができます。ポータルでは、サンプリングを補正するために合計数が乗算されます。
 * 固定レートのサンプリングを使用すると、送信されるイベントの割合を特定できます。
 * アダプティブ サンプリング (ASP.NET SDK 2.0.0-beta3 以降では既定) は、テレメトリの量に従ってサンプリング レートを自動的に調整します。ターゲット ボリュームを設定できます。
* [フィルター処理](#filtering)では、テレメトリがサーバーに送信される前に、SDK でテレメトリを選択または変更することができます。たとえば、ロボットからの要求を除外することでテレメトリの量を削減することができます。フィルター処理は、トラフィックを削減する上では、サンプリングよりも基本的な方法です。フィルター処理を使用すると、送信する内容をより細かく制御することができます。ただし、統計に影響を及ぼすことになるので注意が必要です (たとえば、すべての成功した要求を除外する場合など)。
* アプリから送信される任意のテレメトリ (標準のモジュールからのテレメトリなど) に[プロパティを追加](#add-properties)します。たとえば、算出値や、ポータルでデータをフィルター処理するのに使用できるバージョン番号を追加することが可能です。
* [SDK API](app-insights-api-custom-events-metrics.md) は、カスタム イベントとメトリックの送信に使用します。

開始する前に次の操作を実行してください。

* [アプリに Application Insights SDK](app-insights-asp-net.md) をインストールする。NuGet パッケージを手動でインストールし、最新の*プレリリース* バージョンを選択します。
* [Application Insights API](app-insights-api-custom-events-metrics.md) を試用する。 


## サンプリング

*この機能はベータ版です。*

[サンプリング](app-insights-sampling.md)は、正確な統計情報を保持したままでトラフィックを削減するお勧めの方法です。フィルターを使用すると、関連のある項目が選択されるため、診断内の項目間を移動しやすくなります。フィルター処理された項目を補正するために、メトリックス エクスプローラーでイベントの数が調整されます。

* アダプティブ サンプリングをお勧めします。アダプティブ サンプリングはサンプリングの割合を自動的に調整し、要求が一定の量になるようにします。現在は、ASP.NET サーバー側テレメトリでのみ使用できます。  
* [固定レート サンプリング](app-insights-sampling.md)も利用できます。サンプリングの割合を指定します。ASP.NET Web アプリ コードおよび JavaScript Web ページで使用できます。クライアントとサーバーはサンプリングを同期するので、検索では関連のあるページ ビューと要求の間を移動できます。

### サンプリングを有効にするには

**プロジェクトの NuGet** パッケージを Application Insights の最新の*プレリリース* バージョンに更新します。ソリューション エクスプ ローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選択し、**[プレリリースを含める]** をオンにして、Microsoft.ApplicationInsights.Web を検索します。

[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) では、アダプティブ アルゴリズムが目標とするテレメトリの最大レートを調整できます。

    <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>

### クライアント側のサンプリング

Web ページからのデータに対して固定レートのサンプリングを行うには、(通常は、\_Layout.cshtml などのマスター ページに) 挿入した [Application Insights のスニペット](app-insights-javascript.md)に追加の行を配置します。

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* 100/N (N は整数) と等しいパーセント値 (この例では 10) を設定します。たとえば、50 (=100/2)、33.33 (=100/3)、25 (=100/4)、10 (=100/10) です。 
* サーバー側でも[固定レート サンプリング](app-insights-sampling.md)を有効にしている場合は、クライアントとサーバーはサンプリングを同期するので、検索で関連のあるページ ビューと要求の間を移動できます。

[サンプリングの詳細についてはこちらを参照してください](app-insights-sampling.md)。

## フィルター処理

この手法では、テレメトリ ストリームに含める内容またはテレメトリ ストリームから除外する内容をより直接的に制御できます。フィルター処理はサンプリングと組み合わせて使用することも別々に使用することもできます。

テレメトリのフィルター処理を行うには、テレメトリ プロセッサを記述し、それを SDK に登録します。どのテレメトリもこのプロセッサを通過します。テレメトリをストリームから除外するように選択することも、プロパティを追加することもできます。これには、HTTP 要求コレクターや依存関係コレクターなどの標準的なモジュールのテレメトリに加えて、自身で作成したテレメトリも含まれます。たとえば、ロボットからの要求や成功した依存関係の呼び出しについてのテレメトリをフィルターで除外できます。

> [AZURE.WARNING]プロセッサを使用して SDK から送信されるテレメトリをフィルター処理すると、ポータルに表示される統計にゆがみが生じ、関連項目を追跡するのが困難になる可能性があります。
> 
> 代わりに、[サンプリング](#sampling)の使用を検討します。

### テレメトリ プロセッサを作成する

1. Application Insights SDK を最新バージョンに (2.0.0-beta2 以降) に更新します。Visual Studio ソリューション エクスプローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] をクリックします。NuGet パッケージ マネージャーで、**[プレリリースを含める]** をオンにし、Microsoft.ApplicationInsights を検索します。

1. フィルターを作成するには、ITelemetryProcessor を実装します。これは、テレメトリ モジュール、テレメトリ初期化子、テレメトリ チャネルと同じく、機能拡張ポイントの 1 つです。

    テレメトリ プロセッサが処理のチェーンを構築することに注意してください。テレメトリ プロセッサをインスタンス化するときは、リンクをチェーン内の次のプロセッサに渡します。テレメトリ データ ポイントが Process メソッドに渡されると、作業が実行され、そのチェーンの次のテレメトリ プロセッサが呼び出されます。

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. 次の内容を ApplicationInsights.config に挿入します 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(これは、サンプリング フィルターを初期化するセクションと同じであることに注意してください)。

名前付きのパブリック プロパティをクラス内に指定することにより、.config ファイルから文字列値を渡すことができます。

> [AZURE.WARNING].config ファイル内の型名とプロパティ名をコード内のクラスおよびプロパティ名と慎重に照合してください。存在しない型またはプロパティが .config ファイルによって参照されていると、SDK は何も通知せずにテレメトリの送信に失敗する場合があります。

 
**あるいは**、コード内でフィルターを初期化することもできます。適切な初期化クラス (たとえば Global.asax.cs の AppStart) で、プロセッサをチェーンに挿入します。

```C#

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

この時点より後に作成された TelemetryClients はプロセッサを使用します。

### フィルターの例

#### 人工的な要求

ボットと Web テストを除外します。メトリックス エクスプローラーで人工的なソースを除外することもできますが、SDK でそのソースをフィルター処理することでトラフィックが削減されます。

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### 失敗した認証

"401" 応答が返された要求を除外します。

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### リモートの依存関係の高速呼び出しを除外する

低速な呼び出しの診断のみを実行する場合は、高速呼び出しを除外します。

> [AZURE.NOTE]これによって、ポータルに表示される統計にゆがみが生じます。依存関係のグラフは、依存関係の呼び出しがすべてエラーのように表示されます。

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```


## プロパティの追加

テレメトリ初期化子を使用して、すべてのテレメトリで送信されるグローバル プロパティを定義し、標準テレメトリ モジュールの選択された動作を上書きします。

たとえば、Web 向けの Application Insights パッケージでは HTTP 要求に関するテレメトリが収集されます。既定では、応答コードが 400 以上の要求はすべて失敗としてフラグが設定されます。これに対して 400 を成功として処理する場合は、"成功" プロパティを設定するテレメトリ初期化子を指定できます。

テレメトリ初期化子を指定すると、Track*() メソッドのいずれかが呼び出されるたびに、テレメトリ初期化子も呼び出されます。これには、標準のテレメトリ モジュールによって呼び出されるメソッドも含まれます。通常、これらのモジュールでは、初期化子によって既に設定されているプロパティは設定されません。

**初期化子を定義する**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**初期化子を読み込む**

ApplicationInsights.config で:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*または、* Global.aspx.cs などのコード内で初期化子をインスタンス化することもできます。


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[このトピックのその他のサンプルについては、こちらをご覧ください。](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### JavaScript テレメトリ初期化子

*JavaScript*

ポータルから取得した初期化コードの直後にテレメトリ初期化子を挿入します。

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

telemetryItem で使用できる非カスタム プロパティの概要については、[データ モデル](app-insights-export-data-model.md/#lttelemetrytypegt)に関するページを参照してください。

任意の数の初期化子を追加できます。


## リファレンス ドキュメント

* [API の概要](app-insights-api-custom-events-metrics.md)

* [ASP.NET リファレンス](https://msdn.microsoft.com/library/dn817570.aspx)


## SDK コード

* [ASP.NET コア SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>次のステップ


[イベントおよびログを検索する][diagnostic]

[サンプルとチュートリアル](app-insights-code-samples.md)

[トラブルシューティング][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!----HONumber=AcomDC_1203_2015-->