<properties 
	pageTitle="効率的なリスト クエリ" 
	description="リストに返される項目数の削減と各項目で返される情報量の削減について説明します。" 
	services="batch" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-windows" 
	ms.workload="multiple"
	ms.date="05/09/2015" 
	ms.author="davidmu"/>

# 効率的なリスト クエリ

次のメソッドは、Azure Batch を使用するほぼすべてのアプリケーションで実行される操作の例です。多くの場合、頻繁に実行する必要があります。

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

監視が一般的な使用例です。たとえば、プールの容量と状態を判断するには、クエリを実行する VM のすべてのプールが必要です。別の例では、タスクがまだキューにあるかどうかを判断するために、ジョブのタスクにクエリを実行します。豊富なデータ セットが必要な場合がありますが、項目の合計数や特定の状態の項目数のみが必要な場合もあります。

重要なのは、返される項目数が増えると、項目のリストを表示するのに必要なデータ サイズも増えることを理解することです。大量の応答を返す多くの項目のクエリによって、多くの問題が発生する可能性があります。

- Batch API の応答時間は非常に遅くなります。項目数が増えると、Batch サービスで必要なクエリ時間が長くなります。大量の項目はチャンクに分割する必要があるため、1 つのリストのすべての項目を取得するために複数のサービスの API 呼び出しがクライアント ライブラリからサービスに行われる場合があります。
- Batch を呼び出すアプリケーションによる API 処理は、処理する項目数が増えるほど時間がかかります。
- 項目数または項目のサイズが増えると、より多くのメモリが Batch を呼び出すアプリケーションで使用されます。
- 項目数または項目のサイズが増えると、ネットワーク トラフィックの増加につながります。これによって転送時間が長くなり、アプリケーション アーキテクチャによっては、Batch アカウントのリージョンの外部に転送されるデータのネットワーク料金が増加する可能性があります。

Batch API は、リストで返される項目数と各項目で返される情報量の両方を減らす機能を提供します。[DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) 型のパラメーターは、リスト操作を指定できます。DetailLevel は抽象基本クラスであり、[ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) オブジェクトが実際に作成され、パラメーターとして渡される必要があります。

すべての API では、次が適用されます。

- 各プロパティ名は、オブジェクトのプロパティにマップされる文字列です。
- すべてのプロパティ名は大文字と小文字が区別されますが、プロパティの値は大文字と小文字を区別しません。
- 日付 / 時刻文字列は、次の 2 つの形式のいずれかで、DateTime の前に使用する必要があります。
	- W3CDTF (例: creationTime gt DateTime’2011-05-08T08:49:37Z’)
	- RFC1123 (例: creationTime gt DateTime’Sun, 08 May 2011 08:49:37 GMT’)
- ブール文字列は、"true" または "false" のいずれかです。
- 無効なプロパティまたは演算子が指定されている場合は、例外が "400 (無効な要求)" 内部例外で作成されます。
- Select 句と Expand 句を持つ DetailLevel パラメーターを適切な "Get" メソッドに渡すこともできます (例: IPoolManager.GetPool())。

ODataDetailLevel オブジェクトには、コンストラクターで指定するか、直接設定できる 3 つのパブリック プロパティがあります。次の 3 つのプロパティはすべて文字列です。

- [FilterClause](#filter) – フィルター処理し、潜在的に返される項目数を減らします
- [SelectClause](#select) – 返される特定のプロパティ値を指定し、項目と応答のサイズを小さくします
- [ExpandClause](#expand) -複数の呼び出しではなく 1 回の呼び出しで必要なデータをすべて返します

### <a id="filter"></a> FilterClause

返される項目数は、filter 文字列によって削減できます。1 つ以上のプロパティの値を指定して、必要な項目のみが返されることを確認できます。たとえば、アクティブな作業項目のみのリスト、実行中のジョブのタスクのみのリスト、タスクを実行する準備ができている VM のみのリストなどです。

[FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) は、プロパティ名、演算子、値で構成される、1 つ以上の式から成る文字列です。指定できるプロパティは、演算子が各プロパティでサポートされる場合、各 API 呼び出しに固有です。論理演算子 “and” と “or” を使用して、複数の式を結合できます。

たとえば、タスクを一覧するフィルターは次のようになります。

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

各項目に返されるプロパティの値は、select 文字列を使用して制限できます。項目のプロパティのリストを指定すると、そのプロパティの値のみが返されます。

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) は、プロパティ名のコンマ区切りのリストで構成される文字列です。リスト操作によって返される項目のすべてのプロパティを指定できます。

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

API 呼び出し数は、expand 文字列を使用して削減できます。各リスト項目の詳細情報は、リストを取得してから、リスト内の各項目を呼び出しすのではなく、1 つのリスト API 呼び出しで取得できます。

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) は、Select 句に似ており、Expand 句は特定のデータが結果に返されるかどうかを制御します。Expand 句は、作業項目リスト、タスク リスト、プール リスト、ジョブ リストでのみサポートされています。現在は統計情報のみをサポートしています。すべてのプロパティが必要で、select 句がない場合は、expand 句を使用して統計情報を取得する必要があります。Select 句を使用してプロパティのサブセットを取得する場合は、統計情報を select 句で指定し、expand 句を null のままにすることができます。

> [AZURE.NOTE]アプリケーションに対して最大の効率と最高のパフォーマンスを保証するために、リスト API 呼び出しには常に filter 句と select 句を使用することをお勧めします。

<!---HONumber=62-->