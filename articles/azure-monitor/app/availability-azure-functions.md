---
title: Azure Functions を使用してカスタム可用性テストを作成して実行する
description: このドキュメントでは、TimerTrigger 関数で指定された構成に従って定期的に実行される Azure 関数を TrackAvailability() で作成する方法について説明します。 このテストの結果は、Application Insights リソースに送信されます。そこでは、可用性の結果データに対してクエリを実行し、アラートを生成することができます。 カスタマイズされたテストを使用すると、ポータル UI を使用してできるものより複雑な可用性テストを記述したり、Azure VNET 内のアプリを監視したり、エンドポイントのアドレスを変更したり、リージョンで利用できない場合に可用性テストを作成したりすることができます。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: 900228e1f9bdf9d367fa37b9ec90a6148faec656
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880253"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Functions を使用してカスタム可用性テストを作成して実行する

この記事では、TimerTrigger 関数で指定された構成に従って定期的に実行される Azure 関数を TrackAvailability() で作成する方法について説明します。 このテストの結果は、Application Insights リソースに送信されます。そこでは、可用性の結果データに対してクエリを実行し、アラートを生成することができます。 これにより、ポータルでの[可用性の監視](../../azure-monitor/app/monitor-web-app-availability.md)を使用して実行できるものと同様のカスタマイズされたテストを作成できます。 カスタマイズされたテストを使用すると、ポータル UI を使用してできるものより複雑な可用性テストを記述したり、Azure VNET 内のアプリを監視したり、エンドポイントのアドレスを変更したり、リージョンでこの機能を利用できない場合でも可用性テストを作成したりすることができます。


## <a name="create-timer-triggered-function"></a>タイマーによってトリガーされる関数を作成する

- Application Insights リソースがある場合:
    - Azure Functions では既定で Application Insights リソースが作成されますが、既に作成されているリソースのいずれかを使用したい場合は、作成時にそれを指定する必要があります。
    - [Azure Functions リソースとタイマーによってトリガーされる関数を作成する](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)方法の手順に従い (クリーンアップの前で止めます)、次のように選択します。
        -  **[作成]** を選択する前に、[Application Insights] セクションをクリックします。

            ![ 独自の App Insights リソースを使用して Azure Functions アプリを作成する](media/availability-azure-functions/create-function-app.png)

        - **[既存のリソースの選択]** をクリックし、リソースの名前を入力します。 **[適用]** を選択します

            ![既存の Application Insights リソースの選択](media/availability-azure-functions/app-insights-resource.png)

        - **[作成]**
- タイマーによってトリガーされる関数用の Application Insights リソースをまだ作成していない場合:
    - 既定では、Azure Functions アプリケーションを作成するときに、Application Insights リソースが自動的に作成されます。
    - [Azure Functions リソースとタイマーによってトリガーされる関数を作成する](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)方法の手順に従います (クリーンアップの前で止めます)。

## <a name="sample-code"></a>サンプル コード

次のコードを run.csx ファイルにコピーします (既存のコードを置き換えます)。 これを行うには、Azure Functions アプリケーションに移動し、左側のタイマー トリガー関数を選択します。

![Azure portal での Azure 関数の run.csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> エンドポイント アドレスには、`EndpointAddress= https://dc.services.visualstudio.com/v2/track` を使用します。 リソースが Azure Government や Azure China などのリージョンにある場合は、[既定のエンド ポイントのオーバーライド](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification)に関する記事を参考にして、お使いのリージョンに適したテレメトリ チャネル エンドポイントを選択してください。

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        //throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

右側の [ファイルの表示] で、 **[追加]** を選択します。 次の構成を使用して、新しいファイル **function.proj** を呼び出します。

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![右側で [追加] を選択する。 ファイルを "function.proj" という名前にする。](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>可用性を確認する

すべてが動作していることを確認するには、Application Insights リソースの [可用性] タブでグラフを確認します。

![成功した結果が表示された [可用性] タブ](media/availability-azure-functions/availtab.png)

Azure Functions を使用してテストを設定した場合、[可用性] タブの **[テストの追加]** を使用した場合とは異なり、テストの名前は表示されず、テストと対話できないことがわかります。 結果は視覚化されますが、ポータルを使用して可用性テストを作成したときと同じ詳細ビューではなく、概要ビューが表示されます。

エンドツーエンドのトランザクションの詳細を表示するには、[詳細の表示] で **[成功]** または **[失敗]** を選択してから、サンプルを選択します。 グラフでデータ ポイントを選択することにより、エンドツーエンドのトランザクションの詳細も取得できます。

![可用性テストのサンプルを選択する](media/availability-azure-functions/sample.png)

![エンドツーエンド トランザクションの詳細](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>ログ (Analytics) でのクエリ

ログ (Analytics) を使用して、可用性の結果、依存関係、その他を見ることができます。 ログの詳細については、[ログ クエリの概要](../../azure-monitor/log-query/log-query-overview.md)に関するページを参照してください。

![可用性の結果](media/availability-azure-functions/availabilityresults.png)

![依存関係](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>次の手順

- [アプリケーション マップ](../../azure-monitor/app/app-map.md)
- [トランザクションの診断](../../azure-monitor/app/transaction-diagnostics.md)
