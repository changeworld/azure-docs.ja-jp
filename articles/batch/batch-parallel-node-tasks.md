---
title: タスクを並列実行し、コンピューティング リソースを最適化する
description: Azure Batch プール内の各ノードで同時実行タスクを実行し、使用するコンピューティング ノードの数を減らすことで、効率を高めて、コストを削減します。
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 3c3a81aa624ccc67c0f9e8ec23e5ef9b8e61c724
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851001"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>タスクの同時実行による Batch コンピューティング ノードの使用率の最大化

Azure Batch プールでは、各コンピューティング ノードで複数のタスクを同時に実行することによって、リソースの使用率を最大限に高めつつ、プールに必要なノード数を減らすことができます。 ワークロードによっては、ジョブの時間が短縮され、コストを削減できます。

単一のタスクにすべてのノードのリソースを割り当てることでメリットがあるシナリオもありますが、複数のタスクでリソースを共有することにメリットがある状況もあります。

* **データ転送を最小限に抑える** 。 このシナリオでは、共有データを少数のノードにコピーし、各ノードでタスクを並列実行することにより、データ転送の費用を大幅に削減できます。 これが特に当てはまるのは、各ノードにコピーするデータを、リージョン間で転送する必要がある場合です。
* **メモリの使用率を最大化する** 。 より多くのメモリを使用する少数の大きなコンピューティング ノードを使用することで、このようなスパイクを効率的に処理できます。 これらのノードには、各ノードで並列で実行されるタスクが割り当てられますが、各タスクは、ノードの豊富なメモリを異なるタイミングで利用できます。
* **ノード数の制限を緩和する** 。 現時点では、ノード間通信用に構成されるプールは 50 のコンピューティング ノードに制限されています。 このようなプール内の各ノードがタスクを並列に実行できると、より多くのタスクが同時に実行できるようになります。
* 初めてコンピューティング環境を Azure に移行するときなどに、**オンプレミスのコンピューティング クラスターをレプリケートする**。 現在のオンプレミスのソリューションがコンピューティング ノードごとに複数のタスクを実行している場合、ノード タスクの最大数を増やすことで、その構成をより厳密に反映できます。

## <a name="example-scenario"></a>サンプル シナリオ
並列タスク実行の利点を示す例として、タスク アプリケーションの CPU とメモリの要件を満たすノードのサイズが [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) である場合について考えてみます。 ただし、必要な時間内にジョブを終えるには、そのようなサイズのノードが 1,000 個必要です。

1 個の CPU コアを持つ Standard\_D1 ノードを使用する代わりに、それぞれが 16 個のコアを持つ [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) ノードを使用して、並列タスクの実行を有効にすることもできます。 この場合、" *ノードの数は 16 分の 1* " で済むため、必要なのは 1,000 ノードではなく 63 ノードのみになります。 加えて、大きなアプリケーション ファイルまたは参照データが各ノードに必要である場合、データのコピー先がわずか 63 ノードで済むため、ジョブの実行時間と効率の面でも改善が期待できます。

## <a name="enable-parallel-task-execution"></a>並列タスク実行を有効にする
並列タスク実行のためのコンピューティング ノードの構成は、プール レベルで行います。 Batch .NET ライブラリを使用する場合は、プールを作成するときに [CloudPool.TaskSlotsPerNode][maxtasks_net] プロパティを設定します。 Batch REST API を使用する場合は、プール作成時の要求本文に [taskSlotsPerNode][rest_addpool] 要素を設定します。

Azure Batch では、ノードあたりのタスク スロット数をノード コアの数の最大 4 倍まで設定できます。 たとえば、プールをノード サイズ "Large" (4 コア) で構成した場合、 `taskSlotsPerNode` は 16 に設定できます。 ただし、ノードのコア数には関係なく、ノードあたり 256 タスク スロットを超えることはできません。 各ノード サイズのコア数の詳細については、「 [Cloud Services のサイズ](../cloud-services/cloud-services-sizes-specs.md)」を参照してください。 サービスの制限の詳細については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

> [!TIP]
> プールの[自動スケールの数式][enable_autoscaling]を作成するときは、`taskSlotsPerNode` の値を考慮してください。 たとえば、 `$RunningTasks` を評価する式は、ノードあたりのタスク数の増加によって大きな影響を受ける可能性があります。 詳細については、「 [Azure Batch プール内のコンピューティング ノードの自動スケール](batch-automatic-scaling.md) 」を参照してください。
>
>

> [!NOTE]
> `taskSlotsPerNode` 要素および [TaskSlotsPerNode][maxtasks_net] プロパティは、プール作成時にのみ設定できます。 これらは、プール作成後に変更することはできません。
>
>

## <a name="distribution-of-tasks"></a>タスクの分散
プール内のコンピューティング ノードが複数のタスクを同時に実行できる場合、プール内の各ノードへのタスクの分散方法を指定することが重要です。

[CloudPool.TaskSchedulingPolicy][task_schedule] プロパティを使用することで、プール内のすべてのノードに対して均等にタスクを割り当てるように指定できます ("分散")。 または、1 つのノードにできる限り多くのタスクを割り当ててから、プール内の別のノードにタスクを割り当てるように指定することもできます ("圧縮")。

この機能の有効性を示す例として、前述の [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) ノードのプールを [CloudPool.TaskSlotsPerNode][maxtasks_net] 値 16 で構成した場合について考えます。 [ComputeNodeFillType][fill_type] に *Pack* を指定して [CloudPool.TaskSchedulingPolicy][task_schedule] を構成した場合、各ノードの 16 コアすべての使用率が最大になり、[自動スケール プール](batch-automatic-scaling.md)が未使用ノード (タスクが割り当てられていないノード) をプールから削除できます。 これによってリソース使用率は最小になり、コストを節約できます。

## <a name="variable-slots-per-task"></a>タスクごとの可変スロット数
タスクは [CloudTask.RequiredSlots][taskslots_net] プロパティを使用して定義できます。このプロパティは、コンピューティング ノードでの実行に必要なスロットの数を指定するもので、既定値は 1 です。 コンピューティング ノードでのリソースの使用量の比重がタスクごとに異なる場合には、可変タスク スロットを設定できます。そうすることで、各コンピューティング ノードで、CPU やメモリなどのシステム リソースを圧迫しない数の同時実行タスクが実行されるようにすることができます。

たとえば、プロパティ `taskSlotsPerNode = 8` が指定されたプールの場合、マルチコアを必要とする CPU 負荷の高いタスクを `requiredSlots = 8` として送信できる一方で、その他のタスクは `requiredSlots = 1` とすることができます。 この混合ワークロードがプールに対してスケジュールされた場合、CPU 負荷の高いタスクはコンピューティング ノード上で排他的に実行されますが、他のタスクは他のノードで (最大 8 個まで) 同時に実行できます。 これにより、コンピューティング ノード間でワークロードのバランスを調整し、リソースの使用効率を向上させることができます。

> [!TIP]
> 可変タスク スロットを使用する場合、多くのスロットを必要とする大きなタスクを一時的にスケジュールできない可能性があります。これは、一部のノードにアイドル スロットが残っていたとしても、十分なスロットが利用可能なコンピューティング ノードがないためです。 これらのタスクに対するジョブの優先度を上げることで、ノードで利用可能なスロットを勝ち取る可能性を高めることができます。
>
> また、タスクの実行のスケジュールに失敗した場合には、Batch サービスによって [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) が出力され、必要なスロットが利用可能になるまでスケジュールの再試行が継続されます。 そのイベントをリッスンして、潜在的なタスクのスケジュール スタックの問題を検出し、それに応じて軽減策を実行できます。
>

> [!NOTE]
> タスクの `requiredSlots` をプールの `taskSlotsPerNode` よりも大きい値に指定しないでください。 こうしてしまうと、タスクが実行できなくなります。 現在 Batch サービスでは、タスク送信時にこの検証は行なわれていません。これは、ジョブの送信時にプールがバインドされていない可能性があるか、または無効化および再有効化によって別のプールに変更される可能性があるためです。
>

## <a name="batch-net-example"></a>Batch .NET の例
次の [Batch .NET][api_net] API コード スニペットでは、ノードごとに複数のタスク スロットを持つプールを作成し、必要なスロット数を指定してタスクを送信する方法を示します。

### <a name="create-pool"></a>プールの作成
このコード スニペットは、ノードあたり 4 つのタスク スロットが許可されている 4 つのノードを含むプールの作成要求を示しています。 ここでは、1 つのノードがタスクでいっぱいになってからプール内の別のノードにタスクを割り当てるタスク スケジュール ポリシーが指定されています。 Batch .NET API を使用したプールの追加方法の詳細については、「[BatchClient.PoolOperations.CreatePool][poolcreate_net]」を参照してください。

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-task-with-required-slots"></a>必要なスロットを指定したタスクを作成する
このコード スニペットでは、既定以外の `requiredSlots` を指定してタスクを作成します。 このタスクは、コンピューティング ノード上に利用可能な空きスロットが十分にある場合にのみ実行されます。
```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>コンピューティング ノードを、実行中のタスクとスロットの数とともに一覧表示する
このコード スニペットは、プール内のすべてのコンピューティング ノードを一覧表示し、実行中のタスクおよびノードあたりのタスク スロットの数を出力します。
```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>ジョブのタスク数を一覧表示する
このコード スニペットでは、ジョブのタスク数を取得します。その中には、タスク状態ごとのタスクとタスク スロット数の両方が含まれます。
```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST の例
次の [Batch REST][api_rest] API スニペットには、ノードごとの最大タスク数が 4 である 2 個の大きいノードを含むプールの作成要求を示します。 REST API を使用したプールの追加方法の詳細については、「[Add a pool to an account (アカウントにプールを追加する)][rest_addpool]」を参照してください。

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

このスニペットは、既定以外の `requiredSlots` を指定してタスクを追加する要求を示しています。 このタスクは、コンピューティング ノード上に利用可能な空きスロットが十分にある場合にのみ実行されます。
```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample"></a>コード サンプル
[CloudPool.TaskSlotsPerNode][maxtasks_net] プロパティの使用方法は、GitHub の [ParallelNodeTasks][parallel_tasks_sample] プロジェクトで紹介されています。

この C# コンソール アプリケーションでは、[Batch .NET][api_net] ライブラリを使用して、1 つ以上のコンピューティング ノードを含むプールを作成します。 さらに、これらのノードで構成可能な数のタスクを実行して、可変負荷をシミュレートします。 アプリケーションの出力には、各タスクを実行したノードが示されます。 また、ジョブのパラメーターと実行時間の概要も出力されます。 サンプル アプリケーションの 2 つの異なる実行からの出力の概要部分を以下に示します。

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

サンプル アプリケーションの 1 回目の実行では、プール内の 1 つのノードと、ノードごとに 1 タスクという既定の設定が使用され、ジョブの実行時間は 30 分を超えていることがわかります。

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

2 回目の実行では、ジョブの実行時間が大幅に短縮されています。 これは、ノードごとのタスク数を 4 にしてプールが構成されているためであり、並列タスク実行によってほぼ 4 分の 1 の時間でジョブを完了できます。

> [!NOTE]
> 上記のジョブ期間には、プールの作成時間は含まれません。 上記の各ジョブは事前に作成されたプールに送信されており、そのプールのコンピューティング ノードは送信の時点で *アイドル* 状態でした。
>
>

## <a name="next-steps"></a>次のステップ
### <a name="batch-explorer-heat-map"></a>Batch Explorer のヒート マップ
[Batch Explorer][batch_labs] は、Azure Batch アプリケーションの作成、デバッグ、および監視を支援する、豊富な機能を備えた無料のスタンドアロン クライアント ツールです。 Batch Explorer には、タスクの実行を視覚化する "*ヒート マップ*" 機能が含まれます。 [ParallelTasks][parallel_tasks_sample] サンプル アプリケーションを実行すると、ヒート マップ機能を使用し、各ノードでの並列タスクの実行を簡単に視覚化できます。


[api_net]: /dotnet/api/microsoft.azure.batch
[api_rest]: /rest/api/batchservice/
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[enable_autoscaling]: /rest/api/batchservice/pool/enableautoscale
[fill_type]: /dotnet/api/microsoft.azure.batch.common.computenodefilltype
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: /dotnet/api/microsoft.azure.batch.cloudpool
[rest_addpool]: /rest/api/batchservice/pool/add
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: /dotnet/api/microsoft.azure.batch.pooloperations
[task_schedule]: /dotnet/api/microsoft.azure.batch.cloudpool
[taskslots_net]: /dotnet/api/microsoft.azure.batch.cloudtask.requiredslots
