---
title: 分析ストア データをパーティション分割するためにカスタム パーティション分割を構成する (プレビュー)
description: Azure Cosmos DB の Azure Synapse Link を使用して Azure Synapse Spark ノートブックからカスタム パーティション分割をトリガーする方法について説明します。 構成オプションについて説明します。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7848a9b578c6054ef1d3deb29bf4c2bf92b9f68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089992"
---
# <a name="configure-custom-partitioning-to-partition-analytical-store-data-preview"></a>分析ストア データをパーティション分割するためにカスタム パーティション分割を構成する (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

カスタム パーティション分割を使用するには、Azure Cosmos DB アカウントで Azure Synapse Link を有効にする必要があります。 詳細については、[Azure Synapse Link の構成方法](configure-synapse-link.md)に関するページを参照してください。 Azure Cosmos DB の Azure Synapse Link を使用して、Azure Synapse Spark ノートブックからカスタム パーティション分割をトリガーできます。

> [!IMPORTANT]
> 現在、カスタム パーティション分割機能は、パブリック プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

> [!NOTE]
> カスタム パーティション分割を利用するには、Azure Cosmos DB アカウントで Azure Synapse Link が有効になっている必要があります。 現在、カスタム パーティション分割は Azure Synapse Spark 2.0 でのみサポートされています。

カスタム パーティション分割の実行をトリガーするために必要な構成オプションは次のとおりです。

* `spark.cosmos.asns.execute.partitioning` - カスタム パーティション分割の実行をトリガーするブール値。 既定値は false です。

* `spark.cosmos.asns.partition.keys` - DDL の書式設定された文字列を使用したパーティションのキー。 例: *ReadDate String*。

* `spark.cosmos.asns.basePath` - Synapse プライマリ ストレージ アカウントのパーティション分割されたストアのベース パス。

> [!NOTE]
> 複数のパーティション キーを選択した場合は、キーを示す basePath を使用して、同じパーティション分割されたストアからこれらのレコードにアクセスできます。

カスタム パーティション分割の実行をトリガーするときに使用できる省略可能な構成オプションを次に示します。

* `spark.cosmos.asns.merge.partitioned.files` - 実行のたびに、パーティション値ごとに 1 つのファイルを作成できるようにするブール値。 既定値は false です。

* `spark.cosmos.asns.partitioning.maxRecordsPerFile` - パーティション分割されたストアにある、パーティション分割された 1 つのファイル内のレコードの最大数。 この構成と `spark.cosmos.asns.merge.partitioned.files` が指定されている場合、レコード数が maxRecordsPerFile 値を超えると、新しいファイルが作成されます。 この構成は通常、大規模なコレクションの初期パーティション分割にのみ必要です。 既定値は 1,000,000 です。

  maxRecordsPerFile を設定するが `spark.cosmos.asns.merge.partitioned.files` を構成しない場合、レコードは maxRecordsPerFile に到達する前にファイル間で分割できます。 ファイルの分割は、プールで使用可能な並列処理にも依存します。

* `spark.cosmos.asns.partitioning.shuffle.partitions` - パーティション分割されたストアに対するパーティション分割された書き込み時に並列処理を制御します。 この構成は、大規模なコレクションの初期パーティション分割にのみ必要です。 これは、Spark プールで使用可能なコアの数に設定されます。 既定値は 200 です。 他のワークロードでプールが使用されていない場合、値が小さいとリソースが無駄になる可能性があります。 多くの場合、より大きな値を指定すると問題が発生しません。これは、一部のタスクが早期に完了し、低速なタスクが実行されている間にタスクを開始できるためです。 パーティション分割ジョブを短時間で完了させる場合は、プール サイズを増やすことをお勧めします。

# <a name="python"></a>[Python](#tab/python)

```python
spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.execute.partitioning", "true") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .option("spark.cosmos.asns.merge.partitioned.files", "true") \
    .option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000") \
    .option("spark.cosmos.asns.partitioning.shuffle.partitions", "400") \
    .load()
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
spark.read.
    format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.asns.execute.partitioning", "true").
    option("spark.cosmos.asns.partition.keys", "readDate String").
    option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
    option("spark.cosmos.asns.merge.partitioned.files", "true").
    option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000").
    option("spark.cosmos.asns.partitioning.shuffle.partitions", "400").
    load()
```
---

## <a name="query-execution-with-partitioned-store"></a>パーティション分割されたストアを使用してクエリを実行する

パーティション分割されたストアのサポートを使用してクエリを実行するには、次の 2 つの構成が必要です。

* `spark.cosmos.asns.partition.keys`
* `spark.cosmos.asns.basePath`

次の例では、これらの構成を使用して上記のパーティション分割されたストアに対してクエリを実行する方法と、パーティション キーを使用したフィルター処理でパーティション排除を利用する方法を示します。 このパーティション分割されたストアは、"ReadDate" フィールドを使用してパーティション分割されています。

# <a name="python"></a>[Python](#tab/python)

```python
df = spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .load()

df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
val df = spark.read.
            format("cosmos.olap").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.asns.partition.keys", "readDate String").
            option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
            load()
val df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```
---

実行時、上記の *ReadDate = '2021-11-01'* フィルターによって、*2021-11-01* 以外の ReadDate 値に対応するデータがスキャンから削除されます。

> [!NOTE]
> 次の対象にクエリを実行すると、パーティション分割されたストアを使用したクエリの改善が適用されます。
>
> * Azure Cosmos DB 分析ストア コンテナーから作成された Spark Dataframes および
> * Azure Cosmos DB 分析ストア コンテナーを指す Spark テーブル。

## <a name="next-steps"></a>次のステップ

詳しく学習するために、次のドキュメントを参照してください。

* Azure Synapse Link for Azure Cosmos DB の[カスタム パーティション分割の概要](custom-partitioning-analytical-store.md)
* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md)
* [Azure Cosmos DB 分析ストアの概要](analytical-store-introduction.md)
* [Azure Synapse Link for Azure Cosmos DB の概要](configure-synapse-link.md)
* [Azure Synapse Link for Azure Cosmos DB についてよく寄せられる質問](synapse-link-frequently-asked-questions.yml)
