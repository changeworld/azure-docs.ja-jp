---
title: マルチインスタンス タスクを使用した MPI アプリケーションの実行
description: Azure Batch でマルチインスタンス タスクを使用して、Message Passing Interface (MPI) アプリケーションを実行する方法について説明します。
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: e96cfb89b186d69f6ad969949b8df609956114d2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389402"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行

マルチインスタンス タスクでは、Azure Batch タスクを複数のコンピューティング ノードで同時に実行できます。 これらのタスクにより、Batch での Message Passing Interface (MPI) アプリケーションのようなハイ パフォーマンス コンピューティングのシナリオが可能になります。 この記事では、[Batch .NET](/dotnet/api/microsoft.azure.batch) ライブラリを使用してマルチインスタンス タスクを実行する方法について説明します。

> [!NOTE]
> この記事では、Batch .NET、MS-MPI、および Windows コンピューティング ノードの例をあげていますが、ここで説明するマルチインスタンス タスクは、他のプラットフォームとテクノロジ (たとえば Linux ノードの Python および Intel MP) にも適用できます。

## <a name="multi-instance-task-overview"></a>マルチインスタンス タスクの概要

Batch では、通常、各タスクは単一のコンピューティング ノードで実行されます。複数のタスクをジョブに送信すると、Batch サービスによってノードで実行する各タスクがスケジュールされます。 ただし、代わりにタスクの **マルチインスタンス設定** を構成することで、Batch で 1 つのプライマリ タスクといくつかのサブタスクを作成し、それらを複数のノード上で実行することが可能になります。

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="マルチインスタンス設定の概要を示す図。":::

マルチインスタンス設定が構成されているタスクをジョブに送信すると、Batch によってマルチインスタンス タスク固有の次の手順が実行されます。

1. Batch サービスは、マルチインスタンス設定に基づき、1 つの **プライマリ タスク** といくつかの **サブタスク** を作成します。 タスクの合計数 (プライマリ タスクとすべてのサブタスクの合計) は、マルチインスタンス設定に指定した **インスタンス** の数 (コンピューティングノード) の数と一致します。
2. Batch は、いずれかのコンピューティング ノードを **マスター** ノードと指定し、マスター ノード上でプライマリ タスクを実行するようにスケジュールします。 サブタスクは、マルチインスタンス タスクに割り当てられたコンピューティング ノードのマスター以外のノード上で、ノードごとに 1 つのサブタスクが実行されるようにスケジュールされます。
3. プライマリ タスクとすべてのサブタスクが、マルチインスタンス設定に指定された **共通リソース ファイル** をダウンロードします。
4. 共通リソース ファイルをダウンロードした後、マルチインスタンス設定に指定された **調整コマンド** が、プライマリ タスクとサブタスクで実行されます。 調整コマンドは、通常は、タスクを実行できるようにノードを準備するために使用されます。 これには、バックグラウンド サービス ([Microsoft MPI](/message-passing-interface/microsoft-mpi) の `smpd.exe` など) の開始や、ノードがノード間メッセージを処理する準備ができていることの検証などが含まれます。
5. プライマリ タスクとすべてのサブタスクで調整コマンドが "*正常に完了した後*"、プライマリ タスクがマスター ノード上で **アプリケーション コマンド** を実行します。 アプリケーション コマンド (マルチインスタンス タスクに指定したコマンド ライン) は、プライマリ タスクでのみ実行されます。 [MS-MPI](/message-passing-interface/microsoft-mpi) ベースのソリューションでは、ここで `mpiexec.exe` を使用して MPI 対応アプリケーションを実行します。

> [!NOTE]
> "マルチインスタンス タスク" は機能的には異なりますが、[StartTask](/dotnet/api/microsoft.azure.batch.starttask) や [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) のような一意のタスクの種類ではありません。 マルチインスタンス タスクは、マルチインスタンス設定が構成されている標準の Batch タスク (Batch .NET の [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)) です。 この記事では、これを **マルチインスタンス タスク** と呼んでいます。

## <a name="requirements-for-multi-instance-tasks"></a>マルチインスタンス タスクの要件

マルチインスタンス タスクには、**ノード間通信が有効** であり、**同時実行タスクの実行が無効になっている** プールが必要です。 同時実行タスクの実行を無効にするには、[CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) プロパティを 1 に設定します。

> [!NOTE]
> Batch は、ノード間通信が有効になっているプールのサイズを[制限](batch-quota-limit.md#pool-size-limits)します。

このコード スニペットでは、Batch .NET ライブラリを使用してマルチインスタンス タスクのプールを作成する方法を示します。

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
        imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
        nodeAgentSkuId: "batch.node.windows amd64");

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> ノード間通信が無効になっている ( *taskSlotsPerNode* 値が 1 より大きい) プールでマルチインスタンス タスクを実行しようとしても、タスクはスケジュールされず、いつまでも "アクティブ" 状態のままになります。

### <a name="use-a-starttask-to-install-mpi"></a>StartTask を使用した MPI のインストール

MPI アプリケーションをマルチインスタンス タスクで実行するには、まず、プール内のコンピューティング ノードに MPI 実装 (例: MS-MPI または Intel MPI) をインストールする必要があります。 [StartTask](/dotnet/api/microsoft.azure.batch.starttask) を使用するのに適した機会です。StartTask は、ノードがプールに参加するたびに実行され、ノードの再起動時にも実行されます。 次のコード スニペットでは、[リソース ファイル](/dotnet/api/microsoft.azure.batch.resourcefile)として MS-MPI セットアップ パッケージを指定する StartTask を作成しています。 このスタート タスクのコマンド ラインは、リソース ファイルがノードにダウンロードされた後で実行されます。 この場合、コマンド ラインにより MS-MPI の無人インストールが実行されます。

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>リモート ダイレクト メモリ アクセス (RDMA)

Batch プールのコンピューティング ノードのサイズとして [RDMA 対応サイズ](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) (A9 など) を選択した場合、MPI アプリケーションは Azure の高パフォーマンスで待機時間の短いリモート ダイレクト メモリ アクセス (RDMA) ネットワークを利用できます。

「[Azure の仮想マシンのサイズ](../virtual-machines/sizes.md)」(VirtualMachineConfiguration プールの場合) または「[Cloud Services のサイズ](../cloud-services/cloud-services-sizes-specs.md)」(CloudServicesConfiguration プールの場合) で "RDMA 対応" と記載されているサイズを探します。

> [!NOTE]
> [Linux のコンピューティング ノード](batch-linux-nodes.md)で RDMA を利用するには、ノード上で **Intel MPI** を使用する必要があります。

## <a name="create-a-multi-instance-task-with-batch-net"></a>Batch .NET を使用したマルチインスタンス タスクの作成

プールの要件と MPI パッケージのインストールについて説明しました。次に、マルチインスタンス タスクを作成します。 次のスニペットでは、標準の [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) を作成し、[MultiInstanceSettings](/dotnet/api/microsoft.azure.batch.cloudtask) プロパティを構成しています。 前述のように、マルチインスタンス タスクはタスクの種類ではなく、マルチインスタンス設定で構成された標準の Batch タスクです。

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

## <a name="primary-task-and-subtasks"></a>プライマリ タスクとサブタスク

タスクのマルチインスタンス設定を作成するときは、タスクを実行するコンピューティング ノードの数を指定します。 タスクをジョブに送信すると、Batch サービスによって、1 つの **プライマリ** タスクと、指定したノードの数に対応する十分な数の **サブタスク** が作成されます。

これらのタスクには、0 から *numberOfInstances* - 1 の範囲の整数 ID が割り当てられます。 ID が 0 のタスクがプライマリ タスクで、他のすべての ID がサブタスクです。 たとえば、タスクに対して次のマルチインスタンス設定を作成した場合、プライマリ タスクの ID は 0、サブタスクの ID は 1 から 9 になります。

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>マスター ノード

マルチインスタンス タスクを送信すると、Batch サービスによって、いずれかのコンピューティング ノードがマスター ノードと指定され、マスター ノード上でプライマリ タスクを実行するようにスケジュールされます。 サブタスクは、マルチインスタンス タスクに割り当てられたマスター以外のノード上で実行されるようにスケジュールされます。

## <a name="coordination-command"></a>調整コマンド

**調整コマンド** は、プライマリ タスクとサブタスクの両方で実行されます。

調整コマンドの呼び出しはブロックされています。すべてのサブタスクで調整コマンドから正常に制御が返されるまで、アプリケーション コマンドは実行されません。 そのため、調整コマンドは必要なバックグラウンド サービスを開始し、それらのサービスの使用準備ができていることを確認してから終了する必要があります。 たとえば、MS-MPI バージョン 7 を使用するソリューションの次の調整コマンドは、ノードで SMPD サービスを開始してから終了します。

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

この調整コマンドでは、 `start` を使用していることに注意してください。 `smpd.exe` アプリケーションは実行後すぐに制御を返さないため、これが必須となります。 start コマンドを使用していない場合、この調整コマンドは制御を返さないため、アプリケーション コマンドを実行できません。

## <a name="application-command"></a>アプリケーション コマンド

プライマリ タスクとすべてのサブタスクで調整コマンドの実行が完了すると、マルチインスタンス タスクのコマンド ラインがプライマリ タスクで " *のみ*" 実行されます。 調整コマンドと区別するために、これを **アプリケーション コマンド** と呼びます。

MS-MPI アプリケーションでは、アプリケーション コマンドを使用して、 `mpiexec.exe`で MPI 対応のアプリケーションを実行します。 たとえば、MS-MPI バージョン 7 を使用するソリューションのアプリケーション コマンドは、次のようになります。

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> MS-MPI の `mpiexec.exe` では既定で `CCP_NODES` 変数が使用されるため (「[環境変数](#environment-variables)」を参照)、上記のアプリケーション コマンド ラインの例では、これが除外されています。

## <a name="environment-variables"></a>環境変数

Batch は、マルチインスタンス タスク固有の複数の[環境変数](batch-compute-node-environment-variables.md) を、マルチインスタンス タスクに割り当てられたコンピューティング ノード上に作成します。 調整コマンド ラインとアプリケーション コマンド ラインは、これらの環境変数、スクリプト、および実行するプログラムを参照できます。

Batch サービスによって、マルチインスタンス タスクで使用される次の環境変数が作成されます。

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

これらの変数とその他の Batch コンピューティング ノードの環境変数のコンテンツと可視性を含む詳細については、[コンピューティング ノードの環境変数](batch-compute-node-environment-variables.md)に関する記事をご覧ください。

> [!TIP]
> [Batch Linux MPI コード サンプル](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi)には、これらの環境変数のいくつかを使用する方法の例が含まれています。

## <a name="resource-files"></a>リソース ファイル

マルチインスタンス タスクには、考慮すべきリソース ファイルのセットが 2 つあります。"*すべてのタスク*" (プライマリ タスクとサブタスクの両方) でダウンロードされる **共通リソース ファイル** と、マルチインスタンス タスク自体に指定され、"*プライマリ タスクでのみ*" ダウンロードされる **リソース ファイル** です。

タスクのマルチインスタンス設定で 1 つ以上の **共通リソース ファイル** を指定できます。 これらの共通リソース ファイルは、プライマリ タスクとすべてのサブタスクで、[Azure Storage](../storage/common/storage-introduction.md) から各ノードの **タスク共有ディレクトリ** にダウンロードされます。 タスク共有ディレクトリには、 `AZ_BATCH_TASK_SHARED_DIR` 環境変数を使用して、アプリケーション コマンド ラインと調整コマンド ラインからアクセスできます。 `AZ_BATCH_TASK_SHARED_DIR` パスは、マルチインスタンス タスクに割り当てられたすべてのノードで同じであり、そのため、プライマリ タスクとすべてのサブタスクで 1 つの調整コマンドを共有できます。 Batch は、リモート アクセスという意味ではディレクトリを "共有" しませんが、環境変数に関するヒントとして示したように、マウントまたは共有ポイントとして使用できます。

マルチインスタンス タスク自体に指定したリソース ファイルは、既定では、タスクの作業ディレクトリである `AZ_BATCH_TASK_WORKING_DIR` にダウンロードされます。 既に説明したように、共通リソース ファイルとは異なり、マルチインスタンス タスク自体に指定したリソース ファイルは、プライマリ タスクのみがダウンロードします。

> [!IMPORTANT]
> コマンド ラインでこれらのディレクトリを参照するときは、環境変数 `AZ_BATCH_TASK_SHARED_DIR` と `AZ_BATCH_TASK_WORKING_DIR` を常に使用します。 パスを手動で作成しないでください。

## <a name="task-lifetime"></a>タスクの有効期間

プライマリ タスクの有効期間によって、マルチインスタンス タスク全体の有効期間が制御されます。 プライマリ タスクが終了すると、すべてのサブタスクが終了します。 プライマリ タスクの終了コードがタスクの終了コードになるため、再試行のためにタスクが成功したかどうかを判断する際に使用されます。

サブタスクのいずれかが失敗した場合 (ゼロ以外のリターン コードでの終了など)、マルチインスタンス タスク全体が失敗します。 この場合、マルチインスタンス タスクが終了し、再試行制限回数に達するまで再試行されます。

マルチインスタンス タスクを削除すると、Batch サービスによってプライマリ タスクとすべてのサブタスクも削除されます。 標準タスクの場合と同様に、サブタスクのすべてのディレクトリとファイルがコンピューティング ノードから削除されます。

マルチインスタンス タスクの [TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) ([MaxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)、[MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)、[RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) の各プロパティなど) は、標準タスクの場合と同様に優先され、プライマリ タスクとすべてのサブタスクに適用されます。 ただし、マルチインスタンス タスクをジョブに追加した後に、RetentionTime プロパティを変更すると、この変更はプライマリ タスクにのみ適用され、すべてのサブタスクは引き続き元の RetentionTime を使用します。

最近のタスクがマルチインスタンス タスクの一部だった場合、計算ノードの最近のタスク リストにそのサブタスクの ID が示されます。

## <a name="obtain-information-about-subtasks"></a>サブタスクに関する情報の取得

Batch .NET ライブラリを使用してサブタスクの情報を取得するには、[CloudTask.ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) メソッドを呼び出します。 このメソッドは、すべてのサブタスクの情報と、それらのタスクが実行されたコンピューティング ノードの情報を返します。 この情報から、各サブタスクのルート ディレクトリ、プール ID、現在の状態、終了コードなどを確認できます。 この情報を [PoolOperations.GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) メソッドと共に使用して、サブタスクのファイルを取得できます。 このメソッドは、プライマリ タスク (ID 0) の情報を返さないことに注意してください。

> [!NOTE]
> 特に記載のない限り、[CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) マルチインスタンス自体に影響する Batch .NET のメソッドは、プライマリ タスクに "*のみ*" 適用されます。 たとえば、マルチインスタンス タスクで [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) メソッドを呼び出すと、プライマリ タスクのファイルだけが返されます。

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

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
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

## <a name="code-sample"></a>コード サンプル

GitHub の [MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) コード サンプルでは、マルチインスタンス タスクを使用して Batch 計算ノード上で [MS-MPI](/message-passing-interface/microsoft-mpi) アプリケーションを実行する方法を紹介しています。 下の手順に従って、このサンプルを実行します。

### <a name="preparation"></a>準備

1. [MS MPI SDK と Redist インストーラー](/message-passing-interface/microsoft-mpi)をダウンロードしてインストールします。 インストール後に、MS MPI 環境変数が設定されていることを確認できます。
1. [MPIHelloWorld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) サンプル MPI プログラムの "*リリース*" バージョンをビルドします。 このプログラムは、マルチインスタンス タスクによりコンピューティング ノードで実行されます。
1. `MPIHelloWorld.exe` (手順 2 でビルドしたもの) と `MSMpiSetup.exe` (手順 1 でダウンロードしたもの) を含む zip ファイルを作成します。 この zip ファイルは、次の手順でアプリケーション パッケージとしてアップロードします。
1. [Azure Portal](https://portal.azure.com) を使用して "MPIHelloWorld" という名前の Batch [アプリケーション](batch-application-packages.md)を作成し、前の手順で作成した zip ファイルをアプリケーション パッケージのバージョン "1.0" として指定します。 詳細については、「[アプリケーションのアップロードと管理](batch-application-packages.md#upload-and-manage-applications)」を参照してください。

> [!TIP]
> `MPIHelloWorld.exe` の "*リリース*" バージョンをビルドすると、アプリケーション パッケージに追加の依存関係 (`msvcp140d.dll` や `vcruntime140d.dll` など) を含める必要がなくなります。

### <a name="execution"></a>実行

1. GitHub から [azure-batch-samples .zip ファイル](https://github.com/Azure/azure-batch-samples/archive/master.zip)をダウンロードします。
1. Visual Studio 2019 で MultiInstanceTasks **ソリューション** を開きます。 `MultiInstanceTasks.sln` ソリューション ファイルの保存先は次のとおりです。

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. **Microsoft.Azure.Batch.Samples.Common** プロジェクトの `AccountSettings.settings` に、Batch と Storage のアカウント資格情報を入力します。
1. MultiInstanceTasks ソリューションを **ビルドおよび実行** して、Batch プール内のコンピューティング ノード上で MPI サンプル アプリケーションを実行します。
1. *省略可能*:[Azure portal](https://portal.azure.com) または [Batch Explorer](https://azure.github.io/BatchExplorer/) を使用して、リソースを削除する前に、サンプル プール、ジョブ、タスク ("MultiInstanceSamplePool"、"MultiInstanceSampleJob"、"MultiInstanceSampleTask") を確認します。

> [!TIP]
> まだ Visual Studio がない場合は、[Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) を無料でダウンロードできます。

`MultiInstanceTasks.exe` からの出力は、次の例のようになります。

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>次のステップ

- [Azure Batch での Linux 向けの MPI サポート](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch)の詳細を確認します。
- [Linux コンピューティング ノードのプールを作成](batch-linux-nodes.md)して Azure Batch MPI ソリューションで使用する方法を確認します。
