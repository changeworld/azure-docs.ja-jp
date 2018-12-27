---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 92eb13165326f44432f09322ea97f3cee5ccec2b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251245"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) の構成設定。

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

タスク ハブの名前は、先頭文字をアルファベットとする必要があります。また、使用できるのはアルファベットと数値だけです。 指定しない場合、関数アプリの既定のタスク ハブ名は **DurableFunctionsHub** です。 詳細については、[タスク ハブ](../articles/azure-functions/durable-functions-task-hubs.md)に関するページをご覧ください。

|プロパティ  |既定値 | 説明 |
|---------|---------|---------|
|HubName|DurableFunctionsHub|代替[タスク ハブ](../articles/azure-functions/durable-functions-task-hubs.md)名を使用すると、複数の Durable Functions アプリケーションが同じストレージ バックエンドを使用している場合でも、これらのアプリケーションを互いに分離できます。|
|ControlQueueBatchSize|32|コントロール キューから一度にプルするメッセージの数。|
|PartitionCount |4|コントロール キューのパーティション数。 1 から 16 までの正の整数を使用できます。|
|ControlQueueVisibilityTimeout |5 分|デキューされたコントロール キュー メッセージの表示タイムアウト。|
|WorkItemQueueVisibilityTimeout |5 分|デキューされた作業項目キュー メッセージの表示タイムアウト。|
|MaxConcurrentActivityFunctions |現在のマシン上のプロセッサ数の 10 倍|1 つのホスト インスタンスで同時に処理できるアクティビティ関数の最大数。|
|MaxConcurrentOrchestratorFunctions |現在のマシン上のプロセッサ数の 10 倍|1 つのホスト インスタンスで同時に処理できるオーケストレーター関数の最大数。|
|AzureStorageConnectionStringName |AzureWebJobsStorage|基になる Azure Storage リソースの管理に使用される Azure Storage 接続文字列を含むアプリ設定の名前。|
|TraceInputsAndOutputs |false|関数呼び出しの入力と出力をトレースするかどうかを示す値。 関数の実行イベントをトレースした場合の既定の動作では、関数呼び出しのシリアル化された入力および出力のバイト数が記録されます。 これにより、ログが肥大化することも、機密情報が誤ってログに公開されることもなく、入力および出力に関する最小限の情報が示されます。 このプロパティを true に設定すると、既定の関数ログ記録によって、関数の入力および出力の内容全体がログに記録されます。|
|LogReplayEvents|false|オーケストレーションの再生イベントを Application Insights に書き込むかどうかを示す値。|
|EventGridTopicEndpoint ||Azure Event Grid カスタム トピック エンドポイントの URL。 このプロパティが設定されている場合は、オーケストレーションのライフ サイクル通知イベントがこのエンドポイントに発行されます。 このプロパティは、アプリ設定の解決をサポートします。|
|EventGridKeySettingName ||`EventGridTopicEndpoint` での Azure Event Grid カスタム トピックによる認証に使用されるキーを含むアプリ設定の名前。|
|EventGridPublishRetryCount|0|Event Grid トピックへの発行が失敗した場合に再試行する回数。|
|EventGridPublishRetryInterval|5 分|Event Grid の発行を再試行する間隔 (*hh:mm:ss* 形式)。|

これらの多くはパフォーマンスの最適化を目的としています。 詳細については、[パフォーマンスとスケール](../articles/azure-functions/durable-functions-perf-and-scale.md)に関するページをご覧ください。