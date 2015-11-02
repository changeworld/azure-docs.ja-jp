<properties 
	pageTitle="カスタムのイベントとメトリックのための Application Insights API" 
	description="デバイスまたはデスクトップ アプリケーション、Web ページまたはサービスに数行のコードを追加し、使用状況を追跡し、問題を診断します。" 
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
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Application Insights SDK におけるテレメトリの処理

*Application Insights はプレビュー段階です。*

Application Insights SDK のプラグインを作成および構成して、Application Insights サービスに送信される前のテレメトリのキャプチャと処理の方法をカスタマイズできます。

* [サンプリング](#sampling)は、統計にゆがみを生じさせずにテレメトリの量を削減します。関連するデータ ポイントがまとめられるため、簡単に検索できるようになります。
* [テレメトリ プロセッサ](#telemetry-processors)は、SDK のデータをサーバーに送信する前に初期化、フィルタリング、サンプリングします。たとえば、計算したプロパティをいくつかのテレメトリに追加することや、ロボットからの要求を除外することでテレメトリの量を削減することができます。 
* [テレメトリ初期化子](#telemetry-initializers)は、送信されるすべてのテレメトリに既定のプロパティを追加するためのもので、標準テレメトリ モジュールの一部の動作を上書きできます。これらのプロパティは、追跡の呼び出しごとに評価されるため、値を変更することができます。
* [コンテキスト初期化子](#context-initializers)もグローバル プロパティを設定します。プロパティは開始の際に 1 回だけ設定します。後で変更することはできません。(テレメトリ初期化子と対照的に) これによって CPU 時間がいくらか短縮されますが、柔軟性も低下します。
* [SDK API](app-insights-api-custom-events-metrics.md) は、カスタム イベントとメトリックの送信に使用します。

これらのさまざまなメカニズムの間には多少の冗長性があるため、初期化子は今後変更される可能性があります。

開始する前に次の操作を実行してください。

* [アプリに Application Insights SDK](app-insights-asp-net.md) をインストールする。テレメトリ プロセッサには、が必要です。 
* [Application Insights API](app-insights-api-custom-events-metrics.md) を試用する。 


## サンプリング

*この機能はベータ版です。*

正確な統計情報を保持したままでトラフィックを削減するお勧めの方法です。フィルターを使用すると、関連のある項目が選択されるため、診断内の項目間を移動しやすくなります。フィルター処理された項目を補正するために、メトリックス エクスプローラーでイベントの数が調整されます。

ASP.NET サーバーの場合は、Application\_Start などの適切な初期化の場所に次のコードを挿入します。

*C#*

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```

Web ページの場合は、(通常は、\_Layout.cshtml などのマスター ページに) 挿入した [Application Insights のスニペット](app-insights-javascript.md)に追加の行を配置します。

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* 100/N (N は整数) と等しいパーセント値 (この例では 10) を設定します。たとえば、50 (= 1/2)、33.33 (= 1/3)、25 (1/4 =)、10 (= 1/5) です。
* Web ページとサーバーの両方でサンプリングを設定する場合は、両方で同じサンプリング比率を設定していることを確認してください。
* クライアント側とサーバー側で、連携して関連する項目を選択します。

[サンプリングの詳細についてはこちらを参照してください](app-insights-sampling.md)。

## テレメトリ プロセッサ

テレメトリ プロセッサを記述して、バックエンドのポータルに送信される前に、Application Insights SDK によって生成されたテレメトリに対してフィルター処理と前処理を実行できます。これには、HTTP 要求コレクターや依存関係コレクターなどの標準的なモジュールのテレメトリが含まれます。たとえば、ロボットからの要求や成功した依存関係の呼び出しについてのテレメトリをフィルターで除外できます。

> [AZURE.WARNING]プロセッサを使用して SDK から送信されるテレメトリをフィルター処理すると、ポータルに表示される統計にゆがみが生じ、関連項目を追跡するのが困難になる可能性があります。
> 
> 代わりに、[サンプリング](#sampling)の使用を検討します。

### テレメトリ プロセッサを作成する

1. フィルターを作成するには、ITelemetryProcessor を実装します。これは、テレメトリ モジュール、テレメトリ初期化子、テレメトリ チャネルと同じく、機能拡張ポイントの 1 つです。 

    テレメトリ プロセッサが処理のチェーンを構築することに注意してください。テレメトリ プロセッサをインスタンス化するときは、リンクをチェーン内の次のプロセッサに渡します。テレメトリ データ ポイントが Process メソッドに渡されると、作業が実行され、そのチェーンの次のテレメトリ プロセッサが呼び出されます。

    ``` C#

    namespace FilteringTelemetryProcessor
    {
      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.DataContracts;

      class UnauthorizedRequestFilteringProcessor : ITelemetryProcessor
      {
        public UnauthorizedRequestFilteringProcessor(ITelemetryProcessor next)
		//Initialization will fail without this constructor. Link processors to each other
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
        }      private ITelemetryProcessor Next { get; set; }
      }
    }

    ```
2. 適切な初期化クラス (たとえば Global.asax.cs の AppStart) で、プロセッサをチェーンに挿入します。

    ```C#

    var builder = new TelemetryChannelBuilder();
    builder.Use((next) => new UnauthorizedRequestFilteringProcessor(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    TelemetryConfiguration.Active.TelemetryChannel = builder.Build();

    ```

    この時点より後に作成された TelemetryClients はプロセッサを使用します。

### フィルターの例

#### 人工的な要求

ボットと Web テストを除外します。メトリックス エクスプローラーで人工的なソースを除外することもできますが、SDK でそのソースをフィルター処理することでトラフィックが削減されます。

``` C#

public void Process(ITelemetry item)
{
    if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource))
    { return; }

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


## テレメトリの初期化子

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

*または、*Global.aspx.cs などのコード内で初期化子をインスタンス化することもできます。


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






## <a name="default-properties"></a>コンテキストの初期化子 - すべてのテレメトリに既定のプロパティを設定する

すべての新しい TelemetryClients が自動的にコンテキストを使用できるように、汎用の初期化子を設定できます。これには、Web サーバー要求追跡など、プラットフォーム固有のテレメトリ モジュールにより送信される標準の利用統計情報が含まれます。

一般的な使用方法では、アプリケーションのさまざまなバージョンやコンポーネントからのテレメトリを識別します。ポータルでは、"アプリケーションのバージョン" プロパティによって結果にフィルターを適用し、グループ化することができます。

一般に、[コンテキスト初期化子ではなく、テレメトリ初期化子を使用することをお勧めします](http://apmtips.com/blog/2015/06/09/do-not-use-context-initializers/)。

#### コンテキスト初期化子を定義する


*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MyNamespace
    {
      // Context initializer class
      public class MyContextInitializer : IContextInitializer
      {
        public void Initialize (TelemetryContext context)
        {
            if (context == null) return;

            context.Component.Version = "v2.1";
        }
      }
    }
```

*Java*

```Java

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyContextInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.Component.Version = "2.1";
      }
    }
```

#### コンテキスト初期化子を読み込む

ApplicationInsights.config で:

    <ApplicationInsights>
      <ContextInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MyNamespace.MyContextInitializer, MyAssemblyName"/> 
        ...
      </ContextInitializers>
    </ApplicationInsights>

*または、*コード内で初期化子をインスタンス化することもできます。

*C#*

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyContextInitializer());
    }
```

*Java*

```Java

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyContextInitializer());
```



## リファレンス ドキュメント

* [API の概要](app-insights-api-custom-events-metrics.md)

* [ASP.NET リファレンス](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java リファレンス](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## SDK コード

* [ASP.NET コア SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)
* [すべてのプラットフォーム](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## 疑問がある場合

* *Track\_() の呼び出しでは、どのような例外がスローされることがありますか。*
    
    ありません。try catch 句で例外をラップする必要はありません。SDK で問題が発生すると、デバッグ コンソール出力に表示されるメッセージが記録されます。メッセージがスルーされる場合は、診断検索にも記録されます。



* *REST API はありますか。*

    はい。ただし、まだ公開されていません。

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
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO4-->