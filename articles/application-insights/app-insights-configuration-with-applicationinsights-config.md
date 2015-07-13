<properties 
	pageTitle="ApplicationInsights.config または .xml を使った Application Insights SDK の構成" 
	description="データ コレクション モジュールを有効または無効にし、パフォーマンス カウンターとその他のパラメーターを追加します" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="awills"/>

# ApplicationInsights.config または .xml を使った Application Insights SDK の構成

Application Insights SDK は、いくつかのモジュールで構成されます。コア モジュールは、Application Insights ポータルにテレメトリを送信する基本的な API を提供します。追加のモジュールは、アプリケーションとそのコンテキストからデータを収集します。構成ファイルを調整することによって、モジュールを有効または無効にしたり、その中のいくつかのモジュールのパラメーターを設定したりできます。

アプリケーションの種類に応じて、構成ファイルの名前は `ApplicationInsights.config` または `ApplicationInsights.xml` になります。[SDK をインストールする際に][start]、これはプロジェクトに自動的に追加されます。また、これは、[状態モニターを IIS サーバーに][redfield]追加することによって、または [Azure Web サイトまたは VM のAppplication Insights 拡張を選択する][azure]際にも Web アプリに追加されます。

[Web ページの SDK][client] を制御するための同等のファイルはありません。


## テレメトリ モジュール

各モジュールの構成ファイルにノードが存在します。モジュールを無効にするには、ノードを削除するか、コメント アウトします。

#### Implementation.Tracing.DiagnosticsTelemetryModule

SDK 内のエラーを報告します。たとえば、SDK のパフォーマンス カウンターにアクセスできない場合、またはカスタム TelemetryInitializer が例外をスローする場合です。

データは[診断検索][diagnostic]に表示されます。

#### RuntimeTelemetry.RemoteDependencyModule

アプリケーションで使用される外部のコンポーネントの応答性に関するデータを収集します。このモジュールを IIS サーバーで機能させるには、[ をインストールする][redfield]必要があります。これを Azure Web アプリまたは VM で使用するには、[Application Insights 拡張を選択します][azure]。

#### Web.WebApplicationLifecycleModule

プロセスのシャット ダウン時に失われないよう、テレメトリ データのすべてのメモリ内バッファーのフラッシュを試行します。

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

Web アプリに到着する要求をカウントし、応答時間を測定します。

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

Web アプリで処理されない例外をカウントします。「[障害と例外][exceptions]」をご覧ください。



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## パフォーマンス コレクター モジュール

#### PerfCollector.PerformanceCollectorModule

既定では、このモジュールは、さまざまな Windows パフォーマンス カウンターを収集します。メトリック エクスプローラーで、フィルターのブレードを開いたときにこれらのカウンターを確認できます。

追加のパフォーマンス カウンター (Windows の標準カウンターと、追加したすべてのカウンターの両方) を監視できます。
      
その他のパフォーマンス カウンターを収集するには、次の構文を使用します。
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
`PerformanceCounter` は `\CategoryName(InstanceName)\CounterName` または `\CategoryName\CounterName` のいずれかでなければなりません。
      
`ReportAs` 属性を指定すると、これは Application Insights に表示される名前として使用されます。

Application Insights に報告する場合、カウンター名には、文字、角かっこ、スラッシュ、ハイフン、アンダースコア、スペース、ドットしか含められません。

監視するカウンターに '#' や数字などの無効な文字が含まれている場合、[ReportAs] を使用する必要があります。
      
次のプレースホルダーが `InstanceName` としてサポートされています。

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## チャネル パラメーター (Java)

これらのパラメーターは、Java SDK が収集するテレメトリ データの格納とフラッシュする方法に影響します。

#### MaxTelemetryBufferCapacity

SDK のメモリー内ストレージに格納できるテレメトリ項目の数。この数に達すると、テレメトリのバッファーがフラッシュされます。つまり、テレメトリの項目が、Application Insights サーバーに送信されます。

-	最小: 1
-	最大: 1000
-	既定値: 500

    <ApplicationInsights> ...<Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

メモリー内ストレージに格納されているデータがフラッシュされる (Application Insights に送信される) 頻度を決定します。

-	最小: 1
-	最大: 300
-	既定値: 5

    <ApplicationInsights> ...<Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

ローカル ディスクの永続的なストレージに割り当てられる MB 単位の最大サイズを決定します。このストレージは、Application Insights のエンドポイントへの転送に失敗したテレメトリの項目を保存するために使用されます。ストレージのサイズが満たされている場合は、テレメトリの新しい項目は破棄されます。

-	最小: 1
-	最大: 100
-	既定値: 10

    <ApplicationInsights> ...<Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## コンテキストの初期化子

これらのコンポーネントは、プラットフォームからデータを収集します。データは、[TelemetryContext オブジェクト][api]に収集されます。

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## テレメトリの初期化子

これらのコンポーネントは、Application Insights に送信される各テレメトリのイベントにデータを追加します。


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

このコンポーネントは、検索エンジンや Web テストなどのロボットから送信される HTTP 要求を識別します。これは TelemetryClient.Context.Operation.SyntheticSource を設定します。

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

テレメトリのすべての項目に操作名を追加します。Web アプリの場合、"操作" は HTTP 要求を意味します。TelemetryClient.Context.Operation.Name を設定します。

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

これは、[診断検索][diagnostic]の "同じ要求のイベントを検索する" 機能を有効にします。TelemetryClient.Context.Operation.Id を設定します

Application Insights に送信される各データ項目に操作 ID を追加します。Web アプリの場合、"操作" とは HTTP 要求のことです。それで、たとえば、要求、要求処理の一部を成すカスタム イベントやトレースはすべて、同じ操作 ID を持つことになります。

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

テレメトリのすべての項目に匿名ユーザー ID を追加します。これにより、診断の検索で 1 人のユーザーのアクティビティに関連するイベントだけをフィルター処理することができます。たとえば、例外が報告された場合は、そのユーザーが行っていた操作をトレースできます。

telemetryClient.Context.User を設定します

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

各イベントにセッション ID を追加します。telemetryClient.Context.Session を設定します

## カスタム初期化子


標準の初期化子がアプリケーションに適切でない場合、独自の初期化子を作成できます。

テレメトリのすべてのクライアントを初期化するために使用される値を設定するには、コンテキスト初期化子を使用します。次の例では、アプリの複数のバージョンを発行した場合、カスタム プロパティでフィルター処理してデータを分離できるようにすることができます。

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

処理を各イベントに追加するには、テレメトリの初期化子を使用します。たとえば、Web SDK は、応答コードが 400 以上の要求をすべて失敗としてフラグ設定します。動作をオーバーライドすることもできます。

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
初期化子をインストールするには、ApplicationInsights.config に行を追加します。

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


あるいは、アプリケーション実行の早い段階で初期化子をインストールするコードを記述することもできます。次に例を示します。


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

これは、データが表示される Application Insights のリソースを決定します。通常、アプリケーションごとに、個別のキーを持つリソースを作成します。

キーを動的に設定する場合 (たとえば、アプリケーションからの結果を別のリソースに送信する場合)、構成ファイルからキーを省略し、代わりにコードで設定することができます。

TelemetryClient のすべてのインスタンスのキーを設定するには (標準のテレメトリ モジュールを含む)、TelemetryConfiguration.Active でキーを設定します。ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーの設定を行います。

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

特定のイベント セットを異なるリソースに送信する場合、特定の TelemetryClient のキーを設定できます。

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[API の詳細][api]。

新しいキーを取得するには、[Application Insights ポータルで新しいリソースを作成します][new]。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->