<properties
	pageTitle="Batch でのジョブの準備とクリーンアップ | Microsoft Azure"
	description="Azure Batch コンピューティング ノード間のデータ転送を最小にするにはジョブ準備タスクを使用し、ジョブの完了時にノードをクリーンアップするにはジョブ解放タスクを使用します。"
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
	ms.date="06/22/2016"
	ms.author="marsma" />

# Azure Batch コンピューティング ノードでのジョブ準備タスクとジョブ完了タスクの実行

Azure Batch ジョブでは、実行前に何らかのセットアップが必要になることがよくあります。同様に、ジョブのタスクが完了した後に何らかのジョブ実行後のメンテナンスが必要になることもよくあります。Batch では、この準備と保守のメカニズムが、オプションのジョブ準備タスクおよびジョブ解放タスクの形式で提供されています。

ジョブのタスクが実行される前には、タスクの実行がスケジュールされているすべてのコンピューティング ノードにおいて**ジョブ準備タスク**が実行されます。ジョブが完了すると、少なくとも 1 つのタスクを実行したプールの各ノードで**ジョブ解放タスク**が実行されます。通常の Batch タスクと同様に、ジョブ準備タスクまたはジョブ解放タスクのコマンド ラインを指定して、タスクが実行されるときに呼び出すことができます。これらの特殊なタスクでは、ファイルのダウンロード、管理者特権での実行、カスタム環境変数、最大実行時間、再試行回数、ファイルのリテンション期間などの使い慣れた機能を利用できます。

以降のセクションでは、[Batch .NET][api_net] API の [JobPreparationTask][net_job_prep] クラスと [JobReleaseTask][net_job_release] クラスを使用して、これら 2 つの特別な種類のタスクを使用する方法について説明します。

> [AZURE.TIP] ジョブ準備および解放タスクは、"共有プール" 環境で特に役に立ちます。共有プール環境では、コンピューティング ノードのプールが異なるジョブ実行の間も維持され、多くの異なるジョブの間で共有されます。

## ジョブ準備タスクおよびジョブ解放タスクを使用するのに適した状況

ジョブ準備タスクとジョブ解放タスクは、ジョブ固有の構成やデータを使ってノードを準備するときや、タスクの結果データを除去 (または保存) するときに使用します。以下、このような状況の例を示します。

**一般的なタスク データの転送**

Batch ジョブでは、ジョブのタスクに対する入力として共通のデータ セットが必要になることがよくあります。たとえば、毎日のリスク分析の計算では、市場データはジョブ固有ですが、そのジョブのすべてのタスクに共通です。このような市場データはサイズが数ギガバイトになることも多く、各コンピューティング ノードに 1 回だけダウンロードして、そのノードで実行する各タスクがそれを使用できるようにする必要があります。**ジョブ準備タスク**を使用して、ジョブの他のタスクの実行前に各ノードにデータをダウンロードします。

**ジョブ データの削除**

ジョブとジョブの間でプールのコンピューティング ノードが使用停止されない共有プール環境では、ノードのディスク領域を節約したり、組織のセキュリティ ポリシーを満たしたりするために、実行の間にジョブ データの削除が必要になる場合があります。ジョブ準備タスクによってダウンロードされたデータ、またはタスクの実行中に生成されたデータを削除するには、**ジョブ解放タスク**を使用します。

**ログのリテンション期間**

タスクによって生成されるログ ファイルのコピーや、障害が発生したアプリケーションによって生成される可能性があるクラッシュ ダンプ ファイルの保持が必要な場合があります。そのような場合は、**ジョブ解放タスク**を使用してこのデータを圧縮し、[Azure ストレージ][azure_storage] アカウントにアップロードします。

## ジョブ準備タスク

ジョブ準備タスクは、ジョブのタスクを実行する前に、タスクの実行がスケジュールされている各コンピューティング ノードで実行されます。既定では、Batch サービスはジョブ準備タスクが完了するまで待機してから、ノードでスケジュールされているタスクを実行します。ただし、完了を待たないようにサービスを構成することもできます。コンピューティング ノードが再起動するとジョブ準備タスクも再度実行されますが、この動作もやはり無効にできます。

ジョブ準備タスクは、タスクを実行するようにスケジュールされているノードでのみ実行されます。これにより、ノードにタスクが割り当てられていない場合は、準備タスクが無駄に実行されることがなくなります。これは、ジョブのタスク数がプール内のノード数より少ない場合に発生する可能性があります。また、[同時実行タスクの実行](batch-parallel-node-tasks.md)が有効になっている場合も当てはまります。この場合、タスク数が同時実行可能なタスクの総数より少ないと、一部のノードがアイドル状態のままになります。アイドル状態のノードでジョブ準備タスクを実行しないことで、データ転送の費用を削減できます。

> [AZURE.NOTE] [JobPreparationTask][net\_job\_prep\_cloudjob] と [CloudPool.StartTask][pool_starttask] は異なります。JobPreparationTask が各ジョブの開始時に実行されるのに対し、StartTask はコンピューティング ノードがプールに初めて追加されたときまたは再起動するときにのみ実行されます。

## ジョブ解放タスク

ジョブが完了とマークされると、少なくとも 1 つのタスクを実行したプールの各ノードでジョブ解放タスクが実行されます。ジョブを完了済みとして指定するには、終了要求を発行します。それに対し、Batch サービスはジョブの状態を*終了中*に設定し、ジョブに関連付けられているアクティブなタスクまたは実行中のタスクを終了してから、ジョブ解放タスクを実行します。その後、ジョブは*完了*状態に移行します。

> [AZURE.NOTE] ジョブを削除した場合もジョブ解放タスクが実行されます。ただし、ジョブが既に終了している場合は、その後でジョブを削除しても解放タスクが再度実行されることはありません。

## Batch .NET でのジョブ準備タスクとジョブ解放タスク

ジョブ準備タスクを使用するには、[JobPreparationTask][net_job_prep] オブジェクトを作成して構成し、それをジョブの [CloudJob.JobPreparationTask][net_job_prep_cloudjob] プロパティに割り当てます。同様に、ジョブ解放タスクを設定するには、[JobReleaseTask][net_job_release] を初期化し、それをジョブの [CloudJob.JobReleaseTask][net_job_prep_cloudjob] プロパティに割り当てます。

次のコード例では、`myBatchClient` は完全に初期化された [BatchClient][net_batch_client] のインスタンスであり、`myPool` は Batch アカウント内の既存のプールです。

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

前述のように、解放タスクはジョブが終了または削除されたときに実行されます。Batch .NET API でジョブを終了するには、[JobOperations.TerminateJobAsync][net_job_terminate] を呼び出します。ジョブを削除するには、[JobOperations.DeleteJobAsync][net_job_delete] を呼び出します。通常、どちらの操作も、ジョブのタスクが完了したときか、定義したタイムアウトに達したときに実行されます。

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## GitHub 上のサンプル コード

動作するジョブ準備タスクおよびジョブ解放タスクは、GitHub の [JobPrepRelease][job_prep_release_sample] サンプル プロジェクトで確認できます。このコンソール アプリケーションは次のことを行います。

1. 2 つの "小さい" ノードを含むプールを作成します。
2. ジョブ準備タスク、ジョブ解放タスク、標準タスクを含むジョブを作成します。
3. ノードの "共有" ディレクトリ内のテキスト ファイルに最初にノード ID を書き込むジョブ準備タスクを実行します。
4. 同じテキスト ファイルにタスク ID を書き込むタスクを各ノードで実行します。
5. すべてのタスクが完了したら (またはタイムアウトに達したら)、各ノードのテキスト ファイルの内容をコンソールに出力します。
6. ジョブが完了したら、ジョブ解放タスクを実行してノードからファイルを削除します。
6. タスクを実行した各ノードのジョブ準備タスクとジョブ解放タスクの終了コードを出力します。
7. ジョブまたはプールの削除を確認できるように実行を一時停止します。

サンプル アプリケーションからの出力は次のようになります。

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] 新しいプールにノードが作成されて起動されるタイミングは不定である (一部のノードだけ他のノードよりも早く、タスクを処理できる状態になることがある) ため、出力結果が異なる場合があります。たとえばタスクの実行には時間がかからないので、ジョブのすべてのタスクがプール内のいずれか 1 つのノードによって実行される可能性もあります。このとき、タスクを実行しなかったノードについては、ジョブの準備タスクとジョブの解放タスクが存在しません。

### Azure ポータルでジョブ準備タスクとジョブ解放タスクを調査する

上のサンプル アプリケーションを実行するときに [Azure ポータル][portal]を使用して、ジョブとそのタスクのプロパティを表示したり、ジョブのタスクによって変更された共有テキスト ファイルをダウンロードしたりできます。

以下のスクリーンショットは、サンプル アプリケーションを実行した後の Azure ポータルの **[ノード準備タスク]** ブレードです。タスクの完了後 (なおかつジョブとプールが削除される前に) *JobPrepReleaseSampleJob* のプロパティに移動し、**[準備タスク]** または **[解放タスク]** をクリックしてそれらのプロパティを表示します。

![Job preparation properties in Azure portal][1]

## 次のステップ

### アプリケーション パッケージ

タスクの実行に使用するコンピューティング ノードは、ジョブ準備タスクのほか、Batch の[アプリケーション パッケージ](batch-application-packages.md)機能を使用して準備することもできます。この機能は特に、インストーラーの実行を必要としないアプリケーションや、多数 (100 個超) のファイルを含んだアプリケーション、厳密なバージョン管理が要求されるアプリケーションをデプロイする場合に利便性を発揮します。

### アプリケーションとステージング データのインストール

Azure Batch フォーラムの「[Installing applications and staging data on Batch compute nodes (Batch コンピューティング ノードへのアプリケーションとステージング データのインストール)][forum_post]」という投稿に、タスクの実行に使用するノードを準備するさまざまな方法が簡単に説明されています。この投稿記事は、Azure Batch チームのメンバーによって書かれたものです。コンピューティング ノードにファイル (アプリケーションとタスクの入力データを含む) を展開する各種の方法や、その方法ごとの特別な考慮事項がわかりやすく解説されています。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/ja-JP/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

<!---HONumber=AcomDC_0629_2016-->