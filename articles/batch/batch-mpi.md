<properties
	pageTitle="Azure Batch でのマルチインスタンス タスクを使用した MPI アプリケーションの実行 | Microsoft Azure"
	description="Azure Batch でマルチインスタンス タスクを使用して、Message Passing Interface (MPI) アプリケーションを実行する方法について説明します。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/29/2016"
	ms.author="marsma" />

# Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行

マルチインスタンス タスクでは、Azure Batch タスクを複数のコンピューティング ノードで同時に実行できます。これらのタスクにより、Batch での Message Passing Interface (MPI) アプリケーションのようなハイ パフォーマンス コンピューティングのシナリオが可能になります。この記事では、[Batch .NET][api_net] ライブラリを使用してマルチインスタンス タスクを実行する方法について説明します。

## マルチインスタンス タスクの概要

Batch では、通常、各タスクは単一のコンピューティング ノードで実行されます。複数のタスクをジョブに送信すると、Batch サービスによってノードで実行する各タスクがスケジュールされます。ただし、タスクの**マルチインスタンス設定**を構成することで、複数のノードで実行できるようにタスクをサブタスクに分割するよう Batch に指示できます。

![マルチインスタンス タスクの概要][1]

マルチインスタンス設定が構成されているタスクをジョブに送信すると、Batch によってマルチインスタンス タスク固有の次の手順が実行されます。

1. Batch サービスによって、タスクが 1 つの**プライマリ** タスクと複数の**サブタスク**に自動的に分割されます。次に、プールのコンピューティング ノードで実行するために、プライマリ タスクとサブタスクがスケジュールされます。
2. これらのタスク (プライマリ タスクとサブタスクの両方) では、マルチインスタンス設定で指定した**共通リソース ファイル**がダウンロードされます。
3. 共通リソース ファイルがダウンロードされると、マルチインスタンス設定で指定した**調整コマンド**がプライマリ タスクとサブタスクで実行されます。通常は、この調整コマンドを使用してバックグラウンド サービス ([Microsoft MPI][msmpi_msdn] の `smpd.exe` など) を開始します。また、各ノードがノード間のメッセージを処理できる状態であることを確認する場合もあります。
4. プライマリ タスクとすべてのサブタスクで調整コマンドが正常に完了すると、マルチインスタンス タスクの**コマンド ライン** ("アプリケーション コマンド") が**プライマリ タスク**で "*のみ*" 実行されます。たとえば、[MS-MPI][msmpi_msdn] ベースのソリューションでは、このコマンド ラインで `mpiexec.exe` を使用して MPI 対応アプリケーションを実行します。

> [AZURE.NOTE] "マルチインスタンス タスク" は機能的には異なりますが、[StartTask][net_starttask] や [JobPreparationTask][net_jobprep] のような一意のタスクの種類ではありません。マルチインスタンス タスクは、マルチインスタンス設定が構成されている標準の Batch タスク (Batch .NET の [CloudTask][net_task]) です。この記事では、これを**マルチインスタンス タスク**と呼んでいます。

## マルチインスタンス タスクの要件

マルチインスタンス タスクには、**ノード間通信が有効**で、**同時実行タスクの実行が無効**になっているプールが必要です。ノード間通信が無効になっている (*maxTasksPerNode* 値が 1 より大きい) プールでマルチインスタンス タスクを実行しようとしても、タスクはスケジュールされず、いつまでも "アクティブ" 状態のままになります。次のコード スニペットは、Batch .NET ライブラリを使用してそのようなプールを作成する方法を示しています。

```csharp
CloudPool myCloudPool =
	myBatchClient.PoolOperations.CreatePool(
		poolId: "MultiInstanceSamplePool",
		targetDedicated: 3
		virtualMachineSize: "small",
		cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

マルチインスタンス タスクは、**2015 年 12 月 14 日より後に作成されたプール**のノードで "*のみ*" 実行されます。

> [AZURE.TIP] Batch プールで [A8 または A9 サイズのコンピューティング ノード](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)を使用している場合、MPI アプリケーションは Azure の高パフォーマンスで待機時間の短いリモート ダイレクト メモリ アクセス (RDMA) ネットワークを利用できます。Batch プールで使用できるコンピューティング ノードのサイズの一覧については、「[Cloud Services のサイズ](./../cloud-services/cloud-services-sizes-specs.md)」をご覧ください。

### StartTask を使用した MPI アプリケーションのインストール

マルチインスタンス タスクを使用して MPI アプリケーションを実行するには、まずプールのコンピューティング ノードで MPI ソフトウェアを取得する必要があります。これは、[StartTask][net_starttask] を使用する絶好の機会です。StartTask は、ノードがプールに参加するたびに実行され、ノードの再起動時にも実行されます。次のコード スニペットでは、[リソース ファイル][net_resourcefile]として MS-MPI セットアップ パッケージを指定し、リソース ファイルがノードにダウンロードされた後に実行されるコマンド ラインを指定した StartTask を作成しています。

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

> [AZURE.NOTE] Batch でマルチインスタンス タスクを使用する MPI ソリューションを実装する場合、MS-MPI しか使用できないというわけではありません。プールのコンピューティング ノードに指定したオペレーティング システムと互換性のある MPI 標準の任意の実装を使用できます。

## Batch .NET を使用したマルチインスタンス タスクの作成

プールの要件と MPI パッケージのインストールについて説明しました。次に、マルチインスタンス タスクを作成します。次のスニペットでは、標準の [CloudTask][net_task] を作成し、[MultiInstanceSettings][net_multiinstance_prop] プロパティを構成しています。前述のように、マルチインスタンス タスクはタスクの種類ではなく、マルチインスタンス設定で構成された標準の Batch タスクです。

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## プライマリ タスクとサブタスク

タスクのマルチインスタンス設定を作成するときは、タスクを実行するコンピューティング ノードの数を指定します。タスクをジョブに送信すると、Batch サービスによって、1 つの**プライマリ** タスクと、指定したノードの数に対応する十分な数の**サブタスク**が作成されます。

これらのタスクには、0 から *numberOfInstances - 1* の範囲の整数 ID が割り当てられます。ID が 0 のタスクがプライマリ タスクで、他のすべての ID がサブタスクです。たとえば、タスクに対して次のマルチインスタンス設定を作成した場合、プライマリ タスクには ID として 0 が割り当てられ、サブタスクには 1 ～ 9 が割り当てられます。

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

## 調整コマンド

**調整コマンド**は、プライマリ タスクとサブタスクの両方で実行されます。

調整コマンドの呼び出しはブロックされています。すべてのサブタスクで調整コマンドから正常に制御が返されるまで、アプリケーション コマンドは実行されません。そのため、調整コマンドは必要なバックグラウンド サービスを開始し、それらのサービスの使用準備ができていることを確認してから終了する必要があります。たとえば、MS-MPI バージョン 7 を使用するソリューションの次の調整コマンドは、ノードで SMPD サービスを開始してから終了します。

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

この調整コマンドでは、`start` を使用していることに注意してください。`smpd.exe` アプリケーションは実行後すぐに制御を返さないため、これが必須となります。[start][cmd_start] コマンドを使用していない場合、この調整コマンドは制御を返さないため、アプリケーション コマンドを実行できません。

## アプリケーション コマンド

プライマリ タスクとすべてのサブタスクで調整コマンドの実行が完了すると、マルチインスタンス タスクのコマンド ラインがプライマリ タスクで "*のみ*" 実行されます。調整コマンドと区別するために、これを**アプリケーション コマンド**と呼びます。

MS-MPI アプリケーションでは、アプリケーション コマンドを使用して、`mpiexec.exe` で MPI 対応のアプリケーションを実行します。たとえば、MS-MPI バージョン 7 を使用するソリューションのアプリケーション コマンドは、次のようになります。

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] MS-MPI の `mpiexec.exe` は既定で `CCP_NODES` 変数を使用するため (「[環境変数](#environment-variables)」を参照)、上記のアプリケーション コマンド ラインの例では、この変数が除外されています。

## 環境変数

次の環境変数は、コンピューティング ノードでプライマリ タスクのアプリケーション コマンドによって実行されるアプリケーションとスクリプトで使用できます。

* `CCP_NODES`

  * **使用可能**: CloudServiceConfiguration および VirtualMachineConfiguration プール
  * **形式**: `numNodes<space>nodeIP<space>numCores<space>`
  * **例**: `2 10.0.0.4 1 10.0.0.5 1`

* `AZ_BATCH_NODE_LIST`

  * **使用可能**: CloudServiceConfiguration および VirtualMachineConfiguration プール
  * **形式**: `nodeIP;nodeIP`
  * **例**: `10.0.0.4;10.0.0.5`

* `AZ_BATCH_HOST_LIST`

  * **使用可能**: VirtualMachineConfiguration プール
  * **形式**: `nodeIP,nodeIP`
  * **例**: `10.0.0.4,10.0.0.5`

## リソース ファイル

マルチインスタンス タスクには、考慮すべきリソース ファイルのセットが 2 つあります。"*すべてのタスク*" (プライマリ タスクとサブタスクの両方) でダウンロードされる**共通リソース ファイル**と、マルチインスタンス タスク自体に指定され、"*プライマリ タスクでのみ*" ダウンロードされる**リソース ファイル**です。

タスクのマルチインスタンス設定で 1 つ以上の**共通リソース ファイル**を指定できます。これらの共通リソース ファイルは、プライマリ タスクとすべてのサブタスクで、[Azure Storage](./../storage/storage-introduction.md) から各ノードのタスク共有ディレクトリにダウンロードされます。タスク共有ディレクトリには、`AZ_BATCH_TASK_SHARED_DIR` 環境変数を使用して、アプリケーション コマンド ラインと調整コマンド ラインからアクセスできます。

マルチインスタンス タスク自体に指定したリソース ファイルは、プライマリ タスクで "*のみ*"、そのタスクの作業ディレクトリ (`AZ_BATCH_TASK_WORKING_DIR`) にダウンロードされます。マルチインスタンス タスクに指定したリソース ファイルは、サブタスクではダウンロードされません。

`AZ_BATCH_TASK_SHARED_DIR` の内容には、ノードで実行されるプライマリ タスクとすべてのサブタスクがアクセスできます。タスク共有ディレクトリの例として、`tasks/mybatchjob/job-1/mymultiinstancetask/` があります。プライマリ タスクと各サブタスクには、そのタスクだけがアクセスできる作業ディレクトリもあります。作業ディレクトリには、`AZ_BATCH_TASK_WORKING_DIR` 環境変数を使用してアクセスします。

この記事のコード サンプルでは、マルチインスタンス タスク自体のリソース ファイルは指定していません。プールの StartTask とマルチインスタンス設定の [CommonResourceFiles][net_multiinsance_commonresfiles] のリソース ファイルのみを指定しています。

> [AZURE.IMPORTANT] コマンド ラインでこれらのディレクトリを参照するときは、環境変数 `AZ_BATCH_TASK_SHARED_DIR` と `AZ_BATCH_TASK_WORKING_DIR` を常に使用します。パスを手動で作成しないでください。

## タスクの有効期間

プライマリ タスクの有効期間によって、マルチインスタンス タスク全体の有効期間が制御されます。プライマリ タスクが終了すると、すべてのサブタスクが終了します。プライマリ タスクの終了コードがタスクの終了コードになるため、再試行のためにタスクが成功したかどうかを判断する際に使用されます。

サブタスクのいずれかが失敗した場合 (ゼロ以外のリターン コードでの終了など)、マルチインスタンス タスク全体が失敗します。この場合、マルチインスタンス タスクが終了し、再試行制限回数に達するまで再試行されます。

マルチインスタンス タスクを削除すると、Batch サービスによってプライマリ タスクとすべてのサブタスクも削除されます。標準タスクの場合と同様に、サブタスクのすべてのディレクトリとファイルがコンピューティング ノードから削除されます。

マルチインスタンス タスクの [TaskConstraints][net_taskconstraints] \([MaxTaskRetryCount][net_taskconstraint_maxretry]、[MaxWallClockTime][net_taskconstraint_maxwallclock]、[RetentionTime][net_taskconstraint_retention] の各プロパティなど) は、標準タスクの場合と同様に優先され、プライマリ タスクとすべてのサブタスクに適用されます。ただし、マルチインスタンス タスクをジョブに追加した後に [RetentionTime][net_taskconstraint_retention] プロパティを変更した場合、この変更はプライマリ タスクにのみ適用されます。すべてのサブタスクで引き続き元の [RetentionTime][net_taskconstraint_retention] が使用されます。

最近のタスクがマルチインスタンス タスクの一部である場合、コンピューティング ノードの最近のタスク リストにそのサブタスクの ID が示されます。

## サブタスクに関する情報の取得

Batch .NET ライブラリを使用してサブタスクの情報を取得するには、[CloudTask.ListSubtasks][net_task_listsubtasks] メソッドを呼び出します。このメソッドは、すべてのサブタスクの情報と、それらのタスクが実行されたコンピューティング ノードの情報を返します。この情報から、各サブタスクのルート ディレクトリ、プール ID、現在の状態、終了コードなどを確認できます。この情報を [PoolOperations.GetNodeFile][poolops_getnodefile] メソッドと共に使用して、サブタスクのファイルを取得できます。このメソッドは、プライマリ タスク (ID 0) の情報を返さないことに注意してください。

> [AZURE.NOTE] 特に記載のない限り、[CloudTask][net_task] マルチインスタンス自体に影響する Batch .NET のメソッドは、プライマリ タスクに "*のみ*" 適用されます。たとえば、マルチインスタンス タスクで [CloudTask.ListNodeFiles][net_task_listnodefiles] メソッドを呼び出すと、プライマリ タスクのファイルだけが返されます。

次のコード スニペットは、サブタスクの情報と、サブタスクが実行されたノードの要求ファイルの内容を取得する方法を示しています。

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
			await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## 次のステップ

- Batch でのマルチインスタンス タスクのテストに使用する単純な MS-MPI アプリケーションを構築できます。The Microsoft HPC & Azure Batch Team のブログ記事「[How to compile and run a simple MS-MPI program (単純な MS-MPI プログラムのコンパイルおよび実行の方法)][msmpi_howto]」に、MS-MPI を使用して単純な MPI アプリケーションを作成する手順が記載されています。

- MS-MPI の最新情報については、MSDN の「[Microsoft MPI][msmpi_msdn]」を参照してください。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "マルチインスタンスの概要"

<!---HONumber=AcomDC_0831_2016-->