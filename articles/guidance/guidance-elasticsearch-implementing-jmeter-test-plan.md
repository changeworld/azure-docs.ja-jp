<properties
   pageTitle="Elasticsearch 用の JMeter テスト計画を実装する | Microsoft Azure"
   description="JMeter で Elasticsearch のパフォーマンス テストを実行する方法。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Elasticsearch 用の JMeter テスト計画を実装する

これは[一連の記事の一部](guidance-elasticsearch.md)です。

Elasticsearch に対して実施されるパフォーマンス テストは、JMeter テスト計画を、クラスターにデータをアップロードするなどのタスクを実行する JUnit テストとして組み込まれる Java コードと共に使用することで実装されました。テスト計画と JUnit コードは、「[Azure での Elasticsearch のデータ インジェスト パフォーマンスのチューニング][]」と「[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの集計とクエリのパフォーマンスを調整する)][]」で説明されています。

このドキュメントの目的は、これらのテスト計画の作成と実行から得られる主要なエクスペリエンスを要約することです。Apache JMeter Web サイトの [JMeter Best Practices (JMeter ベスト プラクティス)](http://jmeter.apache.org/usermanual/best-practices.html) ページに、JMeter を効果的に使用するための一般的なアドバイスが記載されています。

## JMeter テスト計画の実装

JMeter テスト計画を作成するときに考慮する必要がある項目を次に示します。

- 実行するテストごとに個別のスレッド グループを作成します。1 つのテストには、ロジック コントローラー、タイマー、プリプロセッサ、ポストプロセッサ、サンプラー、およびリスナーで構成される複数のステップを含めることができます。

- スレッド グループ内のスレッドが多すぎないようにします。スレッドの数が多すぎると、JMeter が「メモリ不足」例外で失敗します。単一の JMeter サーバーで多数のスレッドを実行するのではなく、それぞれが少数のスレッドを実行する従属サーバーを追加することをお勧めします。

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- クラスターのパフォーマンスを評価するために、テスト計画に[パフォーマンス メトリック コレクター](http://jmeter-plugins.org/wiki/PerfMon/) プラグインを組み込みます。これは、JMeter の標準プラグインの 1 つとして利用できる JMeter リスナーです。未加工のパフォーマンス データを CSV 形式でファイルに保存し、テストの完了後に処理します。そのほうが、データがキャプチャされたときに処理するよりも効率的であり、JMeter に与える負荷が軽くなります。 

Excel などのツールを使用してデータをインポートし、分析するためのグラフを生成できます。

![](./media/guidance-elasticsearch/jmeter-testing2.png)

次の情報をキャプチャすることを考慮する必要があります。

- Elasticsearch クラスター内のすべてのノードの CPU 使用率。

- すべてのノードのディスクから読み取られた 1 秒あたりのバイト数。

- 可能であれば、各ノードで I/O が実行されるまで待機した CPU 時間の割合。これは Windows VM では可能でない場合がありますが、Linux では、次のシェル コマンドを実行してノード上で *vmstat* を呼び出すカスタム メトリック (EXEC メトリック) を作成できます。

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

*vmstat* からの出力のフィールド 16 に、I/O を待機するために費やされた CPU 時間が含まれます。このステートメントの動作方法の詳細については、「[vmstat コマンド](http://linuxcommand.org/man_pages/vmstat8.html)」を参照してください。

- 各ノードへのネットワークで送受信されたバイト数。

- 個別の集計レポート リスナーを使用して、パフォーマンスと、成功した操作と失敗した操作の回数を記録します。成功データと失敗データを別々のファイルにキャプチャします。

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- 各 JMeter テスト ケースをできるだけシンプルにして、パフォーマンスを特定のテスト操作に直接関連付けできるようにします。複雑なロジックを必要とするテスト ケースでは、そのロジックを JUnit テスト内にカプセル化し、JMeter の JUnit 要求サンプラーを使用してテストを実行することを検討します。

- HTTP 要求サンプルを使用して、GET、POST、PUT、DELETE などの HTTP 操作を実行します。たとえば、POST クエリを使用し、*[ボディ データ]* ボックスにクエリの詳細を指定することで、Elasticsearch 検索を実行できます。

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- 反復と再利用を容易にするために、JMeter テスト計画をパラメーター化します。その後、スクリプトを使用してテスト計画の実行を自動化できます。

## JUnit テストの実装

1 つまたは複数の JUnit テストを作成することで、複雑なコードを JMeter テスト計画に組み込むことができます。JUnit テストは、Eclipse などの Java IDE を使用して記述できます。「[Elasticsearch のパフォーマンスをテストするための JMeter JUnit サンプラーをデプロイする][]」に、適切な開発環境をセットアップする方法に関する情報が記載されています。

JUnit テスト用のコードを記述するときに従う必要があるいくつかのベスト プラクティスを次に示します。

- テスト クラス コンストラクターを使用して、初期化パラメーターをテストに渡します。JMeter では、1 つの文字列引数を受け取るコンストラクターを使用できます。コンストラクター内で、引数を個々 の要素に解析します。これを次のコード例に示します。

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/* JUnit test class constructor */
public ElasticsearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
	String[] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- コンストラクターまたはセットアップ テスト クラス内では負荷がかかる操作や I/O 操作を実行しないようにします。これらは JUnit テストを実行するたびに実行されるためです (JMeter から実行されるパフォーマンス テストごとに同じ JUnit テストが何千回も実行される可能性があります)。

- 負荷がかかるテスト ケースの初期化では、1回限りのセットアップの使用を検討します。

- テストで大量の入力パラメーターが必要な場合は、テスト構成情報を別の構成ファイルに保存し、このファイルの場所をコンストラクターに渡します。

- ロード テスト コード内にファイルのパスをハード コーディングしないようにします。Windows や Linux などのオペレーティング システムの違いによるエラーを発生させる可能性があります。

- アサーションを使用して JUnit テスト メソッド内のエラーを示して、それらを JMeter で追跡し、ビジネス指標として使用できるようにします。可能であれば、エラーの原因に関する情報を戻します。これを次のコード例に太字で示します。

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Azure での Elasticsearch のデータ インジェスト パフォーマンスのチューニング]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Elasticsearch のパフォーマンスをテストするための JMeter JUnit サンプラーをデプロイする]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの集計とクエリのパフォーマンスを調整する)]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md

<!---HONumber=AcomDC_0224_2016-->