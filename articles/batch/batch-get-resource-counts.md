---
title: タスクとノードの状態をカウントする
description: Azure Batch タスクと計算ノードの状態をカウントし、Batch ソリューションを容易に管理および監視できるようにします。
ms.date: 09/10/2021
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 4f17bb97aefef5db2cbfe9d4e344d26cff3f0296
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806677"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>タスクとノードを状態別にカウントして、Batch ソリューションを監視する

大規模な Azure Batch ソリューションを監視および管理するには、さまざまな状態のリソースの数を特定することが必要な場合があります。 Azure Batch には、Batch タスクと計算ノードの数を取得するための効率的な操作が用意されています。 タスクまたはノードの大きなコレクションについての詳細な情報を返す (時間がかかる可能性がある) リスト クエリではなく、これらの操作を使用できます。

- [[Get Task Counts]\(タスク数の取得\)](/rest/api/batchservice/job/gettaskcounts)。ジョブ内のアクティブなタスク、実行中のタスク、および完了したタスクの数と、成功したタスクまたは失敗したタスクの数の集計を取得します。 各状態のタスクをカウントすると、より簡単にジョブの進行状況をユーザーに表示したり、ジョブに影響を与える恐れのある予期しない遅延や失敗を検出したりできます。

- [[List Pool Node Counts]\(プール ノード数の一覧表示\)](/rest/api/batchservice/account/listpoolnodecounts)。各プール内の専用の計算ノードと低優先度の計算ノードの数を状態別に取得します (状態には、作成中、アイドル、オフライン、割り込み、リブート中、再イメージ化中、起動中などがあります)。 各状態のノードをカウントすると、ジョブの実行に適した計算リソースをいつ用意するかを判断したり、プールに関する潜在的な問題を識別したりできます。

これらの操作によって返される値が最新ではない場合があることに注意してください。 正確なカウントが必要な場合は、リスト クエリを使用してこれらのリソースをカウントします。 リスト クエリでは、アプリケーションなどの他の Batch リソースに関する情報を取得することもできます。 リスト クエリにフィルターを適用する方法の詳細については、「[効率的に Batch リソースを一覧表示するクエリを作成する](batch-efficient-list-queries.md)」を参照してください。

## <a name="task-state-counts"></a>タスクの状態の数

[Get Task Counts]\(タスク数の取得\) 操作では、タスクが次の状態別にカウントされます。

- **Active (アクティブ)** : キューに入っており、実行可能であるが、現在、計算ノードに割り当てられていないタスクです。 まだ完了していない[親タスクに依存](batch-task-dependencies.md)しているタスクも `active` です。
- **Running (実行中)** : 計算ノードに割り当てられているが、まだ完了していないタスクです。 [[Get information about a task]\(タスクに関する情報の取得\)](/rest/api/batchservice/task/get) 操作で示された状態が `preparing` または `running` のタスクは、`running` としてカウントされます。
- **Completed (完了)** : 正常に完了したためもう実行できないタスク、または正常に完了しなかったが再試行制限に達したため実行できないタスクです。
- **Succeeded (成功)** : タスク実行の結果が `success` であるタスクです。 Batch は、[executionInfo](/rest/api/batchservice/task/get) プロパティの `TaskExecutionResult` プロパティを調べることで、タスクが成功したか失敗したかを判断します。
- **Failed (失敗)** : タスク実行の結果が `failure` であるタスクです。

次の .NET コード サンプルは、状態別のタスクの数を取得する方法を示しています。

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

REST やその他のサポートされている言語で同様のパターンを使用して、ジョブのタスクの数を取得できます。

## <a name="node-state-counts"></a>ノードの状態の数

[List Pool Node Counts]\(プール ノード数の一覧表示\) 操作では、 各プール内で計算ノードが次の状態別にカウントされます。 各プール内の専用のノードと低優先度のノードについて、別々に集計数が提供されます。

- **Creating (作成中)** : Azure によって割り当てられたが、プールへの参加をまだ開始していない VM です。
- **Idle (アイドル)** : 現在タスクを実行していない使用可能な計算ノードです。
- **LeavingPool (プールに存在しない)** : ユーザーが明示的に削除したか、プールがサイズ変更または自動スケールダウンしたために、プールに存在しないノードです。
- **Offline (オフライン)** : 新しいタスクをスケジュールする際に Batch が使用できないノードです。
- **Preempted (割り込み)** : Azure によって VM が再要求されたためにプールから削除された低優先度のノードです。 `preempted` ノードは、代替の低優先度 VM の容量が使用可能のときに再初期化できます。
- **Rebooting (リブート中)** : 再起動中のノードです。
- **Reimaging (再イメージ化中)** : オペレーティング システムが再インストールされているノードです。
- **Running (実行中)** : 1 つ以上のタスク (起動タスク以外) を実行しているノードです。
- **Starting (起動中)** : Batch サービスが起動中のノードです。
- **StartTaskFailed (起動タスクが失敗)** : [起動タスク](/rest/api/batchservice/pool/add#starttask)が失敗して再試行回数をすべて使い切り、起動タスク上で `waitForSuccess` が設定されているノードです。 このノードは、タスクの実行に使用できません。
- **Unknown (不明)** : Batch サービスへの接続を失い、状態が不明なノードです。
- **Unusable (使用不可)** : エラーのためタスクの実行に使用できないノードです。
- **WaitingForStartTask (起動タスクの待機中)** : 起動タスクが実行を開始したが、`waitForSuccess` が設定されており、起動タスクが完了していないノードです。

次の C# スニペットは、現在のアカウント内のすべてのプールについて、ノード数を一覧表示する方法を示しています。

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

次の C# スニペットは、現在のアカウント内の指定されたプールについて、ノード数を一覧表示する方法を示しています。

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

REST やその他のサポートされている言語で同様のパターンを使用して、プールのノード数を取得できます。

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- Batch リソースを一覧表示するクエリにフィルターを適用する方法については、「[効率的に Batch リソースを一覧表示するクエリを作成する](batch-efficient-list-queries.md)」を学習してください。
