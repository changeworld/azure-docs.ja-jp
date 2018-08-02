---
title: Azure Event Grid への Durable Functions の発行 (プレビュー)
description: Durable Functions の Azure Event Grid 自動発行を構成する方法を説明します。
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
ms.date: 04/20/2018
ms.author: glenga
ms.openlocfilehash: 691f00b0f05d8c91ae6f4409ba9eeeb50b0cd53b
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343697"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Azure Event Grid への Durable Functions の発行 (プレビュー)

この記事では、オーケストレーション ライフサイクル イベント (作成、完了、失敗など) をカスタムの [Azure Event Grid トピック](https://docs.microsoft.com/azure/event-grid/overview)に発行するように Azure Durable Functions を設定する方法を示します。 

この機能が役立つシナリオを次にいくつか示します。

* **ブルー/グリーン デプロイなどの DevOps シナリオ**: [並行デプロイ戦略](https://docs.microsoft.com/azure/azure-functions/durable-functions-versioning#side-by-side-deployments)を実装する前に、タスクが実行されているかどうかを知ることが必要な場合があります。

* **高度な監視と診断のサポート**: SQL データベースや CosmosDB など、クエリ用に最適化された外部ストアで、オーケストレーションの状態情報を追跡できます。

* **実行時間の長いバック グラウンド アクティビティ**: 実行時間の長いバック グラウンド アクティビティに Durable Functions を使用する場合、この機能は現在の状態を知るのに役立ちます。

## <a name="prerequisites"></a>前提条件

* [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc 以降を Durable Functions プロジェクトにインストールします。
* [Azure ストレージ エミュレーター](https://docs.microsoft.com/azure/storage/common/storage-use-emulator)をインストールします。
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) をインストールするか、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を使用します

## <a name="create-a-custom-event-grid-topic"></a>カスタムの Event Grid トピックの作成

Durable Functions からイベントを送信するための Event Grid トピックを作成します。 次の手順は、Azure CLI を使用してトピックを作成する方法を示しています。 PowerShell または Azure Portal を使用して行う方法については、次の記事をご覧ください。

* [EventGrid クイック スタート: カスタム イベントの作成 - PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid クイック スタート: カスタム イベントの作成 - Azure Portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>リソース グループの作成

`az group create` コマンドでリソース グループを作成します。 現時点では、Event Grid ではすべてのリージョンをサポートしているわけではありません。 サポートされるリージョンについては、[Event Grid の概要](https://docs.microsoft.com/azure/event-grid/overview)に関する記事をご覧ください。 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>カスタム トピックの作成

Event Grid のトピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 `<topic_name>` は、トピックの一意の名前に置き換えてください。 トピック名は、DNS エントリになるため、一意である必要があります。

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup 
```

## <a name="get-the-endpoint-and-key"></a>エンドポイントとキーの取得

トピックのエンドポイントを取得します。 `<topic_name>` を選択した名前で置き換えます。

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

トピック キーを取得します。 `<topic_name>` を選択した名前で置き換えます。

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

これで、トピックにイベントを送信できます。

## <a name="configure-azure-event-grid-publishing"></a>Azure Event Grid の発行の構成

Durable Functions プロジェクトで、`host.json` ファイルを検索します。

`EventGridTopicEndpoint` と `EventGridKeySettingName` を `durableTask` プロパティに追加します。

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

可能性のある Azure Event Grid の構成プロパティは次のとおりです。

* **EventGridTopicEndpoint** - Event Grid トピックのエンドポイント。 *%AppSettingName%* 構文を使用すると、アプリケーションの設定または環境変数からこの値を解決できます。
* **EventGridKeySettingName** - Azure 関数に対するアプリケーション設定のキー。 Durable Functions は、値から Event Grid トピックキーを取得します。
* **EventGridPublishRetryCount** - [省略可能] Event Grid トピックへの発行が失敗した場合に再試行する回数。
* **EventGridPublishRetryInterval** - [省略可能] Event Grid の発行を再試行する間隔 (*hh:mm:ss* 形式)。 指定されていない場合、既定の再試行間隔は 5 分です。

`host.json` ファイルを構成すると、Durable Functions プロジェクトは Event Grid トピックへのライフサイクル イベントの送信を開始します。 これは、Function App で実行する場合とローカルで実行する場合に動作します。

Function App と `local.setting.json` で、トピック キーのアプリ設定を設定します。 次の JSON は、ローカル デバッグ用の `local.settings.json` のサンプルです。 `<topic_key>` はトピック キーで置き換えます。  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

[ストレージ エミュレーター](https://docs.microsoft.com/azure/storage/common/storage-use-emulator)が動作していることを確認します。 実行する前に `AzureStorageEmulator.exe clear all` コマンドを実行することをお勧めします。

## <a name="create-functions-that-listen-for-events"></a>イベントをリッスンする関数の作成

Function App を作成します。 Event Grid トピックと同じリージョンに配置することをお勧めします。

### <a name="create-an-event-grid-trigger-function"></a>イベント グリッド トリガー関数の作成

ライフサイクル イベントを受け取る関数を作成します。 **[カスタム関数]** を選択します。 

![カスタム関数の作成の選択。](media/durable-functions-event-publishing/functions-portal.png)

イベント グリッド トリガーを選択し、[`C#`] を選択します。

![イベント グリッド トリガーの選択。](media/durable-functions-event-publishing/eventgrid-trigger.png)

関数の名前を入力し、[`Create`] を選択します。

![イベント グリッド トリガーの作成。](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

次のコードを含む関数が作成されます。 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

[`Add Event Grid Subscription`] を選択します。 この操作では、作成した Event Grid トピックの Event Grid サブスクリプションを追加します。 詳しくは、「[Azure Event Grid の概念](https://docs.microsoft.com/azure/event-grid/concepts)」をご覧ください

![[イベント グリッド トリガー] リンクの選択。](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

**[トピックの種類]** に [`Event Grid Topics`] を選択します。 Event Grid トピック用に作成したリソース グループを選択します。 次に、Event Grid トピックのインスタンスを選択します。 [`Create`] をクリックします。

![Event Grid のサブスクリプションを作成する。](media/durable-functions-event-publishing/eventsubscription.png)

これで、ライフサイクル イベントを受信する準備が整いました。 

## <a name="create-durable-functions-to-send-the-events"></a>イベントを送信する Durable Functions を作成します。

Durable Functions プロジェクトで、ローカル コンピューターでのデバッグを開始します。  次のコードは、Durable Functions のテンプレート コードと同じです。 ローカル コンピューターに `host.json` と `local.settings.json` を既に構成してあります。 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, TraceWriter log)
        {
            log.Info($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            TraceWriter log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.Info($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Postman またはブラウザーで `Sample_HttpStart` を呼び出す場合、Durable Function はライフサイクル イベントの送信を開始します。 通常、ローカル デバッグのエンドポイントは `http://localhost:7071/api/Sample_HttpStart` です。

Azure Portal で作成した関数からのログをご覧ください。

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",    
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>イベント スキーマ

次の一覧では、ライフサイクル イベントのスキーマについて説明します。

* **id**: Event Grid イベントの一意識別子。
* **subject**: イベントの件名へのパス。 `durable/orchestrator/{orchestrationRuntimeStatus}` `{orchestrationRuntimeStatus}` は`Running`、`Completed`、`Failed`、`Terminated` になります。  
* **data**: Durable Functions 固有のパラメーター。
    * **hubName**: [TaskHub](https://docs.microsoft.com/azure/azure-functions/durable-functions-task-hubs) 名。
    * **functionName**: オーケストレーター関数名。
    * **instanceId**: Durable Functions の instanceId。
    * **reason**: 追跡イベントに関連付けられている付加的なデータ。 詳しくは、「[Durable Functions における診断 (Azure Functions)](https://docs.microsoft.com/azure/azure-functions/durable-functions-diagnostics)」をご覧ください
    * **runtimeStatus**: オーケストレーションのランタイム状態。 実行中、完了、失敗、取り消し済みです。 
* **eventType**: "orchestratorEvent"
* **eventTime**: イベントの時刻 (UTC)。
* **dataVersion**: ライフサイクル イベント スキーマのバージョン。
* **metadataVersion**: メタデータのバージョン。
* **topic**: EventGrid トピックのリソース。

## <a name="how-to-test-locally"></a>ローカルでテストする方法

ローカルでテストするには、[ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok) を使用します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Durable Functions でのインスタンスの管理について](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Durable Functions でのバージョン管理について](durable-functions-versioning.md)
