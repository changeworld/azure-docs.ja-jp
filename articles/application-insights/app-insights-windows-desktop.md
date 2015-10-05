<properties 
	pageTitle="Windows デスクトップのアプリとサービス用の Application Insights" 
	description="Application Insights を使用して Windows デスクトップ アプリの使用状況とパフォーマンスを分析します。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="awills"/>

# Windows デスクトップ アプリ、サービス、worker ロールに対する Application Insights

*Application Insights はプレビュー段階です。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights を使用すると、デプロイしたアプリケーションの使用状況とパフォーマンスを監視できます。

デスクトップ アプリ、バックグラウンド サービス、worker ロールなどの Windows アプリケーションはすべて、Application Insights のコア SDK を使用して、Application Insights にテレメトリを送信します。クラス ライブラリ プロジェクトに Application Insights SDK を追加することもできます。

コア SDK は API を提供するだけです。つまり、Web またはデバイス SDKと違い、データを自動で集めるモジュールを含まないため、コードを記述して、自身のテレメトリを送信する必要があります。パフォーマンス カウンター コレクターなど、他のパッケージの一部は、デスクトップ アプリでも機能します。


## <a name="add"></a>Application Insights リソースの作成


1.  [Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。アプリケーションの種類には、Windows ストア アプリを選択します。 

    ![[新規]、[Application Insights] の順にクリックする](./media/app-insights-windows-desktop/01-new.png)

    (選択したアプリケーションの種類に応じて、[メトリックス エクスプローラー][metrics]で使用できる概要ブレードのコンテンツとプロパティが設定されます)

2.  インストルメンテーション キーをコピーします。

    ![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>アプリケーションでの SDK のインストール


1. Visual Studio で、デスクトップ アプリ プロジェクトの NuGet パッケージを編集します。

    ![プロジェクトを右クリックし、[Nuget パッケージの管理] を選択する](./media/app-insights-windows-desktop/03-nuget.png)

2. Application Insights コア API パッケージ: Microsoft.ApplicationInsights をインストールします。

    ![Search for "Application Insights"](./media/app-insights-windows-desktop/04-core-nuget.png)

    *他のパッケージを使用することができますか。*

    はい、モジュールを使用する場合は、パフォーマンス カウンターや依存関係コレクターのパッケージなど他のパッケージをインストールできます。Microsoft.ApplicationInsights.Web には、このようなパッケージがいくつか含まれています。[ログまたはトレースのコレクター パッケージ](app-insights-asp-net-trace-logs.md)を使用する場合は、Web サーバー パッケージで開始します。

    (ただし、Microsoft.ApplicationInsights.Windows は使用しないでください。これは、Windows ストア アプリ向けです。)

3. InstrumentationKey を設定します。

    * コア API パッケージ Microsoft.ApplicationInsights のみをインストールする場合は、コードにキーを設定する必要があります。たとえば、main() で次のように設定します。 

     `TelemetryConfiguration.Active.InstrumentationKey = "`*your key*`";`

    * その他のパッケージの 1 つをインストールした場合は、コードを使用してキーを設定するか、ApplicationInsights.config 内でキーを設定します。
 
     `<InstrumentationKey>`*your key*`</InstrumentationKey>`



## <a name="telemetry"></a>テレメトリの呼び出しの挿入

`TelemetryClient` インスタンスを作成してから、[このインスタンスを使用してテレメトリを送信][api]します。


たとえば、Windows フォーム アプリケーションでは、次のように記述します。

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

テレメトリを送信するには、[Application Insights API][api] のいずれかを使用します。Windows デスクトップ アプリケーションでは、テレメトリが自動的に送信されることはありません。通常は次のものを使用します。

* `TrackPageView(pageName)` は、フォーム、ページ、またはタブを切り替えるために使用する。
* `TrackEvent(eventName)` は、他のユーザー アクションに使用する。
* `TrackMetric(name, value)` は、特定のイベントにアタッチされていないメトリックスの定期的なレポートを送信する場合にバックグラウンド タスクで使用する。
* `TrackTrace(logEvent)` は、[診断ログ][diagnostic]に使用する。
* `TrackException(exception)` は catch 句に使用する。
* `Flush()` は、アプリを終了する前にすべてのテレメトリを確実に送信するために使用する。これは、コア API (Microsoft.ApplicationInsights) を使用する場合に限られます。Web およびデバイス SDK ではこの動作が自動的に実装されます (インターネットが常に利用できるとは限らないコンテキストでアプリが実行される場合は、[永続化チャネル](#persistence-channel)に関するページも参照してください。)


#### コンテキストの初期化子

ユーザーとセッションの数を表示するために、各 `TelemetryClient` インスタンスに値を設定できます。または、コンテキストの初期化子を使用して、この加算をすべてのクライアントに実行できます。

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>プロジェクトの実行

[F5 キーを押してアプリケーションを実行](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)し、これを使用してテレメトリを生成します。

Visual Studio で、送信されたイベント数が表示されます。

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>監視データの表示

Azure ポータルで、アプリケーションのブレードに戻ります。

[[診断検索]](app-insights-diagnostic-search.md) に最初のイベントが表示されます。

大量のデータが予想される場合は、数秒後に [最新の情報に更新] をクリックします。

TrackMetric、または TrackEvent の測定値パラメーターを使用した場合は、[メトリックス エクスプローラー][metrics]を開き、[フィルター] ブレードを開きます。メトリックが表示されますが、パイプラインを通過するのに時間がかかることがあるため、場合によっては [フィルター] ブレードを閉じてしばらく待ってから更新する必要があります。



## 永続化チャネル 

インターネットが常に利用できるとは限らない状況または速度が遅い状況でアプリが実行される場合は、既定のメモリ内チャネルの代わりに永続化チャネルの使用を検討してください。

既定のメモリ内チャネルでは、アプリが終了するまでに送信されなかったテレメトリは失われます。`Flush()` を使用してバッファーに残っているデータを送信できますが、インターネット接続がない場合、または送信が完了する前にアプリがシャットダウンした場合、データは失われます。

これに対し、永続化チャネルでは、テレメトリはファイル内でバッファー処理されてからポータルに送信されます。`Flush()` によってデータは確実にファイルに格納されます。アプリが終了するまでに送信されなかったデータはファイル内に保持されます。アプリを再起動したときにインターネット接続がある場合、そのデータは送信されます。接続が利用可能になるまでは、データは、必要である期間だけファイル内に蓄積されます。

### 永続化チャネルを使用するには

1. NuGet パッケージ [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel) をインポートします。
2. アプリの適切な初期化の場所に、このコードを含めます。
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. アプリを終了する前に `telemetryClient.Flush()` を使用して、確実にデータがポータルに送信されるか、ファイルに保存されるようにします。

 
永続化チャネルは、アプリケーションで作成されるイベントの数が比較的少なく、接続の信頼性が低いことの多いデバイス シナリオに対して最適化されています。このチャネルは、イベントを最初にディスクに書き込んで確実に保存してから、送信を試行します。

#### 例

未処理の例外を監視するとします。`UnhandledException` イベントをサブスクライブします。コールバックで Flush の呼び出しを含めると、テレメトリが確実に保持されます。
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

アプリをシャットダウンすると、`%LocalAppData%\Microsoft\ApplicationInsights` にファイルが作成され、圧縮されたイベントが格納されます。
 
このアプリケーションを次回起動したときに、チャネルがこのファイルを取得し、可能な場合はテレメトリを Application Insights に配信します。

#### テストの例

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


永続化チャネルのコードは、[github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/master/src/TelemetryChannels/PersistenceChannel) にあります。


## <a name="usage"></a>次のステップ

[アプリの使用状況の追跡][knowUsers]

[診断ログのキャプチャと検索][diagnostic]

[トラブルシューティング][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=Sept15_HO4-->