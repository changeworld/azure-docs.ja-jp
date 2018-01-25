---
title: "Windows デスクトップ アプリでの使用状況とパフォーマンスの監視"
description: "HockeyApp と Application Insights を使用して、Windows デスクトップ アプリの使用状況とパフォーマンスを分析します。"
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: mbullwin
ms.openlocfilehash: 3b91996c12a27847befcacf6daf14457a0e066aa
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Windows デスクトップ アプリでの使用状況とパフォーマンスの監視


[Azure Application Insights](app-insights-overview.md) と [HockeyApp](https://hockeyapp.net) を使用すると、デプロイされたアプリケーションの使用状況とパフォーマンスを監視できます。

> [!IMPORTANT]
> デスクトップ アプリとデバイス アプリを配布および監視するには、 [HockeyApp](https://hockeyapp.net) を使うことをお勧めします。 HockeyApp を使用すると、配布、ライブ テスト、ユーザーからのフィードバックの管理だけでなく、使用状況とクラッシュ レポートの監視も行うことができます。 [Analytics を使用してテレメトリのエクスポートやクエリを実行する](app-insights-hockeyapp-bridge-app.md)こともできます。
> 
> デスクトップ アプリケーションから Application Insights にテレメトリを送信できますが、これは主にデバッグと実験の目的に適しています。
> 
> 

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Windows アプリケーションからテレメトリを Application Insights に送信するには
1. [Azure Portal](https://portal.azure.com) で、[Application Insights のリソースを作成します](app-insights-create-new-resource.md)。 アプリケーションの種類として ASP.NET アプリを選択します。
2. インストルメンテーション キーをコピーします。 先ほど作成した新しいリソースの [要点] ボックスの一覧で、キーを探します。 
3. Visual Studio でアプリ プロジェクトの NuGet パッケージを編集し、Microsoft.ApplicationInsights.WindowsServer を追加します (または、標準テレメトリ コレクション モジュールを含まないベア API だけが必要な場合は、Microsoft.ApplicationInsights を選択します)。
4. インストルメンテーション キーの設定はコードまたは ApplicationInsights.config で行います。コードの場合:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *自分のキー* `";` 
   
    ApplicationInsights.config の場合 (いずれかの標準テレメトリ パッケージをインストールした場合):
   
    `<InstrumentationKey>`*自分のキー*`</InstrumentationKey>` 
   
    ApplicationInsights.config を使用する場合は、ソリューション エクスプローラーでプロパティが **Build Action = Content、Copy to Output Directory = Copy**に設定されていることを確認します。
5. [API を使用して](app-insights-api-custom-events-metrics.md) テレメトリを送信します。
6. アプリケーションを実行し、Azure Portal で作成したリソースのテレメトリを表示します。

## <a name="telemetry"></a>コード例
```csharp

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

## <a name="next-steps"></a>次の手順
* [ダッシュボードを作成する](app-insights-dashboards.md)
* [診断検索](app-insights-diagnostic-search.md)
* [メトリックを探索する](app-insights-metrics-explorer.md)
* [Analytics クエリを作成する](app-insights-analytics.md)

