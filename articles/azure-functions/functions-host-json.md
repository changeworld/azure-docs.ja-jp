---
title: Azure Functions 2.x の host.json のリファレンス
description: Azure Functions の v2 ランタイムの host.json ファイルのリファレンス ドキュメント。
ms.topic: conceptual
ms.date: 09/08/2018
ms.openlocfilehash: 08d772fc9b2871262b449a017f8be59a344576b2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975450"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 以降の host.json のリファレンス 

> [!div class="op_single_selector" title1="使用している Azure Functions ランタイムのバージョンを選択します。 "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

*host.json* メタデータ ファイルには、関数アプリのすべての関数に影響するグローバル構成オプションが含まれています。 この記事では、Azure Functions ランタイムのバージョン 2.x 移行で使用できる設定を一覧表示しています。  

> [!NOTE]
> この記事は、Azure Functions 2.x 以降のバージョンを対象としています。  Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

関数アプリの他の構成オプションは、[アプリの設定](functions-app-settings.md)で管理されます。

host.json の一部の設定は、[local.settings.json](functions-run-local.md#local-settings-file) ファイルでローカルに実行するときにのみ使用されます。

## <a name="sample-hostjson-file"></a>サンプル host.json ファイル

次のサンプル *host.json* ファイルには、すべての使用できるオプションが指定されています。

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

この記事の次のセクションでは、最上位レベルの各プロパティについて説明します。 特記がない場合は、いずれも省略可能です。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

この設定は [logging](#logging) の子です。

[Application Insights のサンプリング機能](./functions-monitoring.md#configure-sampling)を制御します。

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> ログ サンプリングが原因で、一部の実行が Application Insights の [モニター] ブレードに表示されない場合があります。

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|isEnabled|true|サンプリングを有効または無効にします。| 
|maxTelemetryItemsPerSecond|20|サンプリングが開始されるしきい値。| 
|EnableLiveMetrics |true|ライブ メトリックの収集を有効にします。|
|EnableDependencyTracking|true|依存関係の追跡を有効にします。|
|EnablePerformanceCountersCollection|true|Kudu パフォーマンス カウンターの収集を有効にします。|

## <a name="cosmosdb"></a>cosmosDb

構成設定は、[Cosmos DB のトリガーとバインディング](functions-bindings-cosmosdb-v2.md#host-json)に関する記事に記載されています。

## <a name="durabletask"></a>durableTask

構成設定は、[Durable Functions のバインディング](durable/durable-functions-bindings.md#host-json)に関する記事に記載されています。

## <a name="eventhub"></a>eventHub

構成設定は、[Event Hub のトリガーとバインディング](functions-bindings-event-hubs.md#host-json)に関する記事に記載されています。 

## <a name="extensions"></a>拡張機能

バインド固有の設定 ([http](#http) や [eventHub](#eventhub) など) をすべて含むオブジェクトを返すプロパティ。

## <a name="extensionbundle"></a>extensionBundle 

拡張機能バンドルを使用すると、互換性のある一連の関数バインド拡張機能を関数アプリに追加できます。 詳細については、「[ローカル開発用の拡張機能バンドル](functions-bindings-register.md#extension-bundles)」を参照してください。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

ジョブのホストが実行される関数の一覧。 空の配列は、すべての関数を実行することを示します。 [ローカルで実行する](functions-run-local.md)場合にのみ使用します。 Azure の関数アプリでは、この設定を使用する代わりに、「[Azure Functions で関数を無効にする方法](disable-function.md)」の手順に従って、特定の関数を無効にする必要があります。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

すべての関数のタイムアウト期間を示します。 これは、期間文字列形式に従います。 サーバーレス従量課金プランの有効な範囲は 1 秒から 10 分であり、既定値は 5 分です。  

Premium プランの有効な範囲は 1 秒から 60 分であり、既定値は 30 分です。

専用 (App Service) プランでは、全体的な制限はなく、既定値は 30 分です。 `-1` の値は無制限の実行を示しますが、固定の上限を維持することをお勧めします。

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[ホストの正常性監視](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)を行うための構成設定です。

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|enabled|true|機能が有効かどうかを指定します。 | 
|healthCheckInterval|10 秒|定期的なバック グラウンドでの正常性チェックの間隔。 | 
|healthCheckWindow|2 分|`healthCheckThreshold` 設定と組み合わせて使用するスライド時間枠。| 
|healthCheckThreshold|6|正常性チェックの最大失敗回数。この回数を超えると、ホスト リサイクルが開始されます。| 
|counterThreshold|0.80|パフォーマンス カウンターが異常とみなされるしきい値。| 

## <a name="http"></a>http

構成設定は、[HTTP トリガーとバインディング](functions-bindings-http-webhook.md#hostjson-settings)に関する記事に記載されています。

## <a name="logging"></a>logging

Application Insights など、関数アプリのログの動作を制御します。

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|fileLoggingMode|debugOnly|どのレベルでファイルのログ記録を有効にするかを定義します。  オプションは、`never`、`always`、`debugOnly` です。 |
|logLevel|該当なし|アプリ内の関数に対するログ カテゴリのフィルター処理を定義するオブジェクト。 バージョン 2.x 以降のログ カテゴリのフィルター処理は、ASP.NET Core のレイアウトに従います。 これにより、特定の関数についてログをフィルター処理できます。 詳しくは、ASP.NET Core のドキュメントの「[ログのフィルター処理](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)」をご覧ください。 |
|console|該当なし| [console](#console) ログ記録の設定。 |
|applicationInsights|該当なし| [applicationInsights](#applicationinsights) の設定。 |

## <a name="console"></a>console

この設定は [logging](#logging) の子です。 デバッグ モードでないときのコンソール ログ記録を制御します。

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|isEnabled|false|コンソール ログ記録を有効または無効にします。| 

## <a name="manageddependency"></a>managedDependency

マネージド依存関係は、現在 PowerShell ベースの関数でのみサポートされている機能です。 この機能を使用すると、サービスによって依存関係を自動的に管理できます。 `enabled` プロパティが `true` に設定されている場合は、`requirements.psd1` ファイルが処理されます。 いずれかのマイナー バージョンがリリースされると、依存関係が更新されます。 詳細については、PowerShell の記事の[マネージド依存関係](functions-reference-powershell.md#dependency-management)に関する記事をご覧ください。

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

構成設定は、[Storage キュー トリガーとバインディング](functions-bindings-storage-queue.md#host-json)に関する記事に記載されています。  

## <a name="sendgrid"></a>sendGrid

構成設定は、[SendGrid のトリガーとバインディング](functions-bindings-sendgrid.md#host-json)に関する記事に記載されています。

## <a name="servicebus"></a>serviceBus

構成設定は、[Service Bus のトリガーとバインディング](functions-bindings-service-bus.md#host-json)に関する記事に記載されています。

## <a name="singleton"></a>singleton

シングルトン ロック動作の構成設定。 詳細については、「[GitHub issue about singleton support](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)」(シングルトンのサポートに関する GitHub の問題) を参照してください。

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|関数レベルのロックの取得期間。 ロックの自動更新。| 
|listenerLockPeriod|00:01:00|リスナーのロックの取得期間。| 
|listenerLockRecoveryPollingInterval|00:01:00|スタートアップ時にリスナーのロックを獲得できなかった場合に、リスナーのロックの回復に使用される時間間隔。| 
|lockAcquisitionTimeout|00:01:00|ランタイムがロックの獲得を試行する最長時間。| 
|lockAcquisitionPollingInterval|該当なし|ロックの獲得の試行間隔。| 

## <a name="version"></a>version

v2 ランタイムを対象とする関数アプリでは、バージョン文字列 `"version": "2.0"` が必要です。

## <a name="watchdirectories"></a>watchDirectories

変更を監視する[共有コード ディレクトリ](functions-reference-csharp.md#watched-directories)のセット。  これらのディレクトリ内のコードを変更した場合に、関数によって変更を選択するようにします。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [host.json ファイルを更新する方法について説明します](functions-reference.md#fileupdate)。

> [!div class="nextstepaction"]
> [環境変数のグローバル設定を参照してください。](functions-app-settings.md)
