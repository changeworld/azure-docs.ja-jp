<properties 
	pageTitle="Windows デスクトップ アプリ用の Application Insights" 
	description="Application Insights を使用して Windows アプリの使用状況とパフォーマンスを分析します。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="awills"/>

# Windows デスクトップ アプリの Application Insights

*Application Insights はプレビュー段階です。*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Application Insights を使用すると、デプロイしたアプリケーションの使用状況とパフォーマンスを監視できます。

*Application Insights SDK はデスクトップ アプリで動作するように作られていますが、現在このシナリオはサポートされていません。ただし、試験的に使用する方のために、ここではヒントを示します。*




## <a name="add"></a>Application Insights リソースの作成


1.  [Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。アプリケーションの種類には、ASP.NET アプリまたは Windows ストア アプリを選択します。 

    ![\[新規\]、\[Application Insights\] の順にクリックする](./media/app-insights-windows-get-started/01-new.png)

    \(選択したアプリケーションの種類に応じて、[メトリックス エクスプローラー][metrics]で使用できる概要ブレードのコンテンツとプロパティが設定されます\)

2.  インストルメンテーション キーをコピーします。

    ![\[プロパティ\] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-windows-get-started/02-props.png)

## <a name="sdk"></a>アプリケーションでの SDK のインストール


1. Visual Studio で、デスクトップ アプリ プロジェクトの NuGet パッケージを編集します。![プロジェクトを右クリックし、\[Nuget パッケージの管理\] を選択する](./media/app-insights-windows-get-started/03-nuget.png)

2. Application Insights SDK のコアをインストールします。

    ![**\[オンライン\]**、**\[プレリリースを含める\]** の順に選択し、"Application Insights" を検索する](./media/app-insights-windows-get-started/04-ai-nuget.png)

    \(代わりに、Application Insights SDK for Web Apps を選択することもできます。これによって、パフォーマンス カウンター テレメトリがいくつか組み込まれます。\)

3. \(NuGet のインストールによって追加された\) ApplicationInsights.config を編集します。次のコードを終了タグの直前に挿入します。

    &lt;InstrumentationKey&gt;\*コピーしたキー\*&lt;/InstrumentationKey&gt;

    または、次のコードでも同じ効果が得られます。
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

4. Web Apps 用の SDK をインストールした場合は、ApplicationInsights.config から Web テレメトリ モジュールをコメントアウトすることもお勧めします。

## <a name="telemetry"></a>テレメトリの呼び出しの挿入

`TelemetryClient` インスタンスを作成してから、[このインスタンスを使用してテレメトリを送信][track]します。

`TelemetryClient.Flush` を使用して、アプリを閉じる前にメッセージを送信します \(これは他の種類のアプリには使用しないでください\)。

たとえば、Windows フォーム アプリケーションでは、次のように記述します。

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
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

テレメトリを送信するには、[Application Insights API][track] のいずれかを使用します。Windows デスクトップ アプリケーションでは、テレメトリが自動的に送信されることはありません。通常は次のものを使用します。

* フォーム、ページ、またはタブの切り替えには TrackPageView\(pageName\)。
* その他のユーザーの操作には TrackEvent\(eventName\)。
* [診断ログの記録][diagnostic]には TrackTrace\(logEvent\)。
* Catch 句では TrackException\(exception\)。
* 特定のイベントに関連付けられていないメトリックの標準レポートをバックグラウンド タスクで送信するには TrackMetric\(name, value\)。

ユーザーとセッションの数を表示するには、コンテキストの初期化子を設定します。

    class TelemetryInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = DateTime.Now.ToFileTime().ToString();
            context.Session.IsNewSession = true;
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new TelemetryInitializer());
            ...



## <a name="run"></a>プロジェクトの実行

[F5 キーを押してアプリケーションを実行](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)し、これを使用してテレメトリを生成します。

Visual Studio で、送信されたイベント数が表示されます。

![](./media/appinsights/appinsights-09eventcount.png)



## <a name="monitor"></a>監視データの表示

Azure ポータルで、アプリケーションのブレードに戻ります。

\[診断検索\] に最初のイベントが表示されます。

大量のデータが予想される場合は、数秒後に \[最新の情報に更新\] をクリックします。

TrackMetric、または TrackEvent の測定値パラメーターを使用した場合は、[メトリックス エクスプローラー][metrics]を開き、フィルター ブレードを開くと、メトリックが表示されます。



## <a name="usage"></a>次のステップ

[アプリの使用状況の追跡][track]

[診断ログのキャプチャと検索][diagnostic]

[トラブルシューティング][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md


<!--HONumber=54-->