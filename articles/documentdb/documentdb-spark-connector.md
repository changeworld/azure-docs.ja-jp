---
title: "Apache Spark を Azure DocumentDB に接続する | Microsoft Docs"
description: "このチュートリアルでは、Apache Spark を Azure DocumentDB に接続して、クラウド向けに設計された Microsoft のマルチテナント グローバル分散データベース システムで分散集計やデータ サイエンスを実行できる、Azure DocumentDB 用 Spark コネクタについて説明します。"
keywords: Apache Spark
services: documentdb
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: denlee
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 3d534ed667a8d8d012fa35152ae1bdb873e9672d
ms.lasthandoff: 04/06/2017


---

# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-documentdb-connector"></a>Spark-DocumentDB コネクタによるリアルタイムのビッグ データ分析の高速化

Spark-DocumentDB コネクタを使用すると、Azure DocumentDB は Apache Spark ジョブの入力ソースまたは出力シンクとして機能することができます。 [Spark](http://spark.apache.org/) を [DocumentDB](https://azure.microsoft.com/services/documentdb/) に接続すると、DocumentDB を使用してデータをすばやく保持し、クエリを実行できるため、目まぐるしく変化するデータ サイエンスの問題を迅速に解決できます。 Spark-DocumentDB コネクタは、DocumentDB で管理されるネイティブのインデックスを効率的に使用し、IoT、データ サイエンス、分析などのシナリオで、急速に変化するグローバル分散データに対して、分析の実行時に更新可能な列やプッシュダウン述語フィルターを使用できるようにします。 

## <a name="download"></a>ダウンロード

まず、GitHub の [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark/) レポジトリから Spark-DocumentDB コネクタ (プレビュー) をダウンロードします。

## <a name="connector-components"></a>コネクタ コンポーネント

コネクタでは次のコンポーネントを使用します。

* [DocumentDB](http://documentdb.com): クラウド向けに設計された Microsoft のマルチテナント [グローバル分散データベース システム](documentdb-distribute-data-globally.md)です。 DocumentDB では、多数の地理的リージョン間でスループットとストレージをプロビジョニングし、弾力的にスケーリングできます。 このサービスは、99 パーセンタイルの低待機時間と 99.99% の高可用性を保証し、[明確に定義された複数の整合性モデル](documentdb-consistency-levels.md)を開発者に提供します。

* [Apache Spark](http://spark.apache.org/): 速度、使いやすさ、高度な分析に重点を置いて構築された強力なオープン ソース処理エンジンです。 

* [Azure HDInsight 上の Apache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md):  Apache Spark をクラウドにデプロイし、[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) を使用してミッション クリティカルなデプロイメントを実現できます。

正式にサポートされているバージョンは次のとおりです。

| コンポーネント | バージョン |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| Azure DocumentDB Java SDK | 1.9.6 |
 
この記事では、Python (pyDocumentDB を使用) と Scala インターフェイスを使用した簡単なサンプルを実行できます。

Apache Spark と Azure DocumentDB は次の 2 とおりの方法で接続できます。
- [Azure DocumentDB Python SDK](https://github.com/Azure/azure-documentdb-python) で pyDocumentDB を使用する。
- [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java) を使用して、Java ベースの Spark-DocumentDB コネクタを作成する。

## <a name="pydocumentdb-implementation"></a>pyDocumentDB 実装 
最新の [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python) を使用すると、次の図に示すように Spark を DocumentDB に接続できます。

![pyDocumentDB を介した Spark と DocumentDB 間のデータ フロー](./media/documentdb-spark-connector/azure-documentdb-spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>pyDocumentDB 実装のデータ フロー

データ フローは次のとおりです。

1. pyDocumentDB を介して、Spark マスター ノードから DocumentDB ゲートウェイ ノードへの接続が行われます。  ユーザーが指定するのは Spark と DocumentDB の接続だけであり、それぞれのマスター ノードとゲートウェイ ノードへの接続はユーザーに対して透過的に行われます。
2. (ゲートウェイ ノードを介して) DocumentDB に対するクエリが作成されます。その後、このクエリはデータ ノード内のコレクションのパーティションに対するクエリを実行します。 これらのクエリに対する応答がゲートウェイ ノードに送信され、その結果セットが Spark マスター ノードに返されます。
3. 後続のクエリ (Spark DataFrame に対するクエリなど) が Spark worker ノードに送信され、処理されます。

重要なのは、Spark と DocumentDB 間の通信が Spark マスター ノードと DocumentDB ゲートウェイ ノードに制限されることです。  クエリは、この 2 つのノード間にトランスポート層が存在する場合と同様に高速で実行されます。

### <a name="installing-pydocumentdb"></a>pyDocumentDB をインストールする
次の例のように、**pip** を使用して pyDocumentDB をドライバー ノードにインストールできます。

```
pip install pyDocumentDB
```


### <a name="connecting-spark-to-documentdb-via-pydocumentdb"></a>pyDocumentDB を介して Spark を DocumentDB に接続する 
通信転送が単純である代わりに、pyDocumentDB を使用した Spark から DocumentDB へのクエリの実行は比較的単純なものになります。

次のコード スニペットは、Spark コンテキスト内で pyDocumentDB を使用する方法を示しています。

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery 
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to DocumentDB 
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==' 
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

このコード スニペットでは次のことを示しています。

* DocumentDB Python SDK には、優先する場所 (どのような優先順位でどの読み取りレプリカを選択するか) も含め、必要な接続パラメーターがすべて含まれています。
*  必要なライブラリをインポートし、**masterKey** と **host** を構成して DocumentDB *クライアント* (**pydocumentdb.document_client**) を作成します。


### <a name="executing-spark-queries-via-pydocumentdb"></a>pyDocumentDB を介して Spark クエリを実行する
以下の例では、指定した読み取り専用キーを使用して、前のスニペットで作成した DocumentDB インスタンスを使用します。  次のコード スニペットでは、ワシントン州の空港都市を抽出するクエリを実行して、(以前に指定した DoctorWho アカウント内の) **airports.codes** コレクションに接続します。 

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the DocumentDB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

**query** によってクエリが実行されると、結果は、Python リストに変換される **query_iterable.QueryIterable** になります。 Python リストは、次のコードを使用して Spark DataFrame に簡単に変換できます。

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-documentdb"></a>pyDocumentDB を使用して Spark を DocumentDB に接続する理由
pyDocumentDB を使用して Spark を DocumentDB に接続するのは、一般に次のような場合です。

* Python を使用する場合。
* DocumentDB から比較的小規模の結果セットを Spark に返す場合。  DocumentDB 内の基になるデータセットが非常に大規模である可能性があります。 多くの場合、DocumentDB ソースに対してフィルターを適用 (述語フィルターを実行) します。  

## <a name="spark-to-documentdb-connector"></a>Spark-DocumentDB コネクタ

Spark-DocumentDB コネクタは [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java) を使用し、次の図に示すように Spark worker ノードと DocumentDB 間でデータを移動します。

![Spark-DocumentDB コネクタのデータ フロー](./media/documentdb-spark-connector/azure-documentdb-spark-connector.png)

データ フローは次のとおりです。

1. Spark マスター ノードから DocumentDB ゲートウェイ ノードへの接続が行われ、パーティション マップが取得されます。  ユーザーが指定するのは Spark と DocumentDB の接続だけであり、それぞれのマスター ノードとゲートウェイ ノードへの接続はユーザーに対して透過的に行われます。
2. この情報が Spark マスター ノードに返されます。  この時点で、クエリを解析して、アクセスする必要がある DocumentDB 内のパーティション (およびパーティションの場所) を特定できます。
3. この情報が Spark worker ノードに送信されます。
4. これにより、Spark worker ノードは DocumentDB のパーティションに直接接続して必要なデータを抽出し、データを Spark worker ノード内の Spark パーティションに送信できます。

重要なのは、データの移動が Spark worker ノードと DocumentDB データ ノード (パーティション) 間で行われるため、Spark と DocumentDB 間の通信が大幅に高速化されることです。

### <a name="building-the-spark-to-documentdb-connector"></a>Spark-DocumentDB コネクタを構築する
現在、コネクタ プロジェクトでは Maven を使用します。 依存関係なしにコネクタを構築するには、次のコードを実行します。
```
mvn clean package
```
*releases* フォルダー内の最新バージョンの Jar をダウンロードすることもできます。

### <a name="including-the-azure-documentdb-spark-jar"></a>Azure DocumentDB Spark JAR を含める
コードを実行する前に、まず、Azure DocumentDB Spark JAR を含める必要があります。  **spark-shell** を使用する場合は、**--jars** オプションを使用して JAR を含めることができます。  

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1-jar-with-dependencies.jar
```

依存関係なしに Jar を実行する場合は、次のコードを使用します。

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1.jar,/$location/azure-documentdb-1.9.6.jar
```

Azure HDInsight Jupyter Notebook サービスなどの Notebook サービスを使用する場合は、**Spark マジック** コマンドを使用できます。

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.9.6.jar","wasb:///example/jars/azure-documentdb-spark-0.0.1.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

**jars** コマンドでは、**azure-documentdb-spark** (それ自体と Azure DocumentDB Java SDK) に必要な 2 つの Jar を含め、**scala-reflect** を除外できるので、Livy の呼び出しが妨げられることはありません (Jupyter Notebook > Livy > Spark)。

### <a name="connecting-spark-to-documentdb-using-the-connector"></a>コネクタを使用して Spark を DocumentDB に接続する
通信転送は少し複雑になりますが、コネクタを使用して Spark から DocumentDB へのクエリを実行すると、クエリが大幅に高速化されます。

次のコード スニペットは、Spark コンテキスト内で コネクタを使用する方法を示しています。

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))
 
// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

このコード スニペットでは次のことを示しています。

- **azure-documentdb-spark** には、優先する場所 (たとえば、どのような優先順位でどの読み取りレプリカを選択するか) を含め、必要な接続パラメーターがすべて含まれています。
- 必要なライブラリをインポートし、masterKey と host を構成して DocumentDB クライアントを作成します。

### <a name="executing-spark-queries-via-the-connector"></a>コネクタを介して Spark クエリを実行する

次の例では、指定した読み取り専用キーを使用して、前のスニペットで作成した DocumentDB インスタンスを使用します。 次のコード スニペットでは、シアトル発のフライトのフライト遅延情報を抽出するクエリを実行して、(以前に指定した DoctorWho アカウント内の) DepartureDelays.flights_pcoll コレクションに接続します。

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-documentdb-connector-implementation"></a>Spark-DocumentDB コネクタ実装を使用する理由

コネクタを使用して Spark を DocumentDB に接続するのは、一般に次のような場合です。

* Scala を使用し、「[Issue 3: Add Python wrapper and examples (問題点 3: Python ラッパーの追加と例)](https://github.com/Azure/azure-documentdb-spark/issues/3)」で説明するように、Scala を更新して Python ラッパーを含める場合。
* Apache Spark と DocumentDB 間で大量のデータを転送する場合。

クエリのパフォーマンスの違いについては、[クエリ テストの実行に関する Wiki](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs) をご覧ください。

## <a name="distributed-aggregation-example"></a>分散集計の例
このセクションでは、Apache Spark と Azure DocumentDB を組み合わせて使用して、分散集計と分析を実行する方法の例を示します。  ブログ記事「[Planet scale aggregates with Azure DocumentDB (Azure DocumentDB を使用した世界規模の集計)](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)」に記載されているように、Azure DocumentDB では集計を既にサポートしているので、ここでは Apache Spark を使用して次の段階に進める方法を説明します。

これらの集計は、[Spark-DocumentDB コネクタ Notebook](https://github.com/Azure/azure-documentdb-spark/blob/master/samples/notebooks/Spark-to-DocumentDB_Connector.ipynb) に関連しています。

### <a name="connecting-to-flights-sample-data"></a>フライト サンプル データに接続する
以下の集計例では、**DoctorWho** DocumentDB データベースに格納されている一部のフライト実績データにアクセスします。  このデータベースに接続するには、次のコード スニペットを使用する必要があります。

```
// Import Spark to DocumentDB connector
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Connect to DocumentDB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))

// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

このスニペットでは、必要なデータのフィルター処理されたセットを DocumentDB から Spark に転送する基本クエリも実行します (後者は分散集計を実行できます)。  この例では、シアトル (SEA) 発のフライトを要求します。

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

以下の結果は、Jupyter Notebook サービスからクエリを実行して生成されたものです。  コード スニペットはいずれも汎用であり、サービスに固有のものではないことに注意してください。

### <a name="running-limit-and-count-queries"></a>LIMIT クエリと COUNT クエリを実行する
SQL/Spark SQL の場合と同様に、まず **LIMIT** クエリを実行します。

![Spark LIMIT クエリ](./media/documentdb-spark-connector/azure-documentdb-spark-sql-query.png)

次のクエリは、単純な高速の **COUNT** クエリです。

![Spark COUNT クエリ](./media/documentdb-spark-connector/azure-documentdb-spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY クエリ
この次のセットでは、DocumentDB データベースに対して **GROUP BY** クエリを簡単に実行できます。

```
select destination, sum(delay) as TotalDelays 
from originSEA 
group by destination 
order by sum(delay) desc limit 10
```

![Spark GROUP BY クエリのグラフ](./media/documentdb-spark-connector/azure-documentdb-group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>DISTINCT、ORDER BY クエリ
ここでは、**DISTINCT、ORDER BY** クエリを実行します。

![Spark GROUP BY クエリのグラフ](./media/documentdb-spark-connector/azure-documentdb-order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>フライト データの分析の続行
以下のサンプル クエリを使用して、フライト データの分析を続行できます。

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>シアトル発のフライトで遅延が発生した上位 5 つの目的地 (都市)
```
select destination, sum(delay) 
from originSEA
where delay < 0 
group by destination 
order by sum(delay) limit 5
```
![Spark - 上位遅延のグラフ](./media/documentdb-spark-connector/azure-documentdb-top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>シアトル発のフライトの目的地 (都市) 別に遅延の中央値を計算する
```
select destination, percentile_approx(delay, 0.5) as median_delay 
from originSEA
where delay < 0 
group by destination 
order by percentile_approx(delay, 0.5)
```

![Spark - 遅延の中央値のグラフ](./media/documentdb-spark-connector/azure-documentdb-median-delays-graph.png)

## <a name="next-steps"></a>次のステップ

Spark-DocumentDB コネクタをまだダウンロードしていない場合は、[azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark) GitHub レポジトリからダウンロードし、レポジトリでその他のリソースを調べます。

* [分散集計の例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [サンプル スクリプトと Notebook](https://github.com/Azure/azure-documentdb-spark/tree/master/samples)

また、[Apache Spark SQL、DataFrames、データセット ガイド](http://spark.apache.org/docs/latest/sql-programming-guide.html)と [Azure HDInsight 上の Apache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) に関する記事を確認することもできます。



