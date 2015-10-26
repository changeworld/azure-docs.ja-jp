<properties
	pageTitle="Azure Batch での効率的なリスト クエリ | Microsoft Azure"
	description="Azure Batch のプール、ジョブ、タスク、コンピューティング ノードなどのエンティティを問い合わせた場合に返されるデータ量を減らすことで、パフォーマンスを向上させます。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/12/2015"
	ms.author="v-marsma"/>

# 効率的な Azure Batch サービスのクエリ

この記事では、[Batch .NET][api_net] API を使用して、ジョブ、タスク、コンピューティング ノードなどのリストについて、Batch サービスに問い合わせた場合に返される項目数とデータ量を減らす方法を学習します。

Azure Batch は大規模なコンピューティングです。運用環境では、ジョブ、タスク、コンピューティング ノードのようなエンティティは数千単位になることがあります。そのため、そのようなアイテムに関する情報を取得するとき、クエリごとに大量のデータを転送しなければならないことがあります。クエリごとに返される情報の項目数と種類を制限すると、クエリの時間が短縮され、アプリケーションのパフォーマンスが改善されます。

ジョブ、タスク、コンピューティング ノードのリストは、Azure Batch を使用するほぼすべてのアプリケーションが多くの場合、頻繁に実行する必要がある操作の例です。監視が一般的な使用例です。たとえば、プールの容量と状態を判断するには、クエリを実行するそのプールのすべてのコンピューティング ノードが必要になります。また、ジョブのいずれかのタスクがまだキューにあるかどうかを判断するために、それらのタスクを照会する場合もあります。

以下の [Batch .NET][api_net] API のコード スニペットでは、これらのタスクの一連のプロパティと共に、ジョブに関連付けられているタスクをすべて取得します。

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

ただし、[JobOperations.ListTasks][net_list_tasks] メソッドに [ODATADetailLevel][odata] を指定することで、より効率的なリスト クエリを実行できます。以下のスニペットでは、完了したタスクのみの ID、コマンド ライン、コンピューティング ノード情報のプロパティだけを返します。

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

上記の例のシナリオでジョブに何千ものタスクがある場合、通常は 2 つ目のクエリの結果は最初のものより速く返されます。Batch .NET API による項目のリスト時の ODATADetailLevel の使用の詳細については、以下に示します。

> [AZURE.IMPORTANT]アプリケーションの最大限の効率とパフォーマンスを確保するために、.NET API リスト呼び出しには**常に** ODATADetailLevel を指定することを強くお勧めします。詳細レベルの指定は、Batch サービスの応答時間の短縮、ネットワーク使用率の改善、およびクライアント アプリケーションによるメモリ使用量の最小化に役立ちます。

## 効率的なクエリのためのツール

[Batch .NET][api_net] および [Batch REST][api_rest] API では、リスト クエリの実行時に *filter*、*select*、および *expand* 文字列を指定することで、返される各情報の量だけでなく、リストに返される項目の数も減らすことができます。

- **filter** - *filter 文字列* は、返される項目の数を減らす式です。たとえば、あるジョブの実行中のタスクのみ、またはタスクの実行準備が完了しているコンピューティング ノードのみをリストします。
  - filter 文字列は、プロパティ名、演算子、値で構成される、1 つ以上の式から成ります。指定できるプロパティは、演算子が各プロパティでサポートされる場合、各 API 呼び出しの種類に固有です。
  - 論理演算子の `and` と `or` を使用して、複数の式を結合できます。
  - たとえば、実行中のレンダリング タスクのみをリストする場合、filter 文字列は `startswith(id, 'renderTask') and (state eq 'running')` となります。
- **select** - 各項目に対して返されるプロパティの値は、*select 文字列*で制限されます。項目のプロパティ リストを select 文字列に指定でき、リスト クエリの結果では、各項目に対してこれらのプロパティの値のみが返されます。
  - select 文字列は、プロパティ名のコンマ区切りリストで構成されます。リスト操作によって返される項目のすべてのプロパティを指定できます。
  - たとえば、各項目に対して 3 つのプロパティのみを返すように指定する場合、select 文字列は `id, state, stateTransitionTime` になります。
- **expand** - *expand 文字列*は、特定の情報を取得するために必要な API 呼び出しの数を減らします。各リスト項目の詳細情報は、リストを取得してから、リスト内の各項目を呼び出すのではなく、1 つのリスト API 呼び出しで取得できます。
  - select 文字列と同様に、expand 文字列はリスト クエリ結果に特定のデータを含めるかどうかを制御します。
  - expand 文字列がサポートされるのは、ジョブ、ジョブのスケジュール、タスク、およびプールをリストする場合のみであり、現在サポートされているのは統計情報のみです。
  - たとえば、各項目に対して統計情報が返されるように指定する場合、expand 文字列は `stats` のようになります。
  - すべてのプロパティが必要なときに select 文字列が指定されていない場合は、expand 文字列を使用して統計情報を取得する*必要があります*。select 文字列を使用してプロパティのサブセットを取得する場合は、`stats` を select 文字列で指定でき、expand 文字列を指定する必要はありません。

> [AZURE.NOTE]3 つの種類のクエリ文字列 (filter、select、expand) のいずれかを構築する際に、プロパティ名および大文字と小文字が REST API 要素の対応部分のものと一致することを確認する必要があります。たとえば、.NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) を使用する場合は、.NET プロパティが [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state) であっても、**State** の代わりに **state** を指定する必要があります。.NET と REST API 間のプロパティ マッピングについては、以下の表を参照してください。

### filter、select、および expand 文字列の仕様

- filter、select、および expand 文字列に指定されるプロパティは、[Batch REST][api_rest] API に表示される場合のプロパティ名に相当します。これは、[Batch .NET][api_net] ライブラリを使用する場合も同様です。
- すべてのプロパティ名は大文字と小文字が区別されますが、プロパティの値は大文字と小文字を区別しません。
- 日付/時刻文字列は、次の 2 つの形式のいずれかで、`DateTime` の前に使用する必要があります。
  - W3CDTF 形式の例: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - RFC1123 形式の例: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- ブール文字列は、`true` または `false` のいずれかです。
- 無効なプロパティまたは演算子を指定すると、`400 (Bad Request)` エラーが表示されます。

## Batch .NET の効率的クエリ

[Batch .NET][api_net] API 内で、リスト操作に filter、select、および expand 文字列を指定する場合は、[ODATADetailLevel][odata] を使用します。ODataDetailLevel オブジェクトにはコンストラクターで指定または直接設定できる 3 つのパブリック文字列プロパティがあります。このオブジェクトは、[ListPools][net_list_pools]、[ListJobs][net_list_jobs]、および [ListTasks][net_list_tasks] などのさまざまなリスト操作にパラメーターとして渡されます。

- [ODATADetailLevel.FilterClause][odata_filter] – 返される項目の数を制限します。
- [ODATADetailLevel.SelectClause][odata_select] – 各項目で返されるプロパティ値のサブセットを指定します。
- [ODATADetailLevel.ExpandClause][odata_expand] – 項目ごとに呼び出しを発行するのではなく、1 つの API 呼び出しで項目データを取得します。

以下のコード スニペットでは Batch .NET API を使用して、特定のプール セットの統計値を Batch サービスに効率的に問い合わせます。このシナリオでは、Batch ユーザーにはテスト プールと運用プールの両方が与えられています。テスト プールの ID 接頭辞は「test」であり、運用プールの ID 接頭辞は「prod」です。このスニペットで、*myBatchClient* は [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) の適切に初期化されたインスタンスです。

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// We want to pull only the "test" pools, so we limit the number of items returned by using a
	// FilterClause and specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();

> [AZURE.TIP]Select 句と Expand 句で構成された [ODATADetailLevel][odata] のインスタンスは、返されるデータの量を制限するために、[PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx) などの適切な Get メソッドに渡すこともできます。

## Batch REST から NET API へのマッピング

filter、select、および expand 文字列のプロパティ名は、REST API の対応部分の名前および大文字と小文字の両方を反映している*必要があります*。次の表では、.NET および REST API の対応部分の間のマッピングを示します。

### filter 文字列のマッピング

- **.NET リスト メソッド** - この列の各 .NET API メソッドは、[ODATADetailLevel][odata] オブジェクトをパラメーターとして受け入れます。
- **REST リスト要求** - この列の各 REST API ページには、*filter* 文字列で許可されるプロパティと操作を指定する表が含まれます。[ODATADetailLevel.FilterClause][odata_filter] 文字列を構築する際には、これらのプロパティ名と操作を使用します。

| .NET リスト メソッド | REST リスト要求 |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [アカウントの証明書を一覧表示する][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [タスクに関連付けられているファイルを一覧表示する][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [ジョブのジョブ準備とジョブ リリース タスクの状態を一覧表示する][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [アカウントのジョブを一覧表示する][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [ノードのファイルを一覧表示する][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [ジョブに関連付けられているタスクを一覧表示する][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [アカウントのジョブ スケジュールを一覧表示する][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [ジョブ スケジュールに関連付けられているジョブを一覧表示する][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [プールのコンピューティング ノードを一覧表示する][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [アカウントのプールを一覧表示する][rest_list_pools]

### select 文字列のマッピング

- **BATCH .NET の種類** - Batch .NET API の種類
- **REST API のエンティティ** - この列の各ページには、各種類の REST API プロパティ名をリストする 1 つ以上の表が含まれます。*select* 文字列を構築する際に、これらのプロパティ名が使用されます。[ODATADetailLevel.SelectClause][odata_select] 文字列を構築する際には、これらの同じプロパティ名を使用します。

| Batch .NET の種類 | REST API のエンティティ |
|---|---|
| [証明書][net_cert] | [証明書に関する情報を取得する][rest_get_cert] |
| [CloudJob][net_job] | [ジョブに関する情報を取得する][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [ジョブ スケジュールに関する情報を取得する][rest_get_schedule] |
| [ComputeNode][net_node] | [ノードに関する情報を取得する][rest_get_node] |
| [CloudPool][net_pool] | [プールに関する情報を取得する][rest_get_pool] |
| [CloudTask][net_task] | [タスクに関する情報を取得する][rest_get_task] |

### 例: filter 文字列の構築

[ODATADetailLevel.FilterClause][odata_filter] の filter 文字列を構築する場合は、「*filter 文字列のマッピング*」の上記の表を参照して、実行するリスト操作に対応する REST API のドキュメント ページを見つけます。そのページの最初の複数行の表に、フィルター可能なプロパティとサポートされている演算子が表示されます。たとえば、終了コードがゼロ以外のタスクをすべて取得する場合は、「[ジョブに関連付けられているタスクを一覧表示する][rest_list_tasks]」のこの行で、適用可能なプロパティと許可される演算子を指定します。

| プロパティ | 許可される操作 | 型 |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

したがって、終了コードがゼロ以外のタスクをすべて一覧表示するための filter 文字列は次のようになります。

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### 例: select 文字列の構築

[ODATADetailLevel.SelectClause][odata_select] を構築する場合は、「*select 文字列のマッピング*」の上記の表を参照し、一覧表示するエンティティの種類に対応する REST API ページに移動します。そのページの最初の複数行の表に、選択可能なプロパティとサポートされている演算子が表示されます。たとえば、リストの各タスクの ID とコマンド ラインのみを取得する場合は、「[タスクに関する情報を取得する][rest_get_task]」の該当する表でこれらの行を見つけます。

| プロパティ | 型 | メモ |
| :--- | :--- | :--- |
| `id` | `String` | `The id of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

リストされている各タスクの ID とコマンド ラインのみを含めるための select 文字列は次のようになります。

`id, commandLine`

## 次のステップ

GitHub の [EfficientListQueries][efficient_query_sample] サンプル プロジェクトで、効率的なリスト クエリがアプリケーションのパフォーマンスに及ぼす影響を確認します。この C# コンソール アプリケーションでは、多数のタスクを作成してジョブに追加し、さまざまな [ODATADetailLevel][odata] 仕様を使用して Batch サービスのクエリを実行します。表示される出力は次のようになります。

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

経過時間情報に示すように、プロパティと返される項目の数を制限することで、クエリの応答時間を大幅に短縮できます。このサンプル プロジェクトと他のサンプル プロジェクトは、GitHub の [azure-batch-samples][github_samples] リポジトリにあります。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

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

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

<!---HONumber=Oct15_HO3-->