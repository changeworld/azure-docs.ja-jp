---
title: クイックスタート - キューとルーティングのためにジョブを送信する
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、ジョブ ルーター クライアント、配布ポリシー、キュー、ジョブを Azure Communication Services リソース内に作成する方法について説明します。
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/18/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ffad9a33c932300dbf52989536663215f8abb8f9
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164770"
---
# <a name="quickstart-submit-a-job-for-queuing-and-routing"></a>クイックスタート: キューとルーティングのためにジョブを送信する

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

クライアントを設定して Azure Communication Services ジョブ ルーターの使用を開始します。 その後、キュー、ポリシー、ワーカー、ジョブなどのコア機能を構成できます。 ジョブ ルーターの概念の詳細については、[ジョブ ルーターの概念に関するドキュメント](../../concepts/router/concepts.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`JobRouterQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

```console
dotnet new console -o JobRouterQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用してアプリケーションをコンパイルします。

```console
cd JobRouterQuickstart
dotnet build
```

### <a name="install-the-package"></a>パッケージをインストールする

プライベート プレビュー期間中は、[GitHub](https://github.com/Azure/communication-preview/releases/tag/communication-job-router-net-v1.0.0-alpha.20211012.1) から SDK をダウンロードしてください。

> [!NOTE]
> SDK をダウンロードするには、プライベート プレビュー グループのメンバーである必要があります。

**Program.cs** の先頭に以下の `using` ディレクティブを追加し、JOBRouter の名前空間を含めます。

```csharp
using Azure.Communication.JobRouter;
```

`async` となって `Task` を返すように `Main` の関数シグネチャを更新します。

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-job-router-client"></a>ジョブ ルーター クライアントを認証する

ジョブ ルーター クライアントは、Azure portal で Azure Communication Services リソースから取得した接続文字列を使用して認証できます。

```csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "your_connection_string";
var client = new RouterClient(connectionString);
```

## <a name="create-a-distribution-policy"></a>配布ポリシーを作成する

ジョブ ルーターにより、配布ポリシーが使用され、使用可能なジョブと通知の有効期限 (**オファー** とも呼ばれます) をワーカーに通知する方法が決定されます。 **ID**、**名前**、**offerTTL**、配布の **モード** を指定して、ポリシーを作成します。

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);
```

## <a name="create-a-queue"></a>キューを作成する

ジョブは論理キューに編成されます。 **ID**、**名前** を指定してキューを作成し、上記で作成した **配布ポリシー** オブジェクトの ID を指定します。

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: distributionPolicy.Value.Id
);
```

## <a name="submit-a-job"></a>ジョブの送信
作業を開始する最も簡単な方法は、ジョブを送信するときにキューの ID、優先順位、ワーカー要件を指定する方法です。 下の例では、ジョブは **XBOX キュー** に直接送信されます。このキュー内のワーカーには、`Edmonton` という名前と一致する `Location` ラベルが必要です。

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsChatChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelector: new List<LabelSelector>
    {
        new (
            key: "Location", 
            @operator: LabelOperator.Equal, 
            value: "Edmonton")
    });
```

## <a name="register-a-worker"></a>ワーカーを登録する
以前に作成したキュー ID と **容量** 値、**ラベル**、**チャネル構成** を参照してワーカーを登録し、`EdmontonWorker` が "XBOX_Queue" に割り当てられるようにします。

```csharp
var edmontonWorker = await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new []{ queue.Value.Id },
    totalCapacity: 100,
    labels: new LabelCollection()
    {
        {"Location", "Edmonton"}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100)
    }
);
```

## <a name="query-the-worker-to-observe-the-job-offer"></a>ワーカーにクエリを実行してジョブ オファーを確認する
ジョブ ルーター クライアント接続を使用してワーカーにクエリを実行し、ID とジョブの ID を照合します。 

```csharp
    // wait 500ms for the Job Router to offer the Job to the Worker
    Task.Delay(500).Wait();

    var result = await client.GetWorkerAsync(edmontonWorker.Value.Id);

    Console.WriteLine(
        $"Job ID: {job.Value.Id} offered to {edmontonWorker.Value.Id} " +
        $"should match Job ID attached to worker: {result.}");
```

`dotnet run` を使ってアプリケーションを実行し、結果を確認します。

```console
dotnet run

Job 6b83c5ad-5a92-4aa8-b986-3989c791be91 offered to EdmontonWorker should match Job ID from offer attached to worker: 6b83c5ad-5a92-4aa8-b986-3989c791be91
```

> [!NOTE]
> アプリケーションを 2 回以上実行すると、新しいジョブは毎回キューに配置されます。 これにより、上記のコードを実行したときに作成されるジョブ以外のジョブがワーカーに提供される可能性があります。 これにより要求がゆがめられる可能性があるため、キュー内のジョブを毎回削除することを検討してください。 キューまたはジョブの管理については、SDK のドキュメントを参照してください。