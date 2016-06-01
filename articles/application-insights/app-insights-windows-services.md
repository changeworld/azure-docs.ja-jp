<properties 
	pageTitle="Windows サービス用の Application Insights" 
	description="Application Insights で Windows バックグラウンド サービスの使用状況とパフォーマンスを分析します。" 
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
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Windows バックグラウンド サービスでの Application Insights

*Application Insights はプレビュー段階です。*

[Visual Studio Application Insights](app-insights-get-started.md) を使用すると、デプロイされたアプリケーションの使用状況とパフォーマンスを監視できます。

バックグラウンド サービス、worker ロールなどの Windows アプリケーションはすべて、Application Insights SDK を使用して、Application Insights にテレメトリを送信します。クラス ライブラリ プロジェクトに SDK を追加することもできます。

(たとえば、パフォーマンス カウンターまたは依存関係の呼出しを監視するために) どの標準データ コレクターを選択するか、単にコア API を使用して、独自のテレメトリを記述できます。

まず、別の種類の Windows アプリケーションを使用しているかどうかを確認します。

* Web アプリ: [ASP.NET 4](app-insights-asp-net.md)、[ASP.NET 5](app-insights-asp-net-five.md) に移動します。
* [Azure Cloud Services](app-insights-cloudservices.md)
* デスクトップ アプリ: [HockeyApp](https://hockeyapp.net) をお勧めします。HockeyApp を使用すると、配布、ライブ テスト、ユーザーからのフィードバックの管理だけでなく、使用状況とクラッシュ レポートの監視も行うことができます。また、[デスクトップ アプリからテレメトリを Application Insights に送信する](app-insights-windows-desktop.md)こともできます。 


## <a name="add"></a>Application Insights リソースの作成


1.  [Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。アプリケーションの種類として ASP.NET アプリを選択します。 

    ![[新規]、[Application Insights] の順にクリックする](./media/app-insights-windows-services/01-new.png)


2.  インストルメンテーション キーをコピーします。先ほど作成した新しいリソースの [要点] ボックスの一覧で、キーを探します。アプリケーション マップを閉じるか、左側にあるリソースの概要ブレードまでスクロールします。

    ![Click Essentials, select the key, and press ctrl+C](./media/app-insights-windows-services/10.png)

## <a name="sdk"></a>アプリケーションでの SDK のインストール


1. Visual Studio で、Windows アプリケーション プロジェクトの NuGet パッケージを編集します。

    ![プロジェクトを右クリックし、[Nuget パッケージの管理] を選択する](./media/app-insights-windows-services/03-nuget.png)

2. Application Insights Windows Server パッケージである Microsoft.ApplicationInsights.WindowsServer をインストールします。

    !["Application Insights" の検索](./media/app-insights-windows-services/04-ai-nuget.png)

    *他のパッケージを使用することができますか。*

    はい。独自のテレメトリを送信するためだけに API を使用する場合は、コア API (Microsoft.ApplicationInsights) を選択してください。Windows Server パッケージには、コア API に加え、他にも多くのパッケージ (パフォーマンス カウンターや依存関係の監視など) が自動的に含まれます。


3. InstrumentationKey を設定します。

    * コア API パッケージ Microsoft.ApplicationInsights のみをインストールする場合は、コードにキーを設定する必要があります。たとえば、main() で次のように設定します。 

    `TelemetryConfiguration.Active.InstrumentationKey = "` *your key* `";`

    その他のパッケージの 1 つをインストールした場合は、コードを使用してキーを設定するか、ApplicationInsights.config 内でキーを設定します。
 
    `<InstrumentationKey>`*your key*`</InstrumentationKey>`

    ApplicationInsights.config を使用する場合は、ソリューション エクスプローラーでプロパティが **Build Action = Content、Copy to Output Directory = Copy** に設定されていることを確認します。

## <a name="telemetry"></a>テレメトリの呼び出しの挿入

テレメトリを送信するには、[Application Insights API][api] のいずれかを使用します。コア API を使用している場合、テレメトリは自動的に送信されません。通常は次のものを使用します。

* `TrackPageView(pageName)` は、フォーム、ページ、またはタブを切り替えるために使用する。
* `TrackEvent(eventName)` は、他のユーザー アクションに使用する。
* `TrackMetric(name, value)` は、特定のイベントにアタッチされていないメトリックスの定期的なレポートを送信する場合にバックグラウンド タスクで使用する。
* `TrackTrace(logEvent)` は、[診断ログ][diagnostic]に使用する。
* `TrackException(exception)` は catch 句に使用する。
* `Flush()` は、アプリを終了する前にすべてのテレメトリを確実に送信するために使用する。これは、コア API (Microsoft.ApplicationInsights) を使用する場合に限られます。Web SDK では、この動作が自動的に実装されます(インターネットが常に利用できるとは限らないコンテキストでアプリが実行される場合は、[永続化チャネル](#persistence-channel)に関するページも参照してください。)


#### テレメトリの初期化子

ユーザーとセッションの数を表示するために、各 `TelemetryClient` インスタンスに値を設定できます。または、テレメトリの初期化子を使用して、この加算をすべてのクライアントに対して実行することもできます。

```C#

    class UserSessionInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            telemetry.Context.User.Id = Environment.UserName;
            telemetry.Context.Session.Id = Guid.NewGuid().ToString();
        }
        
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>プロジェクトの実行

[F5 キーを押してアプリケーションを実行](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)し、これを使用してテレメトリを生成します。

Visual Studio で、送信されたイベント数が表示されます。

![](./media/app-insights-windows-services/appinsights-09eventcount.png)

イベントは診断および出力のウィンドウにも表示されます。

## <a name="monitor"></a>監視データの表示

Azure ポータルで、アプリケーションのブレードに戻ります。

最初のイベントが、ライブ ストリーム ウィンドウに表示されます。


## 永続化チャネル 

インターネット接続が常に利用できるとは限らない状況または速度が遅い状況でアプリが実行される場合は、既定のメモリ内チャネルの代わりに永続化チャネルの使用を検討してください。

既定のメモリ内チャネルでは、アプリが終了するまでに送信されなかったテレメトリは失われます。`Flush()` を使用してバッファーに残っているデータを送信できますが、インターネット接続がない場合、または送信が完了する前にアプリがシャットダウンした場合、データは失われます。

これに対し、永続化チャネルでは、テレメトリはファイル内でバッファー処理されてからポータルに送信されます。`Flush()` によってデータは確実にファイルに格納されます。アプリが終了するまでに送信されなかったデータはファイル内に保持されます。アプリを再起動したときにインターネット接続がある場合、そのデータは送信されます。接続が利用可能になるまでは、データは、必要である期間だけファイル内に蓄積されます。

### 永続化チャネルを使用するには

1. NuGet パッケージ [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3) をインポートします。
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

    Flush() は永続化チャネルでは同期ですが、その他のチャネルでは非同期であることに注意してください。

 
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


永続化チャネルのコードは、[github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel) にあります。


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
 

<!---HONumber=AcomDC_0518_2016-->