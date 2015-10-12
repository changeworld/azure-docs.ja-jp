<properties
	pageTitle="Azure Batch での効率的なリスト クエリ | Microsoft Azure"
	description="Azure Batch のプール、ジョブ、タスク、コンピューティング ノードなどに問い合わせるとき、返されるデータ量を減らし、パフォーマンスを上げる方法について学習します。"
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="09/24/2015"
	ms.author="davidmu;v-marsma"/>

# 効率的な Batch リスト クエリ

Azure Batch は大規模なコンピューティングです。運用環境では、ジョブ、タスク、コンピューティング ノードのようなエンティティは数千単位になることがあります。そのため、そのようなアイテムに関する情報を取得するとき、クエリごとに大量のデータを転送しなければならないことがあります。クエリごとに返される情報の項目数と種類を制限すると、クエリの時間が短縮され、アプリケーションのパフォーマンスが改善されます。

次の [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) API メソッドは、Azure Batch を利用する事実上すべてのアプリケーションが多くの場合に実行しなければならない操作の例です。

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx)

監視が一般的な使用例です。たとえば、プールの容量と状態を判断するには、クエリを実行するプールのすべてのコンピューティング ノード (VM) が必要です。別の例では、タスクがまだキューにあるかどうかを判断するために、ジョブのタスクにクエリを実行します。豊富なデータ セットが必要な場合がありますが、項目の合計数や特定の状態の一連の項目のみが必要な場合もあります。

重要なのは、返される項目数と項目を表示するために必要なデータ サイズの両方が非常に大規模になる可能性があることを理解することです。大量のアイテムについて問い合わせ、大量の応答が生成されるだけで、多くの問題が発生する可能性があります。

- Batch API の応答時間は非常に遅くなります。項目数が増えると、Batch サービスで必要なクエリ時間が長くなります。大量の項目はチャンクに分割する必要があるため、場合によっては、1 つのリストのすべての項目を取得するために複数の API 呼び出しをクライアント ライブラリからサービスに実行する必要があります。
- Batch を呼び出すアプリケーションによる API 処理は、処理する項目数が増えるほど時間がかかります。
- 項目数または項目のサイズが増えると、より多くのメモリが Batch を呼び出すアプリケーションで使用されます。
- 項目数または項目のサイズが増えると、ネットワーク トラフィックの増加につながります。これによって転送時間が長くなり、アプリケーション アーキテクチャによっては、Batch アカウントのリージョンの外部に転送されるデータのネットワーク料金が増加する可能性があります。

> [AZURE.IMPORTANT]アプリケーションの最大限の効率とパフォーマンスを確保するために、リスト API 呼び出しには常に filter 句と select 句を使用することを強くお勧めします。これらの句とその使用方法については、以下に記載されています。

すべての Batch API では、次が適用されます。

- 各プロパティ名は、オブジェクトのプロパティにマップされる文字列です。
- すべてのプロパティ名は大文字と小文字が区別されますが、プロパティの値は大文字と小文字を区別しません。
- プロパティ名と大文字と小文字の区別は Batch REST API に表示される要素と同じになります。
- 日付 / 時刻文字列は次の 2 つの形式のいずれかで DateTime の前に指定する必要があります。
	- W3CDTF (例: *creationTime gt DateTime'2011-05-08T08:49:37Z'*)
	- RFC1123 (例: *creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'*)
- ブール文字列は、"true" または "false" のいずれかです。
- 無効なプロパティまたは演算子を指定すると、「400 (無効な要求)」エラーが表示されます。

## Batch .NET の効率的クエリ

Batch .NET API では、クエリの [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) を指定することで、リストで返される項目数と各項目で返される情報量の両方を減らすことができます。DetailLevel は抽象基本クラスであり、[ODATADetailLevel][odata] オブジェクトが実際に作成され、適切なメソッドにパラメーターとして渡される必要があります。

ODataDetailLevel オブジェクトには、コンストラクターで指定するか、直接設定できる 3 つのパブリック文字列プロパティがあります。

- [FilterClause](#filter) – フィルター処理し、潜在的に返される項目数を減らします
- [SelectClause](#select) – 項目ごとに返されるプロパティ値のサブセットを指定することで、項目と応答のサイズを減らします
- [ExpandClause](#expand) -複数の呼び出しではなく 1 回の呼び出しで必要なデータをすべて返します

> [AZURE.TIP]Select 句と Expand 句で構成された DetailLevel のインスタンスは、返されるデータの量を制限するために、[PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx) などの適切な Get メソッドに渡すこともできます。

### <a id="filter"></a> FilterClause

返される項目数は、filter 文字列によって削減できます。クエリに関連する項目のみを返すには、修飾子のある 1 つまたは複数のプロパティ値を指定する必要があります。たとえば、あるジョブの実行中タスクやタスクの実行準備が完了しているコンピューティング ノードのみを一覧表示することがあります。

 [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) は、*プロパティ名*、*演算子*、*値*で構成される、1 つ以上の式から成る文字列です。指定できるプロパティは、演算子が各プロパティでサポートされる場合、各 API 呼び出しに固有です。論理演算子の **and** と **or** を使用して、複数の式を結合できます。

たとえば、次のフィルター文字列は、*displayName* が "MyTask" で始まる実行中のタスクのみを返します。

	startswith(displayName, 'MyTask') and (state eq 'Running')

下の Batch REST API の各記事には、サポートされるプロパティと一覧表示操作別のプロパティの操作についてまとめた表があります。

- [アカウントのプールを一覧表示する](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [プールのコンピューティング ノードを一覧表示する](https://msdn.microsoft.com/library/azure/dn820159.aspx)
- [アカウントのジョブを一覧表示する](https://msdn.microsoft.com/library/azure/dn820117.aspx)
- [ジョブのジョブ準備とジョブ リリース タスクの状態を一覧表示する](https://msdn.microsoft.com/library/azure/mt282170.aspx)
- [アカウントのジョブ スケジュールを一覧表示する](https://msdn.microsoft.com/library/azure/mt282174.aspx)
- [ジョブ スケジュールに関連付けられているジョブを一覧表示する](https://msdn.microsoft.com/library/azure/mt282169.aspx)
- [ジョブに関連付けられているタスクを一覧表示する](https://msdn.microsoft.com/library/azure/dn820187.aspx)
- [タスクに関連付けられているファイルを一覧表示する](https://msdn.microsoft.com/library/azure/dn820142.aspx)
- [アカウントの証明書を一覧表示する](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [ノードのファイルを一覧表示する](https://msdn.microsoft.com/library/azure/dn820151.aspx)

> [AZURE.IMPORTANT]3 つの種類の句のいずれかにプロパティを指定するとき、プロパティ名と大文字/小文字が Batch REST API 要素の対応部分のそれと一致することを確認します。たとえば、.NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) を使用するときは、.NET プロパティが [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state) であっても、**State** の代わりに **state** を指定する必要があります。**state** プロパティの適切な名前と大文字/小文字を確認するには、たとえば、Batch REST API ドキュメントの「[Get information about a task (タスクに関する情報の取得)](https://msdn.microsoft.com/library/azure/dn820133.aspx)」で要素名を確認します。

### <a id="select"></a> SelectClause

各項目に返されるプロパティの値は、select 文字列を使用して制限できます。項目のプロパティのリストを指定すると、そのプロパティの値のみが返されます。

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) は、プロパティ名のコンマ区切りのリストで構成される文字列です。一覧表示操作で返される項目に利用できるプロパティはあらゆる組み合わせで指定できます。

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

API 呼び出し数は、expand 句を使用して削減できます。各リスト項目の詳細情報は、リストを取得してからリスト内の各項目を繰り返し呼び出すのではなく、1 つの API 呼び出しで取得できます。

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) は select 句に似ており、特定のデータが結果で返されるかどうかを制御します。expand 句はジョブ リスト、タスク リスト、プール リストでのみサポートされています。現在は統計情報のみをサポートしています。すべてのプロパティが必要なときに select 句が指定されていない場合は、expand 句を使用して統計情報を取得する必要があります。select 句を使用してプロパティのサブセットを取得する場合、「統計情報」を select 句で指定し、expand 句を null のままにすることができます。

## 効率的なクエリの例

以下は Batch .NET API を使用し、特定のプール セットの統計値を Batch サービスに効率的に問い合わせるコード スニペットです。このシナリオでは、Batch ユーザーにはテスト プールと運用プールの両方が与えられています。テスト プールの ID 接頭辞は「test」であり、運用プールの ID 接頭辞は「prod」です。このスニペットで、*myBatchClient* は [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) の適切に初期化されたインスタンスです。

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

## サンプル プロジェクト

効率的なリスト クエリがアプリケーションのパフォーマンスに及ぼす影響を確認するには、GitHub の [EfficientListQueries][efficient_query_sample] サンプル プロジェクトをご覧ください。この C# コンソール アプリケーションでは、多数のタスクを作成してジョブに追加し、さまざまな [ODATADetailLevel][odata] 仕様を使用して Batch サービスのクエリを実行します。出力は次のようになります。

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

## 次のステップ

1. 自分の開発シナリオに関連する Batch API 文書をお読みになっていない場合、必ずご確認ください。
    - [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
    - [Batch .NET](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. GitHub の [Azure Batch サンプル](https://github.com/Azure/azure-batch-samples)を取得し、コードを調べます。

[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx

<!---HONumber=Oct15_HO1-->