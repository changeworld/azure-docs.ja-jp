<properties 
	pageTitle="ApplicationInsights.config または .xml を使った Application Insights SDK の構成" 
	description="データ コレクション モジュールを有効または無効にし、パフォーマンス カウンターとその他のパラメーターを追加します" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# ApplicationInsights.config または .xml を使った Application Insights SDK の構成

Application Insights .NET SDK は、いくつかの NuGet パッケージで構成されます。[コア パッケージ](http://www.nuget.org/packages/Microsoft.ApplicationInsights)は、テレメトリを Application Insights に送信するための API を提供します。[その他のパッケージ](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights)は、お使いのアプリケーションとそのコンテキストからテレメトリを自動的に追跡するためのテレメトリ _モジュール_と_初期化子_を提供します。構成ファイルを調整することによって、テレメトリ モジュールと初期化子を有効または無効にしたり、その中のいくつかのモジュールのパラメーターを設定したりできます。

アプリケーションの種類に応じて、構成ファイルの名前は `ApplicationInsights.config` または `ApplicationInsights.xml` になります。[SDK の一部のバージョンをインストールする際に][start]、これはプロジェクトに自動的に追加されます。また、これは、[状態モニターを IIS サーバーに][redfield]追加することによって、または [Azure Web サイトまたは VM の Appplication Insights 拡張機能を選択する][azure]際にも Web アプリに追加されます。

[Web ページの SDK][client] を制御するための同等のファイルはありません。

各モジュールの構成ファイルにノードが存在します。モジュールを無効にするには、ノードを削除するか、コメント アウトします。

## [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet パッケージ

`Microsoft.ApplicationInsights` NuGet パッケージは、`ApplicationInsights.config` の次のテレメトリ モジュールを提供します。

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
`DiagnosticsTelemetryModule` は Application Insights インストルメンテーション コード自体のエラーを報告します。たとえば、コードのパフォーマンス カウンターにアクセスできない場合、または `ITelemetryInitializer` が例外をスローする場合です。このモジュールが追跡するトレース テレメトリが[診断検索][diagnostic]に表示されます。

## [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet パッケージ

`Microsoft.ApplicationInsights.DependencyCollector` NuGet パッケージは、`ApplicationInsights.config` の次のテレメトリ モジュールを提供します。

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
`DependencyTrackingTelemetryModule` は、外部依存関係に対してアプリケーションが実行した呼び出し (HTTP 要求や SQL クエリなど) に関するテレメトリを追跡します。このモジュールを IIS サーバーで機能させるには、[ をインストールする][redfield]必要があります。これを Azure Web アプリまたは VM で使用するには、[Application Insights 拡張を選択します][azure]。

[TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) を使用して、独自の依存関係追跡コードを記述することもできます。

## [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet パッケージ

`Microsoft.ApplicationInsights.Web` NuGet パッケージは、`ApplicationInsights.config` の次のテレメトリの初期化子とモジュールを提供します。

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**初期化子**

* `SyntheticTelemetryInitializer` は、可用性テストなど、合成ソースからの要求の処理時に追跡されるすべてのテレメトリ項目の `User`、`Session`、および `Operation` コンテキスト プロパティを更新します。Application Insights ポータルでは、既定で、合成テレメトリは表示されません。
* `ClientIpHeaderTelemetryInitializer` は、要求の `X-Forwarded-For` HTTP ヘッダーに基づいて、すべてのテレメトリ項目の `Location` コンテキストの `Ip` プロパティを更新します。
* `UserAgentTelemetryInitializer` は、要求の `User-Agent` HTTP ヘッダーに基づいて、すべてのテレメトリ項目の `User` コンテキストの `UserAgent` プロパティを更新します。
* `OperationNameTelemetryInitializer` は、HTTP メソッドのほか、ASP.NET MVC コントローラーの名前、要求の処理のために呼び出されるアクションに基づいて、すべてのテレメトリ項目の`RequestTelemetry` の `Name` プロパティと `Operation` コンテキストの `Name` プロパティを更新します。
* `OperationNameTelemetryInitializer` は 'Operation.Id` context property of all telemetry items tracked while 
handling a request with the automatically generated `RequestTelemetry.Id` を更新します。
* `UserTelemetryInitializer` は、ユーザーのブラウザーで実行する Application Insights JavaScript インストルメンテーション コードが生成する `ai_user` Cookie から抽出された値を使用して、すべてのテレメトリ項目の `User` コンテキストの `Id` および `AcquisitionDate` プロパティを更新します。
* `SessionTelemetryInitializer` は、ユーザーのブラウザーで実行する Application Insights JavaScript インストルメンテーション コードが生成する `ai_session` Cookie から抽出された値を使用して、すべてのテレメトリ項目の `Session` コンテキストの `Id` プロパティを更新します。 
* `AzureRoleEnvironmentTelemetryInitializer` は、Azure ランタイム環境から抽出された情報により、すべてのテレメトリ項目の `Device` コンテキストの `RoleName` および `RoleInstance` プロパティを更新します。
* `DomainNameRoleInstanceTelemetryInitializer` は、Web アプリケーションを実行中のコンピューターのドメイン名を使用して、すべてのテレメトリ項目の `Device` コンテキストの `RoleInstance` プロパティを更新します。
* `BuildInfoConfigComponentVersionTelemetryInitializer` は、TFS ビルドによって生成された `BuildInfo.config` ファイルから抽出された値を使用して、すべてのテレメトリ項目の `Component` コンテキストの `Version` プロパティを更新します。
* `DeviceTelemetryInitializer` は、すべてのテレメトリ項目の `Device` コンテキストの次のプロパティを更新します。
 - `Type` は "PC" に設定します
 - `Id` は Web アプリケーションが実行中のコンピューターのドメイン名に設定します。
 - `OemName` は WMI を使用して `Win32_ComputerSystem.Manufacturer` フィールドから抽出された値に設定します。
 - `Model` は WMI を使用して `Win32_ComputerSystem.Model` フィールドから抽出された値に設定します。
 - `NetworkType` は `NetworkInterface` から抽出された値に設定します。
 - `Language` は `CurrentCulture` の名前に設定します。

**モジュール**

* `RequestTrackingTelemetryModule` は Web アプリが受け取る要求を追跡し、応答時間を測定します。
* `ExceptionTrackingTelemetryModule` は Web アプリで処理されない例外を追跡します。「[障害と例外][exceptions]」をご覧ください。
* `DeveloperModeWithDebuggerAttachedTelemetryModule` は、デバッガーがアプリケーション プロセスに接続されているときに、Application Insights の `TelemetryChannel` にデータを即座に、一度に 1 つのテレメトリ項目を送信するよう強制します。これにより、アプリケーションによるテレメトリの追跡時や、アプリケーションが Application Insights ポータルに表示されるときの時間間隔が削減されます。ここでは、著しい CPU およびネットワーク帯域幅のオーバーヘッドが費やされます。

## [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet パッケージ

`Microsoft.ApplicationInsights.PerfCounterCollector` NuGet パッケージは、既定で、`ApplicationInsights.config` に次のテレメトリ モジュールを追加します。

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule` はいくつかの Windows パフォーマンス カウンターを追跡します。これらのカウンターは、メトリック エクスプローラー内のチャートをクリックし、その詳細ブレードを開いて確認できます。

追加のパフォーマンス カウンター (Windows の標準カウンターと、追加したすべてのカウンターの両方) を監視できます。
      
その他のパフォーマンス カウンターを収集するには、次の構文を使用します。

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
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

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

メモリー内ストレージに格納されているデータがフラッシュされる (Application Insights に送信される) 頻度を決定します。

-	最小: 1
-	最大: 300
-	既定値: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

ローカル ディスクの永続的なストレージに割り当てられる MB 単位の最大サイズを決定します。このストレージは、Application Insights のエンドポイントへの転送に失敗したテレメトリの項目を保存するために使用されます。ストレージのサイズが満たされている場合は、テレメトリの新しい項目は破棄されます。

-	最小: 1
-	最大: 100
-	既定値: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

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

<!---HONumber=July15_HO5-->