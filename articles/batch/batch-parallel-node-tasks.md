<properties
   pageTitle="並列タスクで Batch ノードの使用率を最大にする |Microsoft Azure"
   description="Azure Batch プール内の各ノードで同時実行タスクを実行すると同時に、使用するコンピューティング ノードの数を減らすことで、効率を高めて、コストを削減します。"
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
   	ms.date="11/02/2015"
   	ms.author="v-marsma"/>

# 同時実行ノード タスクで Azure Batch コンピューティング リソースの使用率を最大にする

大規模な並列タスク実行は、Azure Batch の核となる機能です。この機能の範囲は、タスクを実行する複数のコンピューティング ノードだけでなく、これらのノードで実行している同時実行タスクにまで及びます。Batch では、並列タスク実行はスケールアップおよびスケールアウトします。

プールのコンピューティング ノードでの同時実行タスクの実行を有効にすると、プール内のより少ないノードでリソース使用率を最大化できます。単一のタスクにすべてのノードのリソースを割り当てることでメリットがあるシナリオもありますが、複数のタスクでリソースを共有することにメリットがある状況もあります。

 - 複数のタスクがデータを共有できるときに**データ転送を最小限に抑える**。このシナリオでは、共有データを少数のノードにコピーし、各ノードでタスクを並列実行することにより、データ転送の費用を大幅に削減できます。これが特に当てはまるのは、各ノードにコピーするデータを、地域間で転送する必要がある場合です。

 - タスクが実行中に不特定の回数、大量のメモリを短時間だけ必要とする場合に、**メモリの使用率を最大化する**。より多くのメモリを使用する少数の大きいノード インスタンスを使用することで、このようなスパイクを効率的に処理できます。これらのノード インスタンスには、各ノードで実行する並列タスクが割り当てられます。これにより、ノードの豊富なメモリが異なるタイミングで使用されます。

 - プール内でノード間通信が必要な場合に、**ノード数の制限を緩和する**。現時点では、ノード間通信用に構成されたプールは 50 ノードに制限されています。そのため、このようなプール内の各ノードがタスクを並列に実行できると、より多くのタスクが同時に実行できるようになります。

 - 初めてコンピューティング環境を Azure に移行するときなどに、**オンプレミスのコンピューティング クラスターをレプリケートする**。既存の物理構成が現在 1 つのコンピューティング ノードで複数のタスクを実行している場合、ノード タスクの最大数を増やすことで、その構成をより厳密に反映できます。

## サンプル シナリオ

ここで並列タスク実行による利点を示す例を紹介します。たとえば、タスク アプリケーションの CPU とメモリの要件が、Standard\_D1 のノード サイズが適しているような値だったとします。ただし、必要な時間内にジョブを実行するには、そのようなサイズのノードが 1,000 個必要です。

Standard\_D1 ノードを使用する代わりに、16 コアの Standard\_D14 ノードを使用して並列タスク実行を有効にすることもできます。この場合、*ノードの数は 16 分の 1* で済むため、必要なのは 1000 ノードではなく 63 ノードのみになります。各ノードで大きなアプリケーション ファイルまたは参照データが必要な場合は、これでジョブの実行時間と効率を大幅に向上できます。

## 並列タスク実行を有効にする

並列タスク実行のための Batch ソリューション内のコンピューティング ノードの構成は、プール レベルで行います。Batch .NET API を使用する場合は、プールを作成するときに [CloudPool.MaxTasksPerComputeNode][maxtasks_net] プロパティを設定します。Batch REST API では、プール作成時の要求本文で [maxTasksPerNode][maxtasks_rest] 要素を設定します。

Azure Batch では、ノードあたりの最大タスク数を、ノード コアの数の最大 4 倍まで設定できます。たとえば、プールをノード サイズ "Large" (4 コア) で構成した場合、`maxTasksPerNode` は 16 に設定できます。各ノード サイズのコア数の詳細については、「[Cloud Services のサイズ](../cloud-services/cloud-services-sizes-specs.md)」を参照してください。サービスの制限の詳細については、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

> [AZURE.TIP]プールの[自動スケールの数式][enable_autoscaling]を作成するときは、`maxTasksPerNode` の値を考慮してください。たとえば、`$RunningTasks` を評価する式は、ノードあたりのタスク数の増加によって大きな影響を受ける可能性があります。詳細については、「[Azure Batch プール内のコンピューティング ノードの自動スケール](batch-automatic-scaling.md)」を参照してください。

## タスクの分散

プール内のコンピューティング ノードがタスクを同時に実行できる場合、プール内の各ノードへのタスクの分散方法を指定することが重要です。

[CloudPool.TaskSchedulingPolicy][task_schedule] プロパティを使用することで、プール内のすべてのノードに対して均等にタスクを割り当てるように指定できます ("分散")。または、1 つのノードにできる限り多くのタスクを割り当ててから、プール内の別のノードにタスクを割り当てるように指定することもできます ("圧縮")。

この機能の有効性を示す例として、前述の Standard\_D14 ノードのプールを [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 値 16 で構成した場合について考えます。[ComputeNodeFillType][fill_type] に *Pack* を指定して [CloudPool.TaskSchedulingPolicy][task_schedule] を構成した場合、各ノードの 16 コアすべての使用率が最大になり、[自動スケール プール](./batch-automatic-scaling.md)が未使用ノード (タスクが割り当てられていないノード) をプールから削除できます。これによってリソース使用率は最小になり、コストを節約できます。

## Batch .NET の例

次の [Batch .NET][api_net] API コード スニペットには、ノードごとの最大タスク数が 4 である 4 個の大きいノードを含むプールの作成要求を示します。ここでは、1 つのノードがタスクでいっぱいになってからプール内の別のノードにタスクを割り当てるタスク スケジュール ポリシーが指定されています。Batch .NET API を使用したプールの追加方法の詳細については、「[BatchClient.PoolOperations.CreatePool][poolcreate_net]」を参照してください。

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Batch REST の例

次の [Batch REST][api_rest] API スニペットには、ノードごとの最大タスク数が 4 である 2 個の大きいノードを含むプールの作成要求を示します。REST API を使用したプールの追加方法の詳細については、「[アカウントにプールを追加する][maxtasks_rest]」を参照してください。

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]`maxTasksPerNode` 要素および [MaxTasksPerComputeNode][maxtasks_net] プロパティは、プール作成時にのみ設定できます。これらは、プール作成後に変更することはできません。

## サンプル プロジェクトの例

GitHub にある [ParallelNodeTasks][parallel_tasks_sample] プロジェクトを参照してください。このプロジェクトは、[CloudPool.MaxTasksPerComputeNode][maxtasks_net] の使用方法を示すコード サンプルで、実際に動作します。

この C# コンソール アプリケーションでは、[Batch .NET][api_net] ライブラリを使用して、1 つ以上のコンピューティング ノードを含むプールを作成します。さらに、これらのノードで構成可能な数のタスクを実行して、可変負荷をシミュレートします。アプリケーションの出力には、各タスクを実行したノードが示されます。また、ジョブのパラメーターと実行時間の概要も出力されます。サンプル アプリケーションの 2 つの異なる実行からの出力の概要部分を以下に示します。

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

サンプル アプリケーションの 1 回目の実行では、プール内の 1 つのノードと、ノードごとに 1 タスクという既定の設定が使用され、ジョブの実行時間は 30 分を超えていることがわかります。

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

2 回目の実行では、ジョブの実行時間が大幅に短縮されています。これは、ノードごとのタスク数を 4 にしてプールが構成されているためであり、並列タスク実行によってほぼ 4 分の 1 の時間でジョブを完了できます。

> [AZURE.NOTE]上記のジョブ期間には、プールの作成時間は含まれません。上記の各ジョブは事前に作成されたプールに送信されており、そのプールのコンピューティング ノードは送信の時点で*アイドル*状態でした。

## Batch Explorer のヒート マップ

Azure Batch の[サンプル アプリケーション][github_samples]の 1 つである [Batch Explorer][batch_explorer] には、タスクの実行を視覚化する*ヒート マップ*機能が含まれています。[ParallelTasks][parallel_tasks_sample] サンプル アプリケーションを実行すると、ヒート マップ機能を使用し、各ノードでの並列タスクの実行を簡単に視覚化できます。

![Batch Explorer のヒート マップ][1]

*Batch Explorer のヒート マップに 4 つのノードから成るプールが表示され、各ノードでは現在それぞれ 4 つのタスクが実行されている*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=AcomDC_0114_2016-->