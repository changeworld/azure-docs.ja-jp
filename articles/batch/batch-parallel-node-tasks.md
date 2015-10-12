<properties
   pageTitle="並列タスクで Batch ノードの使用率を最大にする |Microsoft Azure"
   description="Azure Batch プール内の各ノードで同時実行タスクを実行することにより、使用するコンピューティング ノードの数を減らして効率を向上させ、コストを削減します"
   services="batch"
   documentationCenter=".net"
   authors="mmacy"
   manager="timlt"
   editor=""/>

   <tags
   	ms.service="batch"
   	ms.devlang="multiple"
   	ms.topic="article"
   	ms.tgt_pltfrm="vm-windows"
   	ms.workload="big-compute"
   	ms.date="09/30/2015"
   	ms.author="v-marsma"/>

# 同時実行ノード タスクで Azure Batch コンピューティング リソースの使用率を最大にする

大規模な並列タスクの実行は Azure Batch の中核機能であり、この機能は、タスクを実行する複数のコンピューティング ノードだけでなく、これらのノードでの同時実行タスクの実行にも及びます。Batch では、並列タスク実行はスケールアップおよびスケールアウトします。

プールのコンピューティング ノードでの同時実行タスクの実行を有効にすると、プール内の少数のノードでのリソース使用率を最大化できます。単一のタスクにすべてのノードのリソースを割り当てることでメリットがあるシナリオもありますが、複数のタスクでリソースを共有することにメリットがある状況もあります。

 - 複数のタスクがデータを共有できるときに**データ転送を最小限に抑える**。このシナリオでは、共有データを少数のノードにコピーして、各ノードでタスクを並列に実行すると、データ転送料金を大幅に削減できます。各ノードにコピーされるデータを異なる地理的地域間で転送する必要がある場合は特にそうです。

 - タスクが実行中に不特定の回数、大量のメモリを短時間だけ必要とする場合に、**メモリの使用率を最大化する**。多くのメモリを使用する少数の大きいノード インスタンスを使用して、このようなスパイクを効率的に処理できます。各ノードで実行する並列タスクは、ノードの十分にあるメモリを別々の時間に利用します。

 - プール内でノード間通信が必要な場合に、**ノード数の制限を緩和する**。現在、ノード間通信用に構成されたプールは 50 ノードに制限されているので、このようなプール内の各ノードがタスクを並列に実行できると、より多くのタスクを同時に実行できます。

 - 初めてコンピューティング環境を Azure に移動するときなどに、**オンプレミスのコンピューティング クラスターをレプリケートする**。既存の物理構成が現在 1 つのコンピューティング ノードで複数のタスクを実行している場合、ノード タスクの最大数を増やして、そのような構成をいっそう緊密にミラーできます。

## サンプル シナリオ

並列タスク実行の利点を示すため、タスクアプリケーションの CPU とメモリの要件には Standard\_D1 ノードのサイズが適切であり、必要な時間内にジョブを実行するにはそのようなノードが 1000 個必要である場合について考えてみます。Standard\_D1 ノードを使用する代わりに、16 コアの Standard\_D14 ノードを使用して並列タスク実行を有効にすることもできます。この場合は、*ノードの数は 16 分の 1* なので、必要なのは 1000 ノードではなく 63 ノードだけです。各ノードで大きなアプリケーション ファイルまたは参照データが必要な場合は、これでジョブの実行時間と効率を大幅に向上できます。

## 並列タスク実行を有効にする

並列タスク実行のための Batch ソリューションでのコンピューティング ノードの構成は、プールのレベルで行います。Batch .NET API を使用するときは、プールを作成するときに [CloudPool.MaxTasksPerComputeNode][maxtasks_net] プロパティを設定します。Batch REST API では、プール作成時の要求本文で [maxTasksPerNode][maxtasks_rest] 要素を設定します。

Azure Batch では、ノード設定あたりのタスクの数を、ノード コアの数の最大 4 倍まで設定できます。たとえば、プールをノード サイズ "Large" (4 コア) で構成した場合、`maxTasksPerNode` は 16 に設定できます。各ノード サイズに対するコア数の詳細については、「[仮想マシンのサイズ](../virtual-machines/virtual-machines-size-specs.md)」を参照してください。また、サービスの制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

> [AZURE.TIP]プールの[自動スケールの数式][enable_autoscaling]を作成するときは、`maxTasksPerNode` の値を考慮してください。たとえば、`$RunningTasks` を評価する式は、ノードあたりのタスク数の増加によって大きな影響を受ける可能性があります。詳細については、「[Azure Batch プール内のコンピューティング ノードの自動スケール](batch-automatic-scaling.md)」を参照してください。

## タスクの分散

プール内のコンピューティング ノードがタスクを同時に実行できる場合、プール内のノード間へのタスクの分散方法を指定することが重要です。

[CloudPool.TaskSchedulingPolicy][task_schedule] プロパティを使用することにより、プール内のすべてのノードに均等にタスクを割り当てるか ("スプレッド")、または 1 つのノードに可能な限り多くのタスクを割り当ててからプールの次のノードに移るか ("パック") を指定できます。

この機能の例として、前の例の Standard\_D14 ノードのプールを [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 値 16 で構成した場合について考えます。[CloudPool.TaskSchedulingPolicy][task_schedule] を [ComputeNodeFillType][fill_type] (*パック*) に設定した場合、各ノードの全 16 コアの使用率が最大になり、[自動スケール プール](./batch-automatic-scaling.md)は使用されていないノード (タスクが割り当てられていないノード) をプールから削除できるので、リソースの使用率を最小にしてコストを節約できます。

## Batch .NET の例

次の [Batch .NET][api_net] API コードで示されている要求によって作成されるプールは 4 つの大きいノードを含み、ノードごとのタスク数の最大は 4 つであり、1 つのノードがタスクでいっぱいになってからプールの別のノードにタスクを割り当てるタスク スケジュール ポリシーが指定されています。Batch .NET API を使用してプールを追加する詳細は、「[BatchClient.PoolOperations.CreatePool][poolcreate_net]」を参照してください。

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Batch REST の例

次の [Batch REST][api_rest] API では、ノードごとの最大タスク数が 4 である 2 個の大きいノードを含むプールを作成する要求を示します。REST API を使用したプールの追加の詳細は、「[プールの追加][maxtasks_rest]」を参照してください。

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]`maxTasksPerNode` 要素および [MaxTasksPerComputeNode][maxtasks_net] プロパティは、プール作成時にのみ設定できます。作成後のプールでは変更できません。

## サンプル プロジェクトの例

GitHub の [ParallelNodeTasks][parallel_tasks_sample] プロジェクトには、[CloudPool.MaxTasksPerComputeNode][maxtasks_net] の使用方法を示すコード サンプルが含まれます。この C# コンソール アプリケーションは、[Batch .NET][api_net] ライブラリを使用して 1 つ以上のコンピューティング ノードを含むプールを作成し、構成可能な数のタスクをこれらのノードで実行して、可変の負荷をシミュレートします。アプリケーションの出力では、各タスクを実行したノード、およびジョブ パラメーターと期間の概要が、詳しく示されています。サンプル アプリケーションの 2 つの異なる実行からの出力の概要部分を以下に示します。

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

サンプル アプリケーションの 1 回目の実行では、プール内の 1 つのノードで、ノードごとに 1 タスクの既定値が使用されており、ジョブの実行時間は 30 分以上であることが示されています。

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

サンプルの 2 回目の実行では、ジョブの実行時間が大幅に短縮されています。これは、ノードごとのタスク数を 4 にしてプールが構成されているためであり、並列タスク実行によりほぼ 4 分の 1 の時間でジョブを完了できます。

> [AZURE.NOTE]上記のジョブ期間には、プールの作成時間は含まれません。上の各ジョブが前に作成されたプールに送信され、送信の時点でコンピューティング ノードは*アイドル*状態でした。

## Batch Explorer のヒート マップ

Azure Batch の[サンプル アプリケーション][github_samples]の 1 つである [Batch Explorer][batch_explorer] には、プール内のノード コアの使用状況を表示できる*ヒート マップ*機能が含まれます。[ParallelTasks][parallel_tasks_sample] サンプル アプリケーションを実行し、ヒート マップ機能を使用してノード コアの活動を簡単に表示できます。

![Batch Explorer のヒート マップ][1]

*Batch Explorer のヒート マップ、ノードあたり 4 コアの 4 ノードで各コアがタスクを実行している状態*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/ja-JP/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=Oct15_HO1-->