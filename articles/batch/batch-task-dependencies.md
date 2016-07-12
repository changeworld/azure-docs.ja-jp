<properties
	pageTitle="Azure Batch におけるタスクの依存関係 | Microsoft Azure"
	description="MapReduce に見られるようなビッグ データのワークロードを Azure Batch で処理することを目的として、他のタスクの正常完了に依存するタスクを作成します。"
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
	ms.date="06/29/2016"
	ms.author="marsma" />

# Azure Batch におけるタスクの依存関係

MapReduce 形式の計算ワークロードをクラウドで処理する必要がある場合や、有向非巡回グラフ (DAG) としてタスクを表すことのできるデータ処理ジョブがある場合、または下流のタスクが上流のタスクの出力に依存するようなジョブがある場合、Azure Batch のタスク依存関係機能が解決の鍵となります。

この機能を使うと、コンピューティング ノードで他のタスクが正常完了した後にのみ実行がスケジュールされるようなタスクを作成できます。たとえば、並列実行される複数の独立したタスクを使って 3D ムービーの各フレームをレンダリングするジョブの作成が考えられます。そのジョブの最終タスク ("マージ タスク") では、すべてのフレームが正常にレンダリングされた後でのみ、そのレンダリング済みのフレームをマージしてムービーを完成させます。

作成できるタスクの依存関係は一対一や一対多に限らず、特定の範囲に依存するタスク (特定の範囲のタスク ID に該当する一連のタスクが正常に完了した場合にのみ実行されるタスク) を作成することもできます。その 3 つの基本シナリオを組み合わせれば、多対多の関係を作成することもできます。

## Batch .NET でのタスクの依存関係

この記事では、[Batch .NET][net_msdn] ライブラリを使用したタスクの依存関係の構成について説明します。まずジョブに対して[タスクの依存関係を有効](#enable-task-dependency)にする方法を説明した後、[依存関係を伴うタスクの構成](#create-dependent-tasks)方法を簡単に紹介します。最後に、Batch でサポートされる[依存関係のシナリオ](#dependency-scenarios)について取り上げます。

## タスクの依存関係を有効にする

Batch アプリケーションでタスクの依存関係を利用するにはまず、そのジョブにタスクの依存関係が使用されていることを Batch サービスに伝える必要があります。Batch .NET では、[CloudJob][net_cloudjob] の [UsesTaskDependencies][net_usestaskdependencies] プロパティを `true` に設定することによって有効にします。

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

上のコード スニペットの "batchClient" は、[BatchClient][net_batchclient] クラスのインスタンスです。

## 依存タスクの作成

他のタスクの正常完了に依存するタスクを作成するには、タスクが他のタスクに "依存" していることを Batch に伝えます。Batch .NET では、[TaskDependencies][net_taskdependencies] クラスのインスタンスを使って [CloudTask][net_cloudtask].[DependsOn][net_dependson] プロパティを構成します。

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

このコード スニペットは、"Flowers" という ID のタスクを作成しています。"Rain" と "Sun" という ID のタスクが正常に完了した後にのみ、コンピューティング ノードで実行するようスケジューリングしています。

 > [AZURE.NOTE] タスクはその状態が **completed** になり、対応する**終了コード**が `0` であるとき、正常に完了したと見なされます。Batch .NET では、[CloudTask][net_cloudtask].[State][net_taskstate] プロパティの値が `Completed` で、なおかつ CloudTask の [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] プロパティの値が `0` である状態が該当します。

## 依存関係のシナリオ

Azure Batch で利用できる基本的なタスクの依存関係には、一対一、一対多、タスク ID 範囲という 3 つのシナリオがあります。これらを組み合わせることで、第 4 のシナリオ (多対多) を実現することもできます。

 シナリオ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 例 | |
 :-------------------: | ------------------- | -------------------
 [一対一](#one-to-one) | *taskB* が *taskA* に依存 <p/> *taskA* が正常に完了するまで *taskB* は実行されないようにスケジューリングされます。 | ![Diagram: one-to-one task dependency][1]
 [一対多](#one-to-many) | *taskC* が *taskA* と *taskB* の両方に依存 <p/> *taskA* と *taskB* の両方が正常に完了するまで *taskC* は実行されないようにスケジューリングされます。 | ![Diagram: one-to-many task dependency][2]
 [タスク ID の範囲](#task-id-range) | *taskD* が一連のタスク範囲に依存 <p/> ID *1* ～ *10* のタスクが正常に完了するまで *taskD* は実行されないようにスケジューリングされます。 | ![Diagram: Task id range dependency][3]

>[AZURE.TIP] タスク C、D、E、F がそれぞれタスク A と B に依存するような**多対多**の関係を作成できます。たとえば下流の複数のタスクが上流にある複数のタスクの出力に依存するような、前処理を並列実行する状況で有効活用できます。

## 一対一

単一タスクの正常完了に依存するタスクを作成するには、[CloudTask][net_cloudtask] の [DependsOn][net_dependson] プロパティに値を設定する際、[TaskDependencies][net_taskdependencies].[OnId][net_onid] 静的メソッドに 1 つのタスク ID を渡します。

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

## 一対多

複数タスクの正常完了に依存するタスクを作成するには、[CloudTask][net_cloudtask] の [DependsOn][net_dependson] プロパティに値を設定する際、[TaskDependencies][net_taskdependencies].[OnIds][net_onids] 静的メソッドにタスク ID のコレクションを渡します。

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

## タスク ID の範囲

特定の範囲内に ID が該当する一連のタスクの正常完了に依存するタスクを作成するには、[CloudTask][net_cloudtask] の [DependsOn][net_dependson] プロパティに値を設定する際、その範囲の最初と最後のタスク ID を [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] 静的メソッドに渡します。

>[AZURE.IMPORTANT] 依存関係としてタスク ID の範囲を使用するとき、その範囲内のタスク ID には*必ず*、**整数値**の**文字列表記**を使用してください。また、依存する側のタスクが実行対象としてスケジューリングされるためには、**範囲内のすべてのタスク**が正常に完了する必要があります。

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## サンプル コード

[TaskDependencies][github_taskdependencies] サンプル プロジェクトは、GitHub にある [Azure Batch コード サンプル][github_samples]の 1 つです。この Visual Studio 2015 ソリューションは、ジョブに対してタスクの依存関係を有効にし、他のタスクに依存するタスクを作成して、それらのタスクをコンピューティング ノードのプールで実行するものです。

## 次のステップ

### アプリケーションのデプロイ

コンピューティング ノード上でタスクを通じて実行するアプリケーションのデプロイとバージョン管理は、どちらも Batch の[アプリケーション パッケージ](batch-application-packages.md)機能を使って簡単に実現できます。

### アプリケーションとステージング データのインストール

Azure Batch フォーラムの「[Installing applications and staging data on Batch compute nodes (Batch コンピューティング ノードへのアプリケーションとステージング データのインストール)][forum_post]」という投稿に、タスクの実行に使用するノードを準備するさまざまな方法が簡単に説明されています。この投稿記事は、Azure Batch チームのメンバーによって書かれたものです。コンピューティング ノードにファイル (アプリケーションとタスクの入力データを含む) を展開する各種の方法や、その方法ごとの特別な考慮事項がわかりやすく解説されています。

[forum_post]: https://social.msdn.microsoft.com/Forums/ja-JP/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: one-to-one dependency"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: one-to-many dependency"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: task id range dependency"

<!---HONumber=AcomDC_0629_2016-->