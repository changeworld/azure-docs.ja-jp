---
title: タスクとノードの状態をカウントする - Azure Batch | Microsoft Docs
description: Azure Batch タスクと計算ノードの状態をカウントし、Batch ソリューションを容易に管理および監視できるようにします。
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 06/29/2018
ms.author: danlep
ms.openlocfilehash: f4bad3d7058e82a246afce9502d275c7d485cb88
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011950"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>タスクとノードを状態別にカウントして、Batch ソリューションを監視する

大規模な Azure Batch ソリューションを監視および管理するには、さまざまな状態のリソースを正確にカウントする必要があります。 Azure Batch では、Batch "*タスク*"と"*計算ノード*"の数を効率的な操作で取得できます。 タスクまたはノードの大きなコレクションについての詳細な情報を返すには、時間がかかる可能性がある API 呼び出しではなく、これらの操作を使用してください。

* [[Get Task Counts]\(タスク数の取得\)][rest_get_task_counts]。ジョブ内のアクティブなタスク、実行中のタスク、および完了したタスクの数と、成功したタスクまたは失敗したタスクの数の集計を取得します。 

  各状態のタスクをカウントすると、より簡単にジョブの進行状況をユーザーに表示したり、ジョブに影響を与える恐れのある予期しない遅延や失敗を検出したりできます。 [Get Task Counts]\(タスク数の取得\) は、Batch サービス API バージョン 2017-06-01.5.1 とそれに関連する SDK およびツールで使用できます。

* [[List Pool Node Counts]\(プール ノード数の一覧表示\)][rest_get_node_counts]。各プール内の専用の計算ノードと低優先度の計算ノードの数を状態別に取得します (状態には、作成中、アイドル、オフライン、 割り込み、リブート中、再イメージ化中、起動中などがあります)。 

  各状態のノードをカウントすると、ジョブの実行に適した計算リソースをいつ用意するかを判断したり、プールに関する潜在的な問題を識別したりできます。 [List Pool Node Counts]\(プール ノード数の一覧表示\) は、Batch サービス API バージョン 2018-03-01.6.1 とそれに関連する SDK およびツールで使用できます。

ご使用のバージョンのサービスで、タスク カウント操作やノード カウント操作がサポートされていない場合は、代わりにリスト クエリを使用して、これらのリソースをカウントします。 リスト クエリは、アプリケーション、ファイル、ジョブなどの他の Batch リソースに関する情報を取得する場合にも使用します。 リスト クエリにフィルターを適用する方法の詳細については、「[効率的に Batch リソースを一覧表示するクエリを作成する](batch-efficient-list-queries.md)」を参照してください。

## <a name="task-state-counts"></a>タスクの状態の数

[Get Task Counts]\(タスク数の取得\) 操作では、タスクが次の状態別にカウントされます。

- **アクティブ (Active)** - キューに入っており、実行可能であるが、現在、計算ノードに割り当てられていないタスクです。 まだ完了していない[親タスクに依存](batch-task-dependencies.md)しているタスクも `active` です。 
- **実行中 (Running)** - 計算ノードに割り当てられているが、まだ完了していないタスクです。 [[Get information about a task]\(タスクに関する情報の取得\)][rest_get_task] 操作で示された状態が `preparing` または `running` のタスクは、`running` としてカウントされます。
- **完了 (Completed)** - 正常に完了したためもう実行できないタスク、または正常に完了しなかったが再試行制限に達したため実行できないタスクです。 
- **成功 (Succeeded)** - タスク実行の結果が `success` であるタスクです。 Batch は、[executionInfo][rest_get_exec_info] プロパティの `TaskExecutionResult` プロパティを調べることで、タスクが成功したか失敗したかを判断します。
- **失敗 (Failed)** - タスク実行の結果が `failure` であるタスクです。

次の .NET コード サンプルは、状態別のタスクの数を取得する方法を示しています。 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

REST やその他のサポートされている言語で同様のパターンを使用して、ジョブのタスクの数を取得できます。 
 

### <a name="consistency-checking-for-task-counts"></a>タスク数の整合性チェック

Batch では、システムの複数のコンポーネントに対して整合性チェックを実行することで、タスクの状態の数の追加検証を行っています。 めったにありませんが、整合性チェックでエラーが検出された場合、Batch は、整合性チェックの結果に基づいて Get Tasks Counts 操作の結果を修正します。

応答内の `validationStatus` プロパティは、Batch が整合性チェックを実行したかどうかを示します。 Batch が、システムに保持されている実際の状態と照らして状態の数をチェックしなかった場合、`validationStatus` プロパティは `unvalidated` に設定されます。 パフォーマンス上の理由から、ジョブに含まれるタスクが 200,000 を超える場合は、整合性チェックは実行されません。そのため、この場合、`validationStatus` プロパティは `unvalidated` に設定されます。 (この場合、タスク数は必ずしも不正確になるとは限りません。データの消失が制限されても、その可能性は低いためです。) 

タスクの状態が変わると、集計パイプラインが数秒以内にその変更を処理します。 Get Task Counts 操作は、更新されたタスクの数をその期間内に反映します。 ただし、集計パイプラインがタスクの状態の変更を見逃した場合、その変更は次回の検証パスまで登録されることはありません。 この期間中、見逃されたイベントのせいでタスクの数はわずかに不正確になる可能性がありますが、それは次回の検証パスで修正されます。

## <a name="node-state-counts"></a>ノードの状態の数

[List Pool Node Counts]\(プール ノード数の一覧表示\) 操作では、 各プール内で計算ノードが次の状態別にカウントされます。 各プール内の専用のノードと低優先度のノードについて、別々に集計数が提供されます。

- **作成中 (Creating)** - Azure によって割り当てられたが、プールへの参加をまだ開始していない VM です。
- **アイドル (Idle)** - 現在タスクを実行していない使用可能な計算ノードです。
- **プールに存在しない (LeavingPool)** - ユーザーが明示的に削除したか、プールがサイズ変更または自動的に縮小したために、プールに存在しないノードです。
- **オフライン (Offline)** - 新しいタスクをスケジュールする際に Batch が使用できないノードです。
- **割り込み (Preempted)** - Azure が VM を再利用するのでプールから削除された低優先度のノードです。 `preempted` ノードは、代替の低優先度 VM の容量が使用可能のときに再初期化できます。
- **リブート中 (Rebooting)** - 再起動中のノードです。
- **再イメージ化中 (Reimaging)** - オペレーティング システムが再インストールされているノードです。
- **実行中 (Running)** - 1 つ以上のタスク (起動タスク以外) を実行しているノードです。
- **起動中 (Starting)** - Batch サービスが起動中のノードです。 
- **起動タスクが失敗 (StartTaskFailed)** - [起動タスク][rest_start_task]が失敗して再試行回数をすべてを使い切り、起動タスク上で `waitForSuccess` が設定されているノードです。 このノードは、タスクの実行に使用できません。
- **不明 (Unknown)** - Batch サービスへの接続を失い、状態が不明なノードです。
- **使用不可 (Unusable)** - エラーのためタスクの実行に使用できないノードです。
- **起動タスクの待機中 (WaitingForStartTask)** - 起動タスクが実行を開始したが、`waitForSuccess` が設定されており、起動タスクが完了していないノードです。

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
 
## <a name="next-steps"></a>次の手順

* Batch サービスの概念と機能の詳細については、[Batch 機能の概要](batch-api-basics.md)に関するページを参照してください。 記事では、プール、コンピューティング ノード、ジョブ、タスクなどの主要な Batch リソースと、サービスの機能の概要について説明しています。

* Batch リソースを一覧表示するクエリにフィルターを適用する方法については、「[効率的に Batch リソースを一覧表示するクエリを作成する](batch-efficient-list-queries.md)」を参照してください。


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get#executionInfo
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

