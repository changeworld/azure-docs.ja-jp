---
title: Windows デスクトップ アプリでの使用状況とパフォーマンスの監視
description: Application Insights を使用して、Windows デスクトップ アプリの使用状況とパフォーマンスを分析します。
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: eb9e0fc480098478a3a68265ac85e0d5450e27fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537391"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>従来の Windows デスクトップ アプリケーションでの使用状況とパフォーマンスの監視

Application Insights は、オンプレミスや Azure、その他各種クラウドでホストされているすべてのアプリケーションが活用ができます。 唯一の制約は、Application Insights サービスとの[通信を許可](../../azure-monitor/app/ip-addresses.md)する必要があることです。 ユニバーサル Windows プラットフォーム (UWP) アプリケーションの監視には、[Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md) をお勧めします。

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>従来の Windows アプリケーションから Application Insights にテレメトリを送信するには
1. [Azure Portal](https://portal.azure.com) で、[Application Insights のリソースを作成します](../../azure-monitor/app/create-new-resource.md )。 アプリケーションの種類として ASP.NET アプリを選択します。
2. インストルメンテーション キーをコピーします。 先ほど作成した新しいリソースの [要点] ボックスの一覧で、キーを探します。 
3. Visual Studio でアプリ プロジェクトの NuGet パッケージを編集し、Microsoft.ApplicationInsights.WindowsServer を追加します (または、標準テレメトリ コレクション モジュールを含まないベア API だけが必要な場合は、Microsoft.ApplicationInsights を選択します)。
4. インストルメンテーション キーの設定はコードまたは ApplicationInsights.config で行います。コードの場合:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *自分のキー* `";`
   
    ApplicationInsights.config の場合 (いずれかの標準テレメトリ パッケージをインストールした場合):
   
    `<InstrumentationKey>`*自分のキー*`</InstrumentationKey>` 
   
    ApplicationInsights.config を使用する場合は、ソリューション エクスプローラーでプロパティが **Build Action = Content、Copy to Output Directory = Copy**に設定されていることを確認します。
5. [API を使用して](../../azure-monitor/app/api-custom-events-metrics.md) テレメトリを送信します。
6. アプリケーションを実行し、Azure portal で作成したリソースのテレメトリを表示します。

## <a name="example-code"></a><a name="telemetry"></a>コード例
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>コンピューター名のストレージを上書きする

既定では、この SDK を使うと、テレメトリを出力するシステムのコンピューター名が収集されて保存されます。 コレクションを上書きするには、テレメトリ初期化子を使用する必要があります。

**以下のようにカスタム TelemetryInitializer を作成します。**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
`Program.cs` の `Main()` メソッドのインストルメンテーション キーの設定の下で、初期化子をインスタンス化します。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>次のステップ
* [ダッシュボードを作成する](../../azure-monitor/app/overview-dashboard.md)
* [診断検索](../../azure-monitor/app/diagnostic-search.md)
* [メトリックを探索する](../../azure-monitor/platform/metrics-charts.md)
* [Analytics クエリを作成する](../../azure-monitor/app/analytics.md)

