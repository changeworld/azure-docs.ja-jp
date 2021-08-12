---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: peiliu
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 983ce28f736bd36255e65073b202ff6c5761b059
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292371"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- お使いのオペレーティング システムに対応した最新バージョンの [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../create-communication-resource.md)。
- Azure portal で、[Application Insights リソース](../../../azure-monitor/app/create-new-resource.md)を作成します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`TelemetryAppInsightsQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

```console
dotnet new console -o TelemetryAppInsightsQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用してアプリケーションをコンパイルします。

```console
cd TelemetryAppInsightsQuickstart
dotnet build
```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`dotnet add package` コマンドを使用して、.NET 用の Azure Communication Services ID ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
```

OpenTelemetry 用の Azure Monitor エクスポーター ライブラリもインストールする必要があります。

```console
dotnet add package Azure.Monitor.OpenTelemetry.Exporter
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. テキスト エディターで、**Program.cs** ファイルを開きます
2. `Azure.Communication.Identity` 名前空間を含めるように `using` ディレクティブを追加します
3. `using` ディレクティブを追加して、`Azure.Monitor.OpenTelemetry.Exporter` 名前空間を含めます。
4. `using` ディレクティブを追加して、`OpenTelemetry.Trace` 名前空間を含めます。
5. 非同期コードをサポートするように `Main` メソッドの宣言を更新します

次のコードを使用して開始します。

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry.Trace;

namespace TelemetryAppInsightsQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Export Telemetry to Application Insights");

            // Quickstart code goes here
        }
    }
}
```
## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>Communication ID SDK 呼び出しを使用したテレメトリ トレーサーの設定

接続文字列を使用して `CommunicationIdentityClient` を初期化します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

クライアントが作成されたら、すべてのアクティビティを追跡する `Activity Source` を定義する必要があります。 その後、`Activity Source` を使用して、`CreateUserAsync` SDK 呼び出しの追跡に使用する `Activity` を開始できます。 `SetTag` メソッドを使用して、各 `Activity` 内で追跡するカスタム プロパティを定義することもできます。

`GetTokenAsync` 関数でも同様のトレース パターンが実行されます。

`TracedSample` という新しい関数を作成し、次のコードを追加します。

```csharp
public static async Task TracedSample()
{
    // This code demonstrates how to fetch your connection string
    // from an environment variable.
    string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
    var client = new CommunicationIdentityClient(connectionString);

    using var source = new ActivitySource("Quickstart.IdentityTelemetry");
    CommunicationUserIdentifier identity = null;

    using (var activity = source.StartActivity("Create User Activity"))
    {
        var identityResponse = await client.CreateUserAsync();

        identity = identityResponse.Value;
        Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
        activity?.SetTag("Identity id", identity.Id);
    }

    using (var activity = source.StartActivity("Get Token Activity"))
    {
        var tokenResponse = await client.GetTokenAsync(identity, scopes: new[] { CommunicationTokenScope.Chat });

        activity?.SetTag("Token value", tokenResponse.Value.Token);
        activity?.SetTag("Expires on", tokenResponse.Value.ExpiresOn);

        Console.WriteLine($"\nIssued an access token with 'chat' scope that expires at {tokenResponse.Value.ExpiresOn}:");
    }
}
```

## <a name="funneling-telemetry-data-to-application-insights"></a>Application Insights へのテレメトリ データの投入

SDK 呼び出しがアクティビティでラップされたら、OpenTelemetry トレース エクスポーターを追加し、データを Application Insights リソースに投入できます。

Application Insights に表示されるいくつかのリソース属性を含む辞書を定義することもできます。
次に、`AddSource` を呼び出し、`TracedSample` で定義されているものと同じアクティビティ ソース名を使用します。
また、Application Insights リソースから接続文字列を取得し、`AddAzureMonitorTraceExporter()` に渡す必要があります。 これにより、テレメトリ データが Application Insights リソースに投入されます。

最後に、SDK 呼び出しを含む `TracedSample()` 関数を呼び出して待機します。

`Main` メソッドに次のコードを追加します。

```csharp
var resourceAttributes = new Dictionary<string, object> { { "service.name", "<service-name>" }, { "service.instance.id", "<service-instance-id>" } };
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);

using var tracerProvider = Sdk.CreateTracerProviderBuilder()
    .SetResourceBuilder(resourceBuilder)
    .AddSource("Quickstart.IdentityTelemetry")
    .AddAzureMonitorTraceExporter(o =>
    {
        o.ConnectionString = Environment.GetEnvironmentVariable("APPLICATION_INSIGHTS_CONNECTION_STRING");
    })
    .Build();

await TracedSample();
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```