---
title: Azure Functions の host.json のリファレンス
description: Azure Functions の host.json ファイルのリファレンス ドキュメント
services: functions
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: glenga
ms.openlocfilehash: 9043add91022c2829c305425dba9c8f11b224fcf
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345516"
---
# <a name="hostjson-reference-for-azure-functions"></a>Azure Functions の host.json のリファレンス

*host.json* メタデータ ファイルには、関数アプリのすべての関数に影響するグローバル構成オプションが含まれています。 この記事では、使用できる設定の一覧を紹介します。 JSON スキーマは、 http://json.schemastore.org/host にあります。

[アプリ設定](functions-app-settings.md)と [local.settings.json](functions-run-local.md#local-settings-file) ファイルには、他のグローバル構成オプションがあります。

## <a name="sample-hostjson-file"></a>サンプル host.json ファイル

次のサンプル *host.json* ファイルには、すべての使用できるオプションが指定されています。

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
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
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

この記事の次のセクションでは、最上位レベルの各プロパティについて説明します。 特記がない場合は、いずれも省略可能です。

## <a name="aggregator"></a>aggregator

[Application Insights のメトリックを計算する](functions-monitoring.md#configure-the-aggregator)ときに集計される関数呼び出しの数を指定します。 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|プロパティ |既定値  | 説明 |
|---------|---------|---------| 
|batchSize|1,000|集計する要求の最大数。| 
|flushTimeout|00:00:30|集計する最長期間。| 

2 つの制限のいずれかに最初に達した場合、関数呼び出しが集計されます。

## <a name="applicationinsights"></a>applicationInsights

[Application Insights のサンプリング機能](functions-monitoring.md#configure-sampling)を制御します。

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|isEnabled|true|サンプリングを有効または無効にします。| 
|maxTelemetryItemsPerSecond|5|サンプリングが開始されるしきい値。| 

## <a name="durabletask"></a>durableTask

[Durable Functions](durable-functions-overview.md) の構成設定。

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

タスク ハブの名前は、先頭文字をアルファベットとする必要があります。また、使用できるのはアルファベットと数値だけです。 指定しない場合、関数アプリの既定のタスク ハブ名は **DurableFunctionsHub** です。 詳細については、[タスク ハブ](durable-functions-task-hubs.md)に関するページをご覧ください。

|プロパティ  |既定値 | 説明 |
|---------|---------|---------|
|HubName|DurableFunctionsHub|代替[タスク ハブ](durable-functions-task-hubs.md)名を使用すると、複数の Durable Functions アプリケーションが同じストレージ バックエンドを使用している場合でも、これらのアプリケーションを互いに分離できます。|
|ControlQueueBatchSize|32|コントロール キューから一度にプルするメッセージの数。|
|PartitionCount |4|コントロール キューのパーティション数。 1 から 16 までの正の整数を使用できます。|
|ControlQueueVisibilityTimeout |5 分|デキューされたコントロール キュー メッセージの表示タイムアウト。|
|WorkItemQueueVisibilityTimeout |5 分|デキューされた作業項目キュー メッセージの表示タイムアウト。|
|MaxConcurrentActivityFunctions |現在のマシン上のプロセッサ数の 10 倍|1 つのホスト インスタンスで同時に処理できるアクティビティ関数の最大数。|
|MaxConcurrentOrchestratorFunctions |現在のマシン上のプロセッサ数の 10 倍|1 つのホスト インスタンスで同時に処理できるアクティビティ関数の最大数。|
|AzureStorageConnectionStringName |AzureWebJobsStorage|基になる Azure Storage リソースの管理に使用される Azure Storage 接続文字列を含むアプリ設定の名前。|
|TraceInputsAndOutputs |false|関数呼び出しの入力と出力をトレースするかどうかを示す値。 関数の実行イベントをトレースした場合の既定の動作では、関数呼び出しのシリアル化された入力および出力のバイト数が記録されます。 これにより、ログが肥大化することも、機密情報が誤ってログに公開されることもなく、入力および出力に関する最小限の情報が示されます。 このプロパティを true に設定すると、既定の関数ログ記録によって、関数の入力および出力の内容全体がログに記録されます。|
|LogReplayEvents|false|オーケストレーションの再生イベントを Application Insights に書き込むかどうかを示す値。|
|EventGridTopicEndpoint ||Azure Event Grid カスタム トピック エンドポイントの URL。 このプロパティが設定されている場合は、オーケストレーションのライフ サイクル通知イベントがこのエンドポイントに発行されます。 このプロパティは、アプリ設定の解決をサポートします。|
|EventGridKeySettingName ||`EventGridTopicEndpoint` での Azure Event Grid カスタム トピックによる認証に使用されるキーを含むアプリ設定の名前。|
|EventGridPublishRetryCount|0|Event Grid トピックへの発行が失敗した場合に再試行する回数。|
|EventGridPublishRetryInterval|5 分|Event Grid の発行を再試行する間隔 (*hh:mm:ss* 形式)。|

これらの多くはパフォーマンスの最適化を目的としています。 詳細については、[パフォーマンスとスケール](durable-functions-perf-and-scale.md)に関するページをご覧ください。

## <a name="eventhub"></a>eventHub

[Event Hub トリガーとバインディング](functions-bindings-event-hubs.md)の構成設定。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

ジョブのホストが実行される関数の一覧。 空の配列は、すべての関数を実行することを示します。 [ローカルで実行する](functions-run-local.md)場合にのみ使用します。 関数アプリでは、*function.json* の `disabled` プロパティを使用します。*host.json* のこのプロパティは使用しません。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

すべての関数のタイムアウト期間を示します。 従量課金プランの有効な範囲は 1 秒から 10 分であり、既定値は 5 分です。 App Service プランに制限はありません。既定値は null です (タイムアウトなしを示します)。

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

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|enabled|true|機能が有効になっているかどうか。 | 
|healthCheckInterval|10 秒|定期的なバック グラウンドでの正常性チェックの間隔。 | 
|healthCheckWindow|2 分|`healthCheckThreshold` 設定と組み合わせて使用するスライド時間枠。| 
|healthCheckThreshold|6|正常性チェックの最大失敗回数。この回数を超えると、ホスト リサイクルが開始されます。| 
|counterThreshold|0.80|パフォーマンス カウンターが異常とみなされるしきい値。| 

## <a name="http"></a>http

[http トリガーとバインディング](functions-bindings-http-webhook.md)の構成設定。

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

ジョブ ホストの一意の ID。 ダッシュを削除した小文字の GUID を指定できます。 ローカルで実行しているときに必要です。 Azure Functions で実行する場合、`id` を省略すると、ID は自動的に生成されます。

ストレージ アカウントを複数の関数アプリで共有している場合は、各関数アプリの `id` がそれぞれ異なることを確認してください。 `id` プロパティは省略することができます。あるいは、各関数アプリの `id` を手動でそれぞれ異なる値に設定することもできます。 1 つの関数アプリが複数のインスタンスにスケール アウトする場合、タイマー インスタンスが 1 しか存在しないようにするために、タイマー トリガーではストレージ ロックが使用されます。 2 つの関数アプリが同じ `id` を共有していて、それぞれタイマー トリガーを使用している場合は、1 つのタイマーのみが実行されます。


```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

[ILogger object](functions-monitoring.md#write-logs-in-c-functions) から出力されたログまたは [context.log](functions-monitoring.md#write-logs-in-javascript-functions) ログのフィルターを制御します。

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|categoryFilter|該当なし|カテゴリ別のフィルターを指定します| 
|defaultLevel|情報|`categoryLevels` 配列に指定されていないカテゴリの場合、このレベル以上のログを Application Insights に送信します。| 
|categoryLevels|該当なし|各カテゴリの Application Insights に送信される最小ログ レベルを指定するカテゴリの配列。 ここで指定されるカテゴリは、同じ値で始まるすべてのカテゴリを制御し、長い値の方が優先されます。 前述のサンプル *host.json* ファイルでは、`Information` レベルの "Host.Aggregator" で始まるすべてのカテゴリ。 `Error` レベルのログである、"Host.Executor" など "Host" で始まるその他すべてのカテゴリ。| 

## <a name="queues"></a>queues

[Storage キュー トリガーとバインディング](functions-bindings-storage-queue.md)の構成設定。

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

[Service Bus トリガーとバインディング](functions-bindings-service-bus.md)の構成設定。

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

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

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|関数レベルのロックの取得期間。 ロックの自動更新。| 
|listenerLockPeriod|00:01:00|リスナーのロックの取得期間。| 
|listenerLockRecoveryPollingInterval|00:01:00|スタートアップ時にリスナーのロックを獲得できなかった場合に、リスナーのロックの回復に使用される時間間隔。| 
|lockAcquisitionTimeout|00:01:00|ランタイムがロックの獲得を試行する最長時間。| 
|lockAcquisitionPollingInterval|該当なし|ロックの獲得の試行間隔。| 

## <a name="tracing"></a>tracing

`TraceWriter` オブジェクトを使用して作成するログの構成設定。 [C# のログの記録](functions-reference-csharp.md#logging)と [Node.js のログの記録](functions-reference-node.md#writing-trace-output-to-the-console)に関するページを参照してください。 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|consoleLevel|info|コンソール ログのトレース レベル。 オプションは、`off`、`error`、`warning`、`info`、および `verbose` です。|
|fileLoggingMode|debugOnly|ファイルのログ記録のトレース レベル。 オプションは、`never`、`always`、`debugOnly` です。| 

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
