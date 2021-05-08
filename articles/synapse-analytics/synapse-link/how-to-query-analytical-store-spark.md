---
title: Azure Synapse Link で Apache Spark を使用して Azure Cosmos DB と対話する
description: Azure Synapse Link で Apache Spark を使用して Azure Cosmos DB と対話する方法
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 4a8367ea41ea96d8a412af965346684737d190fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627576"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Azure Synapse Link で Apache Spark を使用して Azure Cosmos DB と対話する

この記事では、Synapse Apache Spark を使用して Azure Cosmos DB と対話する方法について説明します。 Scala、Python、SparkSQL、C# が完全にサポートされることで、Synapse Apache Spark は [Azure Synapse Link for Azure Cosmos DB](../../cosmos-db/synapse-link.md) の分析、データ エンジニアリング、データ サイエンス、データ探索のシナリオの中核となります。

Azure Cosmos DB との対話中に、次の機能がサポートされます。
* Synapse Apache Spark を使用すると、トランザクション ワークロードのパフォーマンスに影響を与えることなく、Azure Synapse Link で有効になっている Azure Cosmos DB コンテナー内のデータをほぼリアルタイムで分析できます。 次の 2 つのオプションを使用して、Spark から Azure Cosmos DB [分析ストア](../../cosmos-db/analytical-store-introduction.md)に対してクエリを実行できます。
    + Spark DataFrame に読み込む
    + Spark テーブルを作成する
* Synapse Apache Spark を使用すると、Azure Cosmos DB にデータを取り込むこともできます。 データは常にトランザクション ストアを介して Azure Cosmos DB コンテナーに取り込まれることに注意してください。 Synapse Link が有効になっていると、新しい挿入、更新、および削除が分析ストアに自動的に同期されます。
* また、Synapse Apache Spark では、ソースおよびシンクとしての Azure Cosmos DB を使用した Spark の構造化ストリーミングもサポートされています。 

次のセクションで、上の機能の構文について説明します。 Azure Synapse Analytics ワークスペースのジェスチャは、すぐに使い始めることができるように設計されています。 ジェスチャは、Synapse ワークスペースの **[データ]** タブで Azure Cosmos DB コンテナーを右クリックすると表示されます。 ジェスチャを使用すると、コードをすばやく生成し、ニーズに合わせて調整することができます。 ジェスチャは、1 回のクリックでデータを検出するのにも最適です。

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
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

**Scala** での同じ構文は、次のようになります。
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>コンテナーからストリーミング データフレームを読み込む
このジェスチャでは、Spark Streaming 機能を使用して、コンテナーからデータフレームにデータを読み込みます。 データは、ワークスペースに接続したプライマリ データ レイク アカウント (およびファイル システム) に格納されます。 
> [!NOTE]
> Synapse Apache Spark で外部ライブラリを参照する場合は、[こちら](#external-library-management)で詳細を確認してください。 たとえば、Spark DataFrame を Mongo DB 用 Cosmos DB API のコンテナーに取り込む場合は、[こちら](https://docs.mongodb.com/spark-connector/master/)にある Spark 用の Mongo DB コネクターを利用できます。

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Azure Cosmos DB コンテナーからストリーミング DataFrame を読み込む
この例では、Spark の構造化ストリーミング機能を使用して、Azure Cosmos DB の変更フィード機能を使用している Azure Cosmos DB コンテナーから Spark のストリーミング DataFrame にデータを読み込みます。 Spark によって使用されているチェックポイント データは、ワークスペースに接続したプライマリ データ レイク アカウント (およびファイル システム) に格納されます。

(以下の例で) フォルダー */localReadCheckpointFolder* が作成されていない場合は、自動的に作成されます。 この操作はトランザクション ワークロードのパフォーマンスに影響し、Azure Cosmos DB コンテナーまたは共有データベースでプロビジョニングされた要求ユニットが使用されます。

**Python** の構文は次のようになります。
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

**Scala** での同じ構文は、次のようになります。
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Azure Cosmos DB コンテナーにストリーミング DataFrame を書き込む
この例では、ストリーミング DataFrame を Azure Cosmos DB コンテナーに書き込みます。 この操作はトランザクション ワークロードのパフォーマンスに影響し、Azure Cosmos DB コンテナーまたは共有データベースでプロビジョニングされた要求ユニットが使用されます。 (以下の例で) フォルダー */localWriteCheckpointFolder* が作成されていない場合は、自動的に作成されます。 

**Python** の構文は次のようになります。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

**Scala** での同じ構文は、次のようになります。
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>外部ライブラリの管理

この例では、Synpase Apache Spark ワークスペースで Spark ノートブックを使用するときに、JAR ファイルから外部ライブラリを参照する方法について説明します。 ワークスペースに接続したプライマリ データ レイク アカウントのコンテナーに JAR ファイルを配置し、Spark ノートブックに次の `%configure` ステートメントを追加できます。

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
リモートの Spark ジョブ定義をサーバーレス Apache Spark プールに送信する場合は、こちらの[チュートリアル](../spark/apache-spark-job-definitions.md)に従って外部ライブラリを参照する方法を確認できます。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Link の使用を開始するための GitHub 上のサンプル](https://aka.ms/cosmosdb-synapselink-samples)
* [Azure Synapse Link for Azure Cosmos DB でサポートされている機能を確認する](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB 用の Synapse Link に接続する](../quickstart-connect-synapse-link-cosmos-db.md)
