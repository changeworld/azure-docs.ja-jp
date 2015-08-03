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
	ms.date="06/18/2015" 
	ms.author="awills"/>

# Windows デスクトップのアプリとサービスに対する Application Insights

*Application Insights はプレビュー段階です。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights を使用すると、デプロイしたアプリケーションの使用状況とパフォーマンスを監視できます。

Windows デスクトップのアプリとサービスに対するサポートは、Application Insights のコア SDK によって提供されます。この SDK は、すべてのテレメトリ データ用の API を完全にサポートしていますが、テレメトリの自動収集には対応していません。


## <a name="add"></a>Application Insights リソースの作成


1.  [Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。アプリケーションの種類には、Windows ストア アプリを選択します。 

    ![[新規]、[Application Insights] の順にクリックする](./media/app-insights-windows-desktop/01-new.png)

    (選択したアプリケーションの種類に応じて、[メトリックス エクスプローラー][metrics]で使用できる概要ブレードのコンテンツとプロパティが設定されます)

2.  インストルメンテーション キーをコピーします。

    ![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>アプリケーションでの SDK のインストール


1. Visual Studio で、デスクトップ アプリ プロジェクトの NuGet パッケージを編集します。![プロジェクトを右クリックし、[Nuget パッケージの管理] を選択する](./media/app-insights-windows-desktop/03-nuget.png)

2. Application Insights API パッケージをインストールします。

    ![Search for "Application Insights"](./media/app-insights-windows-desktop/04-core-nuget.png)

3. `TelemetryConfiguration.Active` オブジェクトを使用して、InstrumentationKey をコードに設定します。

    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

## <a name="telemetry"></a>テレメトリの呼び出しの挿入

`TelemetryClient` インスタンスを作成してから、[このインスタンスを使用してテレメトリを送信][api]します。

`TelemetryClient.Flush()` を使用して、アプリを閉じる前にメッセージを送信します Core SDK は、メモリ内バッファーを使用します。フラッシュのメソッドは、プロセスのシャット ダウンでデータ損失が発生しないように、このバッファーを空にすることを確認します。(これは他の種類のアプリには使用しないでください。プラットフォーム SDK では、この動作を自動的に実装します。)

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
            }
            base.OnClosing(e);
        }

```

テレメトリを送信するには、[Application Insights API][api] のいずれかを使用します。Windows デスクトップ アプリケーションでは、テレメトリが自動的に送信されることはありません。通常は次のものを使用します。

* フォーム、ページ、またはタブの切り替えには TrackPageView(pageName)。
* その他のユーザーの操作には TrackEvent(eventName)。
* 特定のイベントに関連付けられていないメトリックの標準レポートをバックグラウンド タスクで送信するには TrackMetric(name, value)。
* [診断ログの記録][diagnostic]には TrackTrace(logEvent)。
* Catch 句では TrackException(exception)。

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

[診断検索] に最初のイベントが表示されます。

大量のデータが予想される場合は、数秒後に [最新の情報に更新] をクリックします。

TrackMetric、または TrackEvent の測定値パラメーターを使用した場合は、[メトリックス エクスプローラー][metrics]を開き、フィルター ブレードを開くと、メトリックが表示されます。



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
 

<!---HONumber=July15_HO4-->