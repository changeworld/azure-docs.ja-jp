---
title: .NET、Node.js、Python アプリケーション用の Azure Monitor OpenTelemetry を有効にする
description: OpenTelemetry を使用するアプリケーションで Azure Monitor を有効にする方法に関するガイダンスを提供します。
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: ae52a8977247903574b9d23fda795e5fd8b7ea3d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054431"
---
# <a name="enable-azure-monitor-opentelemetry-exporter-for-net-nodejs-and-python-applications-preview"></a>.NET、Node.js、Python アプリケーション用の Azure Monitor OpenTelemetry エクスポーターを有効にする (プレビュー)

この記事では、OpenTelemetry ベースの Azure Monitor プレビュー オファリングを有効にして構成する方法について説明します。 この記事の手順を完了すると、OpenTelemetry トレースを Azure Monitor Application Insights に送信できるようになります。 OpenTelemetry の詳細については [、OpenTelemetry](opentelemetry-overview.md) の概要に関するページまたは [OpenTelemetry に](/azure/azure-monitor/faq#opentelemetry)関する FAQ をご覧ください。

> [!IMPORTANT]
> .NET、Node.js、Python アプリケーション用の Azure Monitor OpenTelemetry エクスポーターは現在、プレビュー段階です。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="limitations-of-preview-release"></a>プレビュー リリースの制限事項

### <a name="net"></a>[.NET](#tab/net)

このプレビューが適切かどうかを慎重に検討してください。 これにより、**分散トレースのみが有効** になり、次のものは "_除外_" されます。
 - メトリック API (カスタム メトリック、[事前に集計されたメトリック](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [ライブ メトリック](live-stream.md)
 - ログ API (コンソール ログやログ ライブラリなど)
 - ハンドルされない例外の自動キャプチャ
 - [Profiler](profiler-overview.md)
 - [スナップショット デバッガー](snapshot-debugger.md)
 - [オフライン ディスク ストレージと再試行ロジック](telemetry-channels.md#built-in-telemetry-channels)
 - [Azure AD 認証](azure-ad-authentication.md)
 - [サンプリング](sampling.md)
 - Azure 環境でのクラウド ロール名とクラウド ロール インスタンスの自動入力
 - [Application Insights JavaScript SDK](javascript.md) を使用しているときのユーザー ID と認証されたユーザー ID の自動入力
 - ユーザー IP の自動入力 (位置属性を決定するため)
 - [操作名](correlation.md#data-model-for-telemetry-correlation)をオーバーライドする機能
 - ユーザー ID または認証されたユーザー ID を手動で設定する機能
 - 操作名の依存関係テレメトリへの伝達
 - Azure Functions での[インストルメンテーション ライブラリ](#instrumentation-libraries)のサポート

全機能のエクスペリエンスが必要なユーザーは、OpenTelemetry ベースのオファリングが成熟するまで、既存の Application Insights [ASP.NET](asp-net.md) または [ASP.NET Core](asp-net-core.md) SDK を使用する必要があります。

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

このプレビューが適切かどうかを慎重に検討してください。 これにより、分散トレースのみが有効になり、次のものは "_除外_" されます。
 - メトリック API (カスタム メトリック、[事前に集計されたメトリック](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [ライブ メトリック](live-stream.md)
 - ログ API (コンソール ログやログ ライブラリなど)
 - ハンドルされない例外の自動キャプチャ
 - [Azure AD 認証](azure-ad-authentication.md)
 - [サンプリング](sampling.md)
 - Azure 環境でのクラウド ロール名とクラウド ロール インスタンスの自動入力
 - [Application Insights JavaScript SDK](javascript.md) を使用しているときのユーザー ID と認証されたユーザー ID の自動入力
 - ユーザー IP の自動入力 (位置属性を決定するため)
 - [操作名](correlation.md#data-model-for-telemetry-correlation)をオーバーライドする機能
 - ユーザー ID または認証されたユーザー ID を手動で設定する機能
 - 操作名の依存関係テレメトリへの伝達

全機能のエクスペリエンスが必要なユーザーは、OpenTelemetry ベースのオファリングが成熟するまで、既存の [Application Insights Node.js SDK](nodejs.md) を使用する必要があります。

> [!WARNING] 
> 現時点では、このエクスポーターは Node.js 環境でのみ機能します。 Web またはブラウザーのシナリオには、[Application Insights JavaScript SDK](javascript.md) を使用してください。

### <a name="python"></a>[Python](#tab/python)

このプレビューが適切かどうかを慎重に検討してください。 これにより、**分散トレースのみが有効** になり、次のものは "_除外_" されます。
 - メトリック API (カスタム メトリック、[事前に集計されたメトリック](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [ライブ メトリック](live-stream.md)
 - ログ API (コンソール ログやログ ライブラリなど)
 - ハンドルされない例外の自動キャプチャ
 - オフラインのディスクストレージと再試行ロジック
 - [Azure AD 認証](azure-ad-authentication.md)
 - [サンプリング](sampling.md)
 - Azure 環境でのクラウド ロール名とクラウド ロール インスタンスの自動入力
 - [Application Insights JavaScript SDK](javascript.md) を使用しているときのユーザー ID と認証されたユーザー ID の自動入力
 - ユーザー IP の自動入力 (位置属性を決定するため)
 - [操作名](correlation.md#data-model-for-telemetry-correlation)をオーバーライドする機能
 - ユーザー ID または認証されたユーザー ID を手動で設定する機能
 - 操作名の依存関係テレメトリへの伝達
 - Azure Functions での[インストルメンテーション ライブラリ](#instrumentation-libraries)のサポート

全機能のエクスペリエンスが必要なユーザーは、OpenTelemetry ベースのオファリングが成熟するまで、既存の [Application Insights Python-OpenCensus SDK](opencensus-python.md) を使用する必要があります。

---

## <a name="get-started"></a>はじめに

このセクションの手順に従うと、OpenTelemetry を使用してアプリケーションをインストルメント化できます。

### <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [Azure サブスクリプションを無料で作成する](https://azure.microsoft.com/free/)
- Application Insights リソース - [Application Insights リソースを作成する](create-workspace-resource.md#create-workspace-based-resource)

### <a name="net"></a>[.NET](#tab/net)

- 正式にサポートされているバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet) または [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) >= `.NET Framework 4.6.1` を使用するアプリケーション。


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- 正式に[サポートされているバージョン](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)の Node.js ランタイムを使用するアプリケーション。
  - [OpenTelemetry でサポートされているランタイム](https://github.com/open-telemetry/opentelemetry-js#supported-runtimes)
  - [Azure Monitor OpenTelemetry エクスポーターでサポートされているランタイム](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)

### <a name="python"></a>[Python](#tab/python)

- バージョン 3.6 以上を使用する Python アプリケーション


---

### <a name="install-the-client-libraries"></a>クライアント ライブラリをインストールする

#### <a name="net"></a>[.NET](#tab/net)

最新の [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) NuGet パッケージをインストールします。

```dotnetcli
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter 
```

"パッケージ 'Azure.Monitor.OpenTelemetry.Exporter' に使用できるバージョンがありません。" などのエラーが表示される場合は、NuGet パッケージ ソースの設定がないためである可能性が最も高くなります。 `-s` オプションを使用してソースを指定してみることもできます。

```dotnetcli
# Install the latest package with NuGet package source specified
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter -s https://api.nuget.org/v3/index.json
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

次のパッケージをインストールします。

- [@opentelemetry/sdk-trace-base](https://www.npmjs.com/package/@opentelemetry/sdk-trace-base)
- [@opentelemetry/sdk-trace-node](https://www.npmjs.com/package/@opentelemetry/sdk-trace-node)
- [@azure/monitor-opentelemetry-exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)

```sh
npm install @opentelemetry/sdk-trace-base
npm install @opentelemetry/sdk-trace-node
npm install @azure/monitor-opentelemetry-exporter
```

この記事の後の方で説明されるいくつかの特定のシナリオでは、次のパッケージも使用されます。

- [@opentelemetry/api](https://www.npmjs.com/package/@opentelemetry/api)
- [@opentelemetry/resources](https://www.npmjs.com/package/@opentelemetry/resources)
- [@opentelemetry/semantic-conventions](https://www.npmjs.com/package/@opentelemetry/semantic-conventions)
- [@opentelemetry/instrumentation-http](https://www.npmjs.com/package/@opentelemetry/instrumentation-http)

```sh
npm install @opentelemetry/api
npm install @opentelemetry/resources
npm install @opentelemetry/semantic-conventions
npm install @opentelemetry/instrumentation-http
```

#### <a name="python"></a>[Python](#tab/python)

最新の [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) Pypi パッケージをインストールします。

```sh
pip install azure-monitor-opentelemetry-exporter 
```

---

### <a name="enable-azure-monitor-application-insights"></a>Azure Monitor Application Insights を有効にする

#### <a name="add-opentelemetry-instrumentation-code"></a>OpenTelemetry インストルメンテーション コードを追加する

##### <a name="net"></a>[.NET](#tab/net)

次のコードは、OpenTelemetry TracerProvider を設定することによって、C# コンソールアプリケーションで OpenTelemetry を有効にする方法を示しています このコードは、アプリケーションの起動時に存在する必要があります。 たとえば ASP.NET Core、通常はアプリケーション クラスの `ConfigureServices` メソッドで行 `Startup` われます。 アプリケーション ASP.NET、通常は で行われます `Global.asax.cs` 。

```csharp
using System.Diagnostics;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class Program
{
    private static readonly ActivitySource MyActivitySource = new ActivitySource(
        "OTel.AzureMonitor.Demo");

    public static void Main()
    {
        using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddAzureMonitorTraceExporter(o =>
            {
                o.ConnectionString = "<Your Connection String>";
            })
            .Build();

        using (var activity = MyActivitySource.StartActivity("TestActivity"))
        {
            activity?.SetTag("CustomTag1", "Value1");
            activity?.SetTag("CustomTag2", "Value2");
        }

        System.Console.WriteLine("Press Enter key to exit.");
        System.Console.ReadLine();
    }
}
```

> [!NOTE]
> `System.Diagnostics` 名前空間の `Activity` および `ActivitySource` クラスは、それぞれ `Span` と `Tracer` の OpenTelemetry での概念を表します。 また、`ActivitySource` は、`TracerProvider` を使用するのではなく、コンストラクターを使用して直接作成します (各 [`ActivitySource`](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/trace/customizing-the-sdk#activity-source) は、`AddSource()` を使用して `TracerProvider` に明示的に接続されている必要があります)。 それは、OpenTelemetry トレース API の一部が .NET ランタイムに直接組み込まれるためです。 [詳細については、こちらを参照してください](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Api/README.md#introduction-to-opentelemetry-net-tracing-api)。

##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

次のコードは、単純な Node.js アプリケーションでの OpenTelemetry の有効化を示しています。

```typescript
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { BatchSpanProcessor, Span } = require("@opentelemetry/sdk-trace-base");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
provider.register();

// Create an exporter instance
const exporter = new AzureMonitorTraceExporter({
  instrumentationKey: "<Your Connection String>"
});

// Add the exporter to the provider
provider.addSpanProcessor(
  new BatchSpanProcessor(exporter, {
    bufferTimeout: 15000,
    bufferSize: 1000
  })
);
// Create a span. A span must be closed.
const parentSpan = tracer.startSpan("main");
for (let i = 0; i < 10; i += 1) {
   doWork(parentSpan);
}
// Be sure to end the span.
parentSpan.end();

function doWork(parent: Span) {
  // Start another span. In this example, the main method already started a
  // span, so that'll be the parent span, and this will be a child span.
  const ctx = opentelemetry.trace.setSpan(opentelemetry.context.active(), parent);
  const span = tracer.startSpan("doWork", undefined, ctx);
  // simulate some random work.
  for (let i = 0; i <= Math.floor(Math.random() * 40000000); i += 1) {
    // empty
  }
  // Set attributes to the span.
  span.setAttribute("key", "value");
  // Annotate our span to capture metadata about our operation
  span.addEvent("invoking doWork");
  span.end();
}
```

##### <a name="python"></a>[Python](#tab/python)

次のコードは、単純な Python アプリケーションでの OpenTelemetry の有効化を示しています。

```python
import os
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)
span_processor = BatchSpanProcessor(exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("hello"):
    print("Hello, World!")

```

---

> [!TIP]
> イン [ストルメンテーション ライブラリを](#instrumentation-libraries) 追加して、一般的なフレームワーク/ライブラリ間でテレメトリを自動収集します。

#### <a name="set-application-insights-connection-string"></a>Application Insights の接続文字列を設定する

上のコードにあるプレースホルダー `<Your Connection String>` を、実際の Application Insights リソースの接続文字列に置き換えます。

:::image type="content" source="media/opentelemetry/connection-string.png" alt-text="Application Insights の接続文字列のスクリーンショット。":::

#### <a name="confirm-data-is-flowing"></a>データが流れていることを確認する

アプリケーションを実行し、Azure portal で [Application Insights リソース] タブを開きます。 ポータルにデータが表示されるまでに数分かかることがあります。

> [!NOTE]
> アプリケーションを実行できない場合、またはデータが期待どおりに取得されない場合は、「[トラブルシューティング](#troubleshooting)」に進んでください。

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="サーバー要求とサーバー応答時間が強調表示されている [Application Insights の概要] タブのスクリーンショット。":::

> [!IMPORTANT]
> 同じ Application Insights リソースに 2 つ以上のサービスがテレメトリを出力している場合は、それらをアプリケーション マップで正しく表すために、[クラウド ロール名を設定する](#set-cloud-role-name-and-cloud-role-instance)必要があります。

> [!NOTE]
> Application Insights インストルメンテーションの使用時に、診断データが収集され、Microsoft に送信されます。 このデータは、Application Insights の実行と改善に役立ちます。 重要でないデータ収集を無効にするオプションがあります。 [詳細については、こちらを参照してください](./statsbeat.md)。

## <a name="set-cloud-role-name-and-cloud-role-instance"></a>クラウド ロール名とクラウド ロール インスタンスを設定する

[[リソース]](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/sdk.md#resource-sdk) 属性を使用して、[クラウド ロール名](app-map.md#understanding-cloud-role-name-within-the-context-of-the-application-map)とクラウド ロール インスタンスを設定できます。 これにより、クラウド ロール名とクラウド ロール インスタンスが、その既定値からチームにとって意味があるものに更新されます。 これは、アプリケーション マップのノードの下に名前として表示されます。 クラウド ロール名は `service.namespace` および `service.name` 属性を使用しますが、`service.namespace` が設定されていない場合は `service.name` にフォールバックします。 クラウド ロール インスタンスは `service.instance.id` 属性値を使用します。

### <a name="net"></a>[.NET](#tab/net)

```csharp
// Setting Role name and Role instance
var resourceAttributes = new Dictionary<string, object> {
    { "service.name", "my-service" },
    { "service.namespace", "my-namespace" },
    { "service.instance.id", "my-instance" }};
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);
// Done setting Role name and Role instance

// Set ResourceBuilder on the provider
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .SetResourceBuilder(resourceBuilder)
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>";
        })
        .Build();
```


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```typescript
...
import { NodeTracerProvider, NodeTracerConfig } from "@opentelemetry/sdk-trace-node";
import { Resource } from "@opentelemetry/resources";
import { SemanticResourceAttributes } from "@opentelemetry/semantic-conventions";

// ----------------------------------------
// Setting Role name and role instance
// ----------------------------------------
const config: NodeTracerConfig = {
        resource: new Resource({
            [SemanticResourceAttributes.SERVICE_NAME]: "my-helloworld-service",
            [SemanticResourceAttributes.SERVICE_NAMESPACE]: "my-namespace",
            [SemanticResourceAttributes.SERVICE_INSTANCE_ID]: "my-instance",
        }),
    };
// ----------------------------------------
// Done setting Role name and role instance
// ----------------------------------------
const provider = new NodeTracerProvider(config);
...
```

### <a name="python"></a>[Python](#tab/python)

```python
...
from opentelemetry.sdk.resources import SERVICE_NAME, SERVICE_NAMESPACE, SERVICE_INSTANCE_ID, Resource
trace.set_tracer_provider(
    TracerProvider(
        resource=Resource.create(
            {
                SERVICE_NAME: "my-helloworld-service",
# ----------------------------------------
# Setting Role name and role instance
# ----------------------------------------
                SERVICE_NAMESPACE: "my-namespace",
                SERVICE_INSTANCE_ID: "my-instance",
# ----------------------------------------------
# Done setting Role name and role instance
# ----------------------------------------------
            }
        )
    )
)
...
```

---

リファレンス: [リソースのセマンティック規則](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/semantic_conventions/README.md)

## <a name="sampling"></a>サンプリング

サンプリングは OpenTelemetry でサポートされていますが、現時点で Azure Monitor OpenTelemetry エクスポーターではサポートされていません。

> [!WARNING]
> OpenTelemetry でサンプリングを有効にすると、標準およびログ ベースのメトリックがきわめて不正確になるため、すべての Application Insights エクスペリエンスに悪影響を与えます。 さらに、既存の Application Insights SDK と共にサンプリングを有効にすると、トレースが破損します。

## <a name="instrumentation-libraries"></a>インストルメンテーション ライブラリ
<!-- Microsoft has tested and validated that the following instrumentation libraries will work with the **Preview** Release. -->
次のライブラリは、プレビュー リリースで動作することが検証されています。

> [!WARNING]
> インストルメンテーション ライブラリは、試験的な OpenTelemetry の仕様に基づいています。 Microsoft の **プレビュー** サポート コミットメントでは、下に一覧表示されているライブラリが Azure Monitor Application Insights にデータを出力することを保証しますが、破壊的変更や試験的なマッピングによって一部のデータ要素がブロックされる可能性があります。

### <a name="http"></a>HTTP

#### <a name="net"></a>[.NET](#tab/net)

- [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md) バージョン: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNet/1.0.0-rc7)
- [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md) バージョン: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNetCore/1.0.0-rc7)
- [HTTP クライアント](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md) バージョン: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.Http/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- [HTTP/HTTPS](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http/README.md) バージョン: [0.26.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-http/v/0.26.0)


#### <a name="python"></a>[Python](#tab/python)

- [Django](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-django) バージョン: [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-django/0.24b0/)
- [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask) バージョン: [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-flask/0.24b0/)
- [要求](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-requests) バージョン: [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-requests/0.24b0/)

---

### <a name="database"></a>データベース

#### <a name="net"></a>[.NET](#tab/net)

- [SQL クライアント](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.SqlClient/README.md) バージョン: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.SqlClient/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- [mysql](https://github.com/open-telemetry/opentelemetry-js-contrib/tree/main/plugins/node/opentelemetry-instrumentation-mysql) バージョン: [0.25.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-mysql/v/0.25.0)

#### <a name="python"></a>[Python](#tab/python)

- [Psycopg2](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-psycopg2) バージョン: [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-psycopg2/0.24b0/)

---

> [!NOTE]
> **プレビュー** オファリングには、HTTP およびデータベース要求を処理するインストルメンテーションのみが含まれます。 詳細については、[OpenTelemetry のセマンティック規則](https://github.com/open-telemetry/opentelemetry-specification/tree/main/specification/trace/semantic_conventions)を参照してください。

## <a name="modify-telemetry"></a>テレメトリの変更

### <a name="add-span-attributes"></a>スパン属性を追加する

スパン属性は、次の 2 つの方法のどちらかを使用して追加できます。
1. [インストルメンテーション ライブラリ](#instrumentation-libraries)によって提供されるオプションの使用。
2. カスタム スパン プロセッサの追加。

これらの属性には、テレメトリへのカスタム プロパティの追加が含まれる可能性があります。 また、属性を使用して、Application Insights のスキーマの [クライアント IP] などの省略可能なフィールドを設定することもできます。

> [!TIP]
> "インストルメンテーション ライブラリによって提供されるオプション" (使用可能な場合) を使用する利点は、コンテキスト全体を使用できることです。つまり、ユーザーは属性を追加するか、または追加の属性をフィルター処理するかを選択できます。 たとえば、HttpClient インストルメンテーション ライブラリに含まれている強化オプションにより、ユーザーは httpRequestMessage 自体にアクセスして、そこから任意のものを選択し、それを属性として格納できます。

#### <a name="add-custom-property"></a>カスタム プロパティを追加する

アクティビティまたはスパンに追加された[属性](#add-span-attributes)はすべて、カスタム プロパティとしてエクスポートされます。 これにより、Application Insights の要求または依存関係テーブル内の _customDimensions_ フィールドにデータが入力されます。

##### <a name="net"></a>[.NET](#tab/net)

1. 多くのインストルメンテーション ライブラリには、強化オプションが用意されています。 ガイダンスについては、個々のインストルメンテーション ライブラリの Readme を参照してください。
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#enrich)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#enrich)
    - [HttpClient および HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#enrich)

2. カスタム プロセッサの使用:

> [!TIP]
> 下に示すプロセッサは、Azure Monitor エクスポーターの前に追加します。

```csharp
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddProcessor(new ActivityEnrichingProcessor())
        .AddAzureMonitorTraceExporter(o =>
        {
                o.ConnectionString = "<Your Connection String>"
        })
        .Build();
```

次のコードを含む `ActivityEnrichingProcessor.cs` をプロジェクトに追加します。

```csharp
using System.Diagnostics;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class ActivityEnrichingProcessor : BaseProcessor<Activity>
{
    public override void OnEnd(Activity activity)
    {
        // The updated activity will be available to all processors which are called after this processor.
        activity.DisplayName = "Updated-" + activity.DisplayName;
        activity.SetTag("CustomDimension1", "Value1");
        activity.SetTag("CustomDimension2", "Value2");
    }
}
```


##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

カスタム プロセッサの使用:

> [!TIP]
> 下に示すプロセッサは、Azure Monitor エクスポーターの前に追加します。

```typescript
import { AzureMonitorTraceExporter } from "@azure/monitor-opentelemetry-exporter";
import { NodeTracerProvider } from "@opentelemetry/sdk-trace-node";
import { ReadableSpan, SimpleSpanProcessor, Span, SpanProcessor } from "@opentelemetry/sdk-trace-base";

class SpanEnrichingProcessor implements SpanProcessor{
    forceFlush(): Promise<void>{
        return Promise.resolve();
    }
    shutdown(): Promise<void>{
        return Promise.resolve();
    }
    onStart(_span: Span): void{}
    onEnd(span: ReadableSpan){
        span.attributes["CustomDimension1"] = "value1";
        span.attributes["CustomDimension2"] = "value2";
    }
}

const provider = new NodeTracerProvider();
const azureExporter = new AzureMonitorTraceExporter();
provider.addSpanProcessor(new SpanEnrichingProcessor());
provider.addSpanProcessor(new SimpleSpanProcessor(azureExporter));

```

##### <a name="python"></a>[Python](#tab/python)

カスタム プロセッサの使用:

> [!TIP]
> 下に示すプロセッサは、Azure Monitor エクスポーターの前に追加します。

```python
...
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

trace.set_tracer_provider(TracerProvider())
span_processor = BatchSpanProcessor(exporter)
span_enrich_processor = SpanEnrichingProcessor()
trace.get_tracer_provider().add_span_processor(span_enrich_processor)
trace.get_tracer_provider().add_span_processor(span_processor)
...
```

次のコードを含む `SpanEnrichingProcessor.py` をプロジェクトに追加します。

```python
from opentelemetry.sdk.trace import SpanProcessor

class SpanEnrichingProcessor(SpanProcessor):

    def on_end(self, span):
        span._name = "Updated-" + span.name
        span._attributes["CustomDimension1"] = "Value1"
        span._attributes["CustomDimension2"] = "Value2"

```
---

#### <a name="set-user-ip"></a>ユーザー IP を設定する

アクティビティまたはスパンで `http.client_ip` 属性を設定することによって、要求の _client_IP_ フィールドにデータを入力できます。 Application Insights では、この IP アドレスを使用してユーザーの位置属性を生成した後、[既定でそれを破棄します](ip-collection.md#default-behavior)。

##### <a name="net"></a>[.NET](#tab/net)

[カスタム プロパティの追加の例](#add-custom-property)を使用しますが、`ActivityEnrichingProcessor.cs` の次のコード行を入れ替えます。

```C#
// only applicable in case of activity.Kind == Server
activity.SetTag("http.client_ip", "<IP Address>");
```

##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

[カスタム プロパティの追加の例](#add-custom-property)を使用しますが、次のコード行を入れ替えます。

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.HTTP_CLIENT_IP] = "<IP Address>";
    }
}
```

##### <a name="python"></a>[Python](#tab/python)

[カスタム プロパティの追加の例](#add-custom-property)を使用しますが、`SpanEnrichingProcessor.py` の次のコード行を入れ替えます。

```python
span._attributes["http.client_ip"] = "<IP Address>"
```

---
<!--

#### Set user ID or authenticated user ID

You can populate the _user_Id_ or _user_Authenticatedid_ field for requests by setting `xyz` or `xyz` attribute on activity/span. User ID is an anonymous user identifier and Authenticated User ID is a known user identifier.

> [!IMPORTANT]
> Consult applicable privacy laws before setting Authenticated User ID.

##### [.NET](#tab/net)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```C#
Placeholder
```

##### [Node.js](#tab/nodejs)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.ENDUSER_ID] = "<User ID>";
    }
}
```

##### [Python](#tab/python)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```python
span._attributes["enduser.id"] = "<User ID>"
```

---
-->

### <a name="filter-telemetry"></a>テレメトリのフィルター処理

次の方法を使用して、アプリケーションを終了する前にテレメトリをフィルターで除外できます。

#### <a name="net"></a>[.NET](#tab/net)

1. 多くのインストルメンテーション ライブラリには、フィルター オプションが用意されています。 ガイダンスについては、個々のインストルメンテーション ライブラリの Readme を参照してください。
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#filter)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#filter)
    - [HttpClient および HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#filter)

2. カスタム プロセッサの使用:
    
    ```csharp
    using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddProcessor(new ActivityFilteringProcessor())
            .AddAzureMonitorTraceExporter(o =>
            {
                    o.ConnectionString = "<Your Connection String>"
            })
            .Build();
    ```
    
    次のコードを含む `ActivityFilteringProcessor.cs` をプロジェクトに追加します。
    
    ```csharp
    using System.Diagnostics;
    using OpenTelemetry;
    using OpenTelemetry.Trace;
    
    public class ActivityFilteringProcessor : BaseProcessor<Activity>
    {
        public override void OnStart(Activity activity)
        {
            // prevents all exporters from exporting internal activities
            if (activity.Kind == ActivityKind.Internal)
            {
                activity.IsAllDataRequested = false;
            }
        }
    }
    ```


3. 特定のソースが `AddSource("ActivitySourceName")` を使用して明示的に追加されていない場合は、そのソースを使用して作成されたどのアクティビティもエクスポートされません。
    
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```C#
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. 多くの HTTP インストルメンテーション ライブラリによって提供される URL オプションを除外します。

    [HTTP/HTTPS インストルメンテーション ライブラリ](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http)を使用した追跡から特定の URL を除外する方法の例を次に示します。
    
    ```typescript
    ...
    import { HttpInstrumentation, HttpInstrumentationConfig } from "@opentelemetry/instrumentation-http";
    
    ...
    const httpInstrumentationConfig: HttpInstrumentationConfig = {
        ignoreIncomingPaths: [new RegExp(/dc.services.visualstudio.com/i)]
    };
    const httpInstrumentation = new HttpInstrumentation(httpInstrumentationConfig);
    provider.register();
    registerInstrumentations({
        instrumentations: [
            httpInstrumentation,
        ]
    });
    
    ```

2. カスタム プロセッサの使用。 カスタム スパン プロセッサを使用して、特定のスパンをエクスポートから除外できます。 スパンをエクスポートされないようにマークするには、その `TraceFlag` を `DEFAULT` に設定します。
[カスタム プロパティの追加の例](#add-custom-property)を使用しますが、次のコード行を入れ替えます。

    ```typescript
    ...
    import { SpanKind, TraceFlags } from "@opentelemetry/api";
    
    class SpanEnrichingProcessor implements SpanProcessor{
        ...
    
        onEnd(span: ReadableSpan) {
            if(span.kind == SpanKind.INTERNAL){
                span.spanContext().traceFlags = TraceFlags.NONE;
            }
        }
    }
    ```

#### <a name="python"></a>[Python](#tab/python)

1. 多くの HTTP インストルメンテーション ライブラリによって提供される URL オプションを除外します。

    [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask) インストルメンテーションを使用した追跡から特定の URL を除外する方法の例を次に示します。
    
    ```python
    ...
    import flask
    
    from opentelemetry.instrumentation.flask import FlaskInstrumentor
    
    # You may also populate OTEL_PYTHON_FLASK_EXCLUDED_URLS env variable
    # List will consist of comma delimited regexes representing which URLs to exclude
    excluded_urls = "client/.*/info,healthcheck"
    
    FlaskInstrumentor().instrument(excluded_urls=excluded_urls) # Do this before flask.Flask
    app = flask.Flask(__name__)
    ...
    ```

2. カスタム プロセッサの使用。 カスタム スパン プロセッサを使用して、特定のスパンをエクスポートから除外できます。 スパンをエクスポートされないようにマークするには、その `TraceFlag` を `DEFAULT` に設定します。
    
    ```python
    ...
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    
    trace.set_tracer_provider(TracerProvider())
    span_processor = BatchSpanProcessor(exporter)
    span_filter_processor = SpanFilteringProcessor()
    trace.get_tracer_provider().add_span_processor(span_filter_processor)
    trace.get_tracer_provider().add_span_processor(span_processor)
    ...
    ```
    
    次のコードを含む `SpanFilteringProcessor.py` をプロジェクトに追加します。
    
    ```python
    from opentelemetry.trace import SpanContext, SpanKind, TraceFlags
    from opentelemetry.sdk.trace import SpanProcessor
    
    class SpanFilteringProcessor(SpanProcessor):
    
        # prevents exporting spans from internal activities
        def on_start(self, span):
            if span._kind is SpanKind.INTERNAL:
                span._context = SpanContext(
                    span.context.trace_id,
                    span.context.span_id,
                    span.context.is_remote,
                    TraceFlags.DEFAULT,
                    span.context.trace_state,
                )
    
    ```
    
    <!-- For more information, see [GitHub Repo](link). -->
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```python
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

---

## <a name="enable-otlp-exporter"></a>OTLP エクスポーターを有効にする

テレメトリを 2 つの場所に送信するために、Azure Monitor エクスポーターと共に OpenTelemetry プロトコル (OTLP) エクスポーターを有効にすることもできます。

> [!NOTE]
> OTLP エクスポーターは、便宜上示されているだけです。 Microsoft では、OTLP エクスポーターや、そのすべてのコンポーネントまたはサードパーティ エクスペリエンス ダウンストリームを正式にサポートしていません。 Azure のサポート範囲外の OpenTelemetry の問題については、[OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector) に関する問題を開くことをお勧めします。

#### <a name="net"></a>[.NET](#tab/net)

1. プロジェクトで [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) と共に [OpenTelemetry.Exporter.OpenTelemetryProtocol](https://www.nuget.org/packages/OpenTelemetry.Exporter.OpenTelemetryProtocol/) パッケージをインストールします。

2. 次のコード スニペットを追加します。 この例では、OpenTelemetry コレクターが OTLP レシーバーと共に実行されることを前提にしています。 詳細については、[ここ](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/examples/Console/TestOtlpExporter.cs)にある例を参照してください。

```csharp
// sends data to Application Insights as well as OTLP
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>"
        })
        .AddOtlpExporter()
        .Build();
```


#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. プロジェクトで [Azure Monitor OpenTelemetry エクスポーター](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)と共に [OpenTelemetry コレクター エクスポーター](https://www.npmjs.com/package/@opentelemetry/exporter-otlp-http) パッケージをインストールします。


```sh
    npm install @opentelemetry/exporter-otlp-http
    npm install @azure/monitor-opentelemetry-exporter
```

2. 次のコード スニペットを追加します。 この例では、OpenTelemetry コレクターが OTLP レシーバーと共に実行されることを前提にしています。 詳細については、[ここ](https://github.com/open-telemetry/opentelemetry-js/tree/main/examples/otlp-exporter-node)にある例を参照してください。

```typescript
const { BasicTracerProvider, SimpleSpanProcessor } = require('@opentelemetry/sdk-trace-base');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-otlp-http');

const provider = new BasicTracerProvider();
const azureMonitorExporter = new AzureMonitorTraceExporter({
  instrumentationKey: os.environ["APPLICATIONINSIGHTS_CONNECTION_STRING"]
});
const otlpExporter = new OTLPTraceExporter();
provider.addSpanProcessor(new SimpleSpanProcessor(azureMonitorExporter));
provider.addSpanProcessor(new SimpleSpanProcessor(otlpExporter));
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

1. [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) および [opentelemetry-exporter-otlp](https://pypi.org/project/opentelemetry-exporter-otlp/) パッケージをインストールします。

2. 次のコード スニペットを追加します。 この例では、OpenTelemetry コレクターが OTLP レシーバーと共に実行されることを前提にしています。 詳細については、この [README](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/monitor/azure-monitor-opentelemetry-exporter/samples/traces#collector) を参照してください。

```python
from opentelemetry import trace 

from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__) 

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)
otlp_exporter = OTLPSpanExporter(endpoint="http://localhost:4317")
span_processor = BatchSpanProcessor(otlp_exporter) 
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("test"):
    print("Hello world!")
```

---

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="enable-diagnostic-logging"></a>診断ログの有効化

#### <a name="net"></a>[.NET](#tab/net)

Azure Monitor エクスポーターでは、独自の内部ログのために EventSource を使用します。 "OpenTelemetry-AzureMonitor-Exporter" という名前のソースにオプトインすることによって、エクスポーター ログをすべての EventListener に使用できます。 詳細なトラブルシューティング手順については、[ OpenTelemetry のトラブルシューティング](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/src/OpenTelemetry#troubleshooting)に関するページを参照してください。

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Azure Monitor エクスポーターでは、内部ログのために OpenTelemetry API ロガーを使用します。これは、次のコードを使用して有効にすることができます。 

```typescript
const { diag, DiagConsoleLogger, DiagLogLevel } = require("@opentelemetry/api");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
diag.setLogger(new DiagConsoleLogger(), DiagLogLevel.ALL);
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

Azure Monitor エクスポーターでは、独自の内部ログのために Python の標準のログ [ライブラリ](https://docs.python.org/3/library/logging.html)を使用します。 OpenTelemetry API と Azure Monitor エクスポーターのログは通常、不規則なアクティビティの場合は重大度レベル WARNING または ERROR で、通常のアクティビティや成功したアクティビティの場合は INFO で記録されます。 既定の Python ログ ライブラリでは重大度レベルが既定で WARNING になるため、この重大度でログを表示するには、重大度レベルを変更する必要があります。 すべての重大度のログをコンソールとファイルに出力する方法の例を次に示します。

```python
...
import logging

logging.basicConfig(format="%(asctime)s:%(levelname)s:%(message)s", level=logging.DEBUG)

logger = logging.getLogger(__name__)
file = logging.FileHandler("example.log")
stream = logging.StreamHandler()
logger.addHandler(file)
logger.addHandler(stream)
...

```

---

### <a name="known-issues"></a>既知の問題

Azure Monitor OpenTelemetry エクスポーターに関する既知の問題には、次のものがあります。 

- 依存関係テレメトリに操作名がない。これは、[エラーとパフォーマンス] タブのエクスペリエンスに悪影響を与えます。
- 要求テレメトリと依存関係テレメトリにデバイス モデルがない。これは、デバイスのコーホート分析に悪影響を与えます。
- データベース サーバー名が依存関係名から漏れたままになる。これにより、異なるサーバー上の同じ名前を持つテーブルが誤って集計されます。

## <a name="support"></a>サポート

- この記事のトラブルシューティング手順を確認してください。
- Azure サポートの問題については、[Azure サポート チケット](https://azure.microsoft.com/support/create-ticket/)を開いてください。

### <a name="net"></a>[.NET](#tab/net)

OpenTelemetry の問題については、[OpenTelemetry .NET コミュニティ](https://github.com/open-telemetry/opentelemetry-dotnet)に直接問い合わせてください。

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

OpenTelemetry の問題については、[OpenTelemetry JavaScript コミュニティ](https://github.com/open-telemetry/opentelemetry-js)に直接問い合わせてください。

### <a name="python"></a>[Python](#tab/python)

OpenTelemetry の問題については、[OpenTelemetry Python コミュニティ](https://github.com/open-telemetry/opentelemetry-python)に直接問い合わせてください。

---

## <a name="opentelemetry-feedback"></a>OpenTelemetry のフィードバック

- OpenTelemetry コミュニティの[カスタマー フィードバック アンケート](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform)に記入してください。
- Microsoft の [OpenTelemetry 早期導入者コミュニティ](https://aka.ms/AzMonOTel/)に参加することにより、お客様自身のことを少しお知らせください。
- [Microsoft の 技術コミュニティ](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)で他の Azure Monitor ユーザーと交流してください。

## <a name="next-steps"></a>次のステップ

### <a name="net"></a>[.NET](#tab/net)

- [Azure Monitor エクスポーター GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter)でソース コードを確認します。
- NuGet パッケージをインストールしたり、更新プログラムを確認したり、リリース ノートを表示したりするには、[Azure Monitor エクスポーター NuGet パッケージ](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter/)のページを参照してください。
- [Azure Monitor のアプリケーション例](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter/tests/Azure.Monitor.OpenTelemetry.Exporter.Tracing.Customization)を使用して、Azure Monitor Application Insights と OpenTelemetry により精通します。
- OpenTelemetry とそのコミュニティの詳細については、[OpenTelemetry .NET GitHub リポジトリ](https://github.com/open-telemetry/opentelemetry-dotnet)を参照してください。
- 使用法エクスペリエンスを有効にするために、[Web またはブラウザーのユーザー監視を有効にします](javascript.md)。


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- [Azure Monitor エクスポーター GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter)でソース コードを確認します。
- NPM パッケージをインストールしたり、更新プログラムを確認したり、リリース ノートを表示したりするには、[Azure Monitor エクスポーター NPM パッケージ](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)のページを参照してください。
- [Azure Monitor のアプリケーション例](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter/samples)を使用して、Azure Monitor Application Insights と OpenTelemetry により精通します。
- OpenTelemetry とそのコミュニティの詳細については、[OpenTelemetry JavaScript GitHub リポジトリ](https://github.com/open-telemetry/opentelemetry-js)を参照してください。
- 使用法エクスペリエンスを有効にするために、[Web またはブラウザーのユーザー監視を有効にします](javascript.md)。

### <a name="python"></a>[Python](#tab/python)

- [Azure Monitor エクスポーター GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/monitor/azure-monitor-opentelemetry-exporter/README.md)でソース コードを確認します。
- PyPI パッケージをインストールしたり、更新プログラムを確認したり、リリース ノートを表示したりするには、[Azure Monitor エクスポーター PyPI パッケージ](https://pypi.org/project/azure-monitor-opentelemetry-exporter/)のページを参照してください。
-  [Azure Monitor のアプリケーション例](https://github.com/Azure-Samples/azure-monitor-opentelemetry-python)を使用して、Azure Monitor Application Insights と OpenTelemetry により精通します。
- OpenTelemetry とそのコミュニティの詳細については、[OpenTelemetry Python GitHub リポジトリ](https://github.com/open-telemetry/opentelemetry-python)を参照してください。
- 使用法エクスペリエンスを有効にするために、[Web またはブラウザーのユーザー監視を有効にします](javascript.md)。

---
