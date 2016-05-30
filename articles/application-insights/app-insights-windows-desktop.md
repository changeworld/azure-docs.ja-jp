<properties 
	pageTitle="Windows デスクトップ アプリでの使用状況とパフォーマンスの監視" 
	description="HockeyApp と Application Insights を使用して、Windows デスクトップ アプリの使用状況とパフォーマンスを分析します。" 
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

# Windows デスクトップ アプリでの使用状況とパフォーマンスの監視

*Application Insights はプレビュー段階です。*

[Visual Studio Application Insights](app-insights-get-started.md) と [HockeyApp](https://hockeyapp.net) を使用すると、デプロイされたアプリケーションの使用状況とパフォーマンスを監視できます。

> [AZURE.IMPORTANT] デスクトップ アプリとデバイス アプリを配布および監視するには、[HockeyApp](https://hockeyapp.net) を使うことをお勧めします。HockeyApp を使用すると、配布、ライブ テスト、ユーザーからのフィードバックの管理だけでなく、使用状況とクラッシュ レポートの監視も行うことができます。

> デスクトップ アプリケーションから Application Insights にテレメトリを送信できますが、これは主にデバッグと実験の目的に適しています。


## Windows アプリケーションからテレメトリを Application Insights に送信するには

1. [Azure ポータル](https://portal.azure.com)で、Application Insights の新しいリソースを作成します。アプリケーションの種類として ASP.NET アプリを選択します。
2. インストルメンテーション キーをコピーします。先ほど作成した新しいリソースの [要点] ボックスの一覧で、キーを探します。アプリケーション マップを閉じるか、左側にあるリソースの概要ブレードまでスクロールします。
3. Visual Studio でアプリ プロジェクトの NuGet パッケージを編集し、Microsoft.ApplicationInsights.WindowsServer を追加します(または、標準テレメトリ コレクション モジュールを含まないベア API だけが必要な場合は、Microsoft.ApplicationInsights を選択します)。
4. インストルメンテーション キーの設定はコードまたは ApplicationInsights.config で行います。コードの場合:

    `TelemetryConfiguration.Active.InstrumentationKey = "` *your key* `";`

    ApplicationInsights.config の場合 (いずれかの標準テレメトリ パッケージをインストールした場合):
 
    `<InstrumentationKey>`*your key*`</InstrumentationKey>`

    ApplicationInsights.config を使用する場合は、ソリューション エクスプローラーでプロパティが **Build Action = Content、Copy to Output Directory = Copy** に設定されていることを確認します。
5. [API を使用して](app-insights-api-custom-events-metrics.md)テレメトリを送信します。
6. アプリケーションを実行し、Azure ポータルで作成したリソースのテレメトリを表示します。

## <a name="telemetry"></a>コード例

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
            tc.Context.User.Id = Environment.UserName;
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


 

<!---HONumber=AcomDC_0518_2016-->