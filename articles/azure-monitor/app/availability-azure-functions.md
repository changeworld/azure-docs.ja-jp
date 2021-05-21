---
title: Azure Functions を使用してカスタム可用性テストを作成して実行する
description: このドキュメントでは、TimerTrigger 関数で指定された構成に従って定期的に実行される Azure 関数を TrackAvailability() で作成する方法について説明します。 このテストの結果は、Application Insights リソースに送信されます。そこでは、可用性の結果データに対してクエリを実行し、アラートを生成することができます。 カスタマイズされたテストを使用すると、ポータル UI を使用してできるものより複雑な可用性テストを記述したり、Azure VNET 内のアプリを監視したり、エンドポイントのアドレスを変更したり、リージョンで利用できない場合に可用性テストを作成したりすることができます。
ms.topic: conceptual
ms.date: 05/06/2021
ms.openlocfilehash: 891f69bd42dc228e01e7b341630e7f37522f9828
ms.sourcegitcommit: c1b0d0b61ef7635d008954a0d247a2c94c1a876f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2021
ms.locfileid: "109628189"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Functions を使用してカスタム可用性テストを作成して実行する

この記事では、独自のビジネス ロジックを使用して TimerTrigger 関数で指定された構成に従って定期的に実行される Azure 関数を TrackAvailability() で作成する方法について説明します。 このテストの結果は、Application Insights リソースに送信されます。そこでは、可用性の結果データに対してクエリを実行し、アラートを生成することができます。 これにより、ポータルでの[可用性の監視](./monitor-web-app-availability.md)を使用して実行できるものと同様のカスタマイズされたテストを作成できます。 カスタマイズされたテストを使用すると、ポータル UI を使用してできるものより複雑な可用性テストを記述したり、Azure VNET 内のアプリを監視したり、エンドポイントのアドレスを変更したり、リージョンでこの機能を利用できない場合でも可用性テストを作成したりすることができます。

> [!NOTE]
> この例は、Azure Functions 内で TrackAvailability () API 呼び出しが機能する仕組みを示すためだけに設計されています。 基になる HTTP テスト コード/ビジネス ロジックを記述する方法を示すものではありません (それは、これを完全に機能する可用性テストにする場合に必要になります)。 既定で、この例を実行すると、基本的な可用性 HTTP GET テストが作成されます。

## <a name="create-a-timer-trigger-function"></a>タイマー トリガー関数を作成する

1. Azure Functions リソースを作成します。
    - Application Insights リソースが既にある場合:
        - Azure Functions では既定で Application Insights リソースが作成されますが、既に作成されているリソースのいずれかを使用したい場合は、作成時にそれを指定する必要があります。
        - [Azure Functions リソースの作成](../../azure-functions/functions-create-scheduled-function.md#create-a-function-app)方法の手順を実行し、次の変更を行ってください。
            - **[監視]** タブで、Application Insights のドロップダウン ボックスを選択し、リソースの名前を入力または選択します。
                :::image type="content" source="media/availability-azure-functions/app-insights-resource.png" alt-text="[監視] タブで、既存の Application Insights リソースを選択します。":::
    - タイマーによってトリガーされる関数用の Application Insights リソースをまだ作成していない場合:
        - 既定では、Azure Functions アプリケーションを作成するときに、Application Insights リソースが自動的に作成されます。 [Azure Functions リソースの作成](../../azure-functions/functions-create-scheduled-function.md#create-a-function-app)方法の手順を実行します。
    > [!NOTE]
    > 関数は、従量課金、Premium、または App Service プランでホストできます。 V-Net の背後でテストする場合、または非パブリック エンドポイントをテストする場合は、従量課金の代わりに Premium プランを使用する必要があります。 **[ホスティング]** タブでプランを選択します。
2. タイマー トリガー関数を作成します。
    1. 該当する関数アプリで、 **[関数]** タブを選択します。
    1. **[追加]** を選択し、[関数の追加] タブで次の構成を選択します。
        1. 開発環境: *[Develop in portal]\(ポータルで開発\)*
        1. テンプレートの選択: *[タイマー トリガー]*
    1. **[追加]** を選択して、タイマー トリガー関数を作成します。

    :::image type="content" source="media/availability-azure-functions/add-function.png" alt-text="関数アプリにタイマー トリガー関数を追加する方法を示すスクリーンショット。" lightbox="media/availability-azure-functions/add-function.png":::

## <a name="add-and-edit-code-in-the-app-service-editor"></a>App Service Editor でコードを追加して編集する

デプロイした関数アプリに移動し、 *[開発ツール]* で **[App Service Editor]** タブを選択します。

新しいファイルを作成するには、タイマー トリガー関数の下 (たとえば "TimerTrigger1") を右クリックし、 **[新しいファイル]** を選択します。 次に、ファイルの名前を入力し、Enter キーを押します。

1. "function.proj" という新しいファイルを作成し、次のコードを貼り付けます。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk"> 
        <PropertyGroup> 
            <TargetFramework>netstandard2.0</TargetFramework> 
        </PropertyGroup> 
        <ItemGroup> 
            <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version --> 
        </ItemGroup> 
    </Project> 
    
    ```

     :::image type="content" source="media/availability-azure-functions/function-proj.png" alt-text="App Service Editor 内の function.proj のスクリーンショット。" lightbox="media/availability-azure-functions/function-proj.png":::

2. "runAvailabilityTest.csx" という新しいファイルを作成し、次のコードを貼り付けます。

    ```csharp
    using System.Net.Http; 
    
    public async static Task RunAvailabilityTestAsync(ILogger log) 
    { 
        using (var httpClient = new HttpClient()) 
        { 
            // TODO: Replace with your business logic 
            await httpClient.GetStringAsync("https://www.bing.com/"); 
        } 
    } 
    ```

3. 次のコードを run.csx ファイルにコピーします (これによって、既存のコードが置き換えられます)。

    ```csharp
    #load "runAvailabilityTest.csx" 
    
    using System; 
    
    using System.Diagnostics; 
    
    using Microsoft.ApplicationInsights; 
    
    using Microsoft.ApplicationInsights.Channel; 
    
    using Microsoft.ApplicationInsights.DataContracts; 
    
    using Microsoft.ApplicationInsights.Extensibility; 
    
    private static TelemetryClient telemetryClient; 
    
    // ============================================================= 
    
    // ****************** DO NOT MODIFY THIS FILE ****************** 
    
    // Business logic must be implemented in RunAvailabilityTestAsync function in runAvailabilityTest.csx 
    
    // If this file does not exist, please add it first 
    
    // ============================================================= 
    
    public async static Task Run(TimerInfo myTimer, ILogger log, ExecutionContext executionContext) 
    
    { 
        if (telemetryClient == null) 
        { 
            // Initializing a telemetry configuration for Application Insights based on connection string 
    
            var telemetryConfiguration = new TelemetryConfiguration(); 
            telemetryConfiguration.ConnectionString = Environment.GetEnvironmentVariable("APPLICATIONINSIGHTS_CONNECTION_STRING"); 
            telemetryConfiguration.TelemetryChannel = new InMemoryChannel(); 
            telemetryClient = new TelemetryClient(telemetryConfiguration); 
        } 
    
        string testName = executionContext.FunctionName; 
        string location = Environment.GetEnvironmentVariable("REGION_NAME"); 
        var availability = new AvailabilityTelemetry 
        { 
            Name = testName, 
    
            RunLocation = location, 
    
            Success = false, 
        }; 
    
        availability.Context.Operation.ParentId = Activity.Current.SpanId.ToString(); 
        availability.Context.Operation.Id = Activity.Current.RootId; 
        var stopwatch = new Stopwatch(); 
        stopwatch.Start(); 
    
        try 
        { 
            using (var activity = new Activity("AvailabilityContext")) 
            { 
                activity.Start(); 
                availability.Id = Activity.Current.SpanId.ToString(); 
                // Run business logic 
                await RunAvailabilityTestAsync(log); 
            } 
            availability.Success = true; 
        } 
    
        catch (Exception ex) 
        { 
            availability.Message = ex.Message; 
            throw; 
        } 
    
        finally 
        { 
            stopwatch.Stop(); 
            availability.Duration = stopwatch.Elapsed; 
            availability.Timestamp = DateTimeOffset.UtcNow; 
            telemetryClient.TrackAvailability(availability); 
            telemetryClient.Flush(); 
        } 
    } 
    
    ```

## <a name="check-availability"></a>可用性を確認する

すべてが動作していることを確認するには、Application Insights リソースの [可用性] タブでグラフを確認します。

> [!NOTE]
> TrackAvailability() を使用して作成されたテストは、テスト名の横に **[CUSTOM]** と表示されます。

 :::image type="content" source="media/availability-azure-functions/availability-custom.png" alt-text="成功した結果が含まれた [可用性] タブ" lightbox="media/availability-azure-functions/availability-custom.png":::

エンドツーエンドのトランザクションの詳細を表示するには、[詳細の表示] で **[成功]** または **[失敗]** を選択してから、サンプルを選択します。 グラフでデータ ポイントを選択することにより、エンドツーエンドのトランザクションの詳細も取得できます。

:::image type="content" source="media/availability-azure-functions/sample.png" alt-text="サンプルの可用性テストを選択する。":::

:::image type="content" source="media/availability-azure-functions/end-to-end.png" alt-text="エンドツーエンド トランザクションの詳細。" lightbox="media/availability-azure-functions/end-to-end.png":::

## <a name="query-in-logs-analytics"></a>ログ (Analytics) でのクエリ

ログ (Analytics) を使用して、可用性の結果、依存関係、その他を見ることができます。 ログの詳細については、[ログ クエリの概要](../logs/log-query-overview.md)に関するページを参照してください。

:::image type="content" source="media/availability-azure-functions/availabilityresults.png" alt-text="可用性の結果。" lightbox="media/availability-azure-functions/availabilityresults.png":::

:::image type="content" source="media/availability-azure-functions/dependencies.png" alt-text="依存関係が 50 に制限された [新しいクエリ] タブが表示されたスクリーンショット。" lightbox="media/availability-azure-functions/dependencies.png":::

## <a name="next-steps"></a>次のステップ

- [アプリケーション マップ](./app-map.md)
- [トランザクションの診断](./transaction-diagnostics.md)
