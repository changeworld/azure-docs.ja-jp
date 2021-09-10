---
title: Azure Synapse Link で Apache Spark 3 を使用して Azure Cosmos DB と対話する
description: Azure Synapse Link で Apache Spark 3 を使用して Azure Cosmos DB と対話する方法
services: synapse-analytics
author: moderakh
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/26/2021
ms.author: moderakh
ms.custom: cosmos-db
ms.openlocfilehash: 4eaa82f3a4089d4fedc002fc159ecf9b7ae02c24
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117894"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-3-in-azure-synapse-link"></a>Azure Synapse Link で Apache Spark 3 を使用して Azure Cosmos DB と対話する

この記事では、Synapse Apache Spark 3 を使用して Azure Cosmos DB と対話する方法について説明します。 Scala、Python、SparkSQL、C# が完全にサポートされることで、Synapse Apache Spark 3 は [Azure Synapse Link for Azure Cosmos DB](../../cosmos-db/synapse-link.md) の分析、Data Engineering、データ サイエンス、データ探索のシナリオの中核となります。

Azure Cosmos DB との対話中に、次の機能がサポートされます。
* Synapse Apache Spark 3 を使用すると、トランザクション ワークロードのパフォーマンスに影響を与えることなく、Azure Synapse Link で有効になっている Azure Cosmos DB コンテナー内のデータをほぼリアルタイムで分析できます。 次の 2 つのオプションを使用して、Spark から Azure Cosmos DB [分析ストア](../../cosmos-db/analytical-store-introduction.md)に対してクエリを実行できます。
    + Spark DataFrame に読み込む
    + Spark テーブルを作成する
* Synapse Apache Spark を使用すると、Azure Cosmos DB にデータを取り込むこともできます。 データは常にトランザクション ストアを介して Azure Cosmos DB コンテナーに取り込まれることに注意してください。 Synapse Link が有効になっていると、新しい挿入、更新、および削除が分析ストアに自動的に同期されます。
* また、Synapse Apache Spark では、ソースおよびシンクとしての Azure Cosmos DB を使用した Spark の構造化ストリーミングもサポートされています。 

次のセクションで、上の機能の構文について説明します。 また、[Apache Spark for Azure Synapse Analytics を使用して Azure Cosmos DB 分析ストアのクエリを実行する](/learn/modules/query-azure-cosmos-db-with-apache-spark-for-azure-synapse-analytics/)方法に関する Learn モジュールも確認できます。 Azure Synapse Analytics ワークスペースのジェスチャは、すぐに使い始めることができるように設計されています。 ジェスチャは、Synapse ワークスペースの **[データ]** タブで Azure Cosmos DB コンテナーを右クリックすると表示されます。 ジェスチャを使用すると、コードをすばやく生成し、ニーズに合わせて調整することができます。 ジェスチャは、1 回のクリックでデータを検出するのにも最適です。

> [!IMPORTANT]
> 分析スキーマには、データの読み込み操作での予期しない動作につながる可能性のある、いくつかの制約があることに注意してください。
> たとえば、分析スキーマではトランザクション スキーマの最初の 1,000 個のプロパティしか使用できない、スペースを含むプロパティは使用できない、などです。予期しない結果が生じた場合は、詳細について[分析ストア スキーマの制約](../../cosmos-db/analytical-store-introduction.md#schema-constraints)を参照してください。

## <a name="query-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析ストアに対してクエリを実行する

Azure Cosmos DB 分析ストアに対してクエリを実行するために使用できる、Spark DataFrame に読み込むことと、Spark テーブルを作成することの 2 つのオプションについて学習する前に、ニーズに適したオプションを選択できるように、エクスペリエンスの違いを調べることをお勧めします。

エクスペリエンスの違いは、Azure Cosmos DB コンテナー内の基になるデータの変更が、Spark で実行される分析に自動的に反映されるかどうかです。 コンテナーの分析ストアに対して Spark DataFrame が登録されるか、Spark テーブルが作成されると、その分析ストア内のデータの現在のスナップショットに関するメタデータが Spark にフェッチされ、後続の分析が効率的にプッシュダウンされます。 Spark は遅延評価ポリシーに従うため、Spark DataFrame でアクションが呼び出されるか、Spark テーブルに対して SparkSQL クエリが実行されない限り、基になるコンテナーの分析ストアから実際のデータはフェッチされないことに注意してください。

**Spark DataFrame に読み込む** 場合、フェッチされたメタデータは Spark セッションの有効期間を通じてキャッシュされるため、DataFrame の作成時に、そのDataFrame で呼び出される後続のアクションが分析ストアのスナップショットに対して評価されます。

一方、**Spark テーブルを作成する** 場合、分析ストアの状態のメタデータは Spark にキャッシュされず、Spark テーブルに対して SparkSQL のクエリが実行されるたびに再読み込みが行われます。

このため、Spark DataFrame に読み込むことと、Spark テーブルを作成することは、Spark 分析を分析ストアの固定スナップショットに対して評価するか、分析ストアの最新のスナップショットに対して評価するかに基づいて選択できます。

> [!NOTE]
> Mongo DB アカウントの Azure Cosmos DB API に対してクエリを実行するには、分析ストア内の[完全に忠実なスキーマ表現](../../cosmos-db/analytical-store-introduction.md#analytical-schema)と、使用される拡張プロパティ名の詳細を確認してください。

> [!NOTE]
> 以下のコマンドのすべての `options` では、大文字と小文字が区別されることに注意してください。

### <a name="load-to-spark-dataframe"></a>Spark DataFrame に読み込む

この例では、Azure Cosmos DB 分析ストアを指す Spark DataFrame を作成します。 これで、DataFrame に対して Spark アクションを呼び出すことで、追加の分析を実行できるようになります。 この操作はトランザクション ストアに影響しません。

**Python** の構文は次のようになります。
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

**Scala** での同じ構文は、次のようになります。
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Spark テーブルを作成する

この例では、Azure Cosmos DB 分析ストアを指す Spark テーブルを作成します。 これで、テーブルに対して SparkSQL のクエリを呼び出すことで、追加の分析を実行できるようになります。 この操作は、トランザクション ストアに影響を与えることも、データ移動を引き起こすこともありません。 この Spark テーブルを削除しても、基になっている Azure Cosmos DB コンテナーと対応する分析ストアに影響はありません。 

このシナリオは、サードパーティのツールで Spark のテーブルを再利用したり、ランタイムに基になるデータへのアクセスを提供したりするのに便利です。

Spark テーブルを作成する構文は次のとおりです。
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> 基になる Azure Cosmos DB コンテナーのスキーマが時間の経過と共に変化するシナリオがあり、更新されたスキーマを Spark テーブルに対するクエリに自動的に反映させる必要がある場合は、Spark テーブルのオプションで `spark.cosmos.autoSchemaMerge` オプションを `true` に設定することでこれを実現できます。


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Spark DataFrame を Azure Cosmos DB コンテナーに書き込む

この例では、Spark DataFrame を Azure Cosmos DB コンテナーに書き込みます。 この操作はトランザクション ワークロードのパフォーマンスに影響し、Azure Cosmos DB コンテナーまたは共有データベースでプロビジョニングされた要求ユニットが使用されます。

**Python** の構文は次のようになります。
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .mode('append')\
    .save()
```

**Scala** での同じ構文は、次のようになります。
```scala
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    mode(SaveMode.Append).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>コンテナーからストリーミング データフレームを読み込む
このジェスチャでは、Spark Streaming 機能を使用して、コンテナーからデータフレームにデータを読み込みます。 データは、ワークスペースに接続したプライマリ データ レイク アカウント (およびファイル システム) に格納されます。 
> [!NOTE]
> Synapse Apache Spark で外部ライブラリを参照する場合は、[こちら](../spark/apache-spark-azure-portal-add-libraries.md)で詳細を確認してください。 たとえば、Spark DataFrame を Mongo DB 用 Cosmos DB API のコンテナーに取り込む場合は、[こちら](https://docs.mongodb.com/spark-connector/master/)にある Spark 用の Mongo DB コネクターを利用できます。

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Azure Cosmos DB コンテナーからストリーミング DataFrame を読み込む
この例では、Spark の構造化ストリーミング機能を使用して、Azure Cosmos DB の変更フィード機能を使用している Azure Cosmos DB コンテナーから Spark のストリーミング DataFrame にデータを読み込みます。 Spark によって使用されているチェックポイント データは、ワークスペースに接続したプライマリ データ レイク アカウント (およびファイル システム) に格納されます。

**Python** の構文は次のようになります。
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp.changeFeed")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.startFrom", "Beginning")\
    .option("spark.cosmos.changeFeed.mode", "Incremental")\
    .load()
```

**Scala** での同じ構文は、次のようになります。
```scala
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp.changeFeed").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.startFrom", "Beginning").
    option("spark.cosmos.changeFeed.mode", "Incremental").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Azure Cosmos DB コンテナーにストリーミング DataFrame を書き込む
この例では、ストリーミング DataFrame を Azure Cosmos DB コンテナーに書き込みます。 この操作はトランザクション ワークロードのパフォーマンスに影響し、Azure Cosmos DB コンテナーまたは共有データベースでプロビジョニングされた要求ユニットが使用されます。 (以下の例で) フォルダー */localWriteCheckpointFolder* が作成されていない場合は、自動的に作成されます。 

**Python** の構文は次のようになります。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
    .writeStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("checkpointLocation", "/tmp/myRunId/")\
    .outputMode("append")\
    .start()

streamQuery.awaitTermination()
```

**Scala** での同じ構文は、次のようになります。
```scala
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("checkpointLocation", "/tmp/myRunId/").
            start()

query.awaitTermination()
```


## <a name="next-steps"></a>次のステップ

* [Azure Synapse Link の使用を開始するための GitHub 上のサンプル](https://aka.ms/cosmosdb-synapselink-samples)
* [Azure Synapse Link for Azure Cosmos DB でサポートされている機能を確認する](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB 用の Synapse Link に接続する](../quickstart-connect-synapse-link-cosmos-db.md)
* [Apache Spark for Azure Synapse Analytics を使用して Azure Cosmos DB のクエリを実行する](/learn/modules/query-azure-cosmos-db-with-apache-spark-for-azure-synapse-analytics/)方法に関する Learn モジュールを確認する。
