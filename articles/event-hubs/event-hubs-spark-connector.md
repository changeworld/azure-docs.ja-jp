---
title: Apache Spark と Azure Event Hubs の統合 | Microsoft Docs
description: Apache Spark と統合して、Event Hubs での構造化ストリームを有効にします
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 2dcf390b80c119ab21948b982c0812395e45bc01
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Apache Spark と Event Hubs の統合

Azure Event Hubs と [Apache Spark](https://spark.apache.org/) をシームレスに統合すると、_エンド ツー エンド_分散ストリーミング のアプリケーションの開発がさらに簡単になります。 この統合では、[Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html)、[Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html)、[構造化ストリーミング](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)がサポートされています。 Apache Spark 用の Event Hubs コネクタは [GitHub](https://github.com/Azure/azure-event-hubs-spark) で入手できます。 このライブラリは、[Maven Central Repository](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C) の Maven プロジェクトで使用することもできます

この記事では、[Azure Databrick](https://azure.microsoft.com/services/databricks/) で継続的なアプリケーションを作成する方法について説明します。 この記事では [Azure Databricks](https://azure.microsoft.com/services/databricks/) を使用しますが、Spark クラスターも [HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-overview) で使用できます。

次の例では、2 つの Scala ノートブックを使用します。1 つはイベント ハブからのイベントのストリーミング、もう 1 つはイベント ハブへのイベントの返送に使用します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 ない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)します
* Event Hubs インスタンス。 ない場合は[作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)します
* [Azure Databricks](https://azure.microsoft.com/services/databricks/) インスタンス。 ない場合は[作成](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)します
* [Maven コーディネートを使用してライブラリを作成](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

使用しているノートブックで次のコード スニペットを使用して、イベント ハブからイベントをストリーミングします。

```scala
import org.apache.spark.eventhubs._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()

// Select the body column and cast it to a string.
val eventhubs = reader
  .select("CAST (body AS STRING)")
  .as[String]
```
次のコード スニペットは、Spark のバッチ API を使用して、ユーザーのイベント ハブにイベントを送信するときに使用されます。 ストリーミング クエリを記述して、イベントをイベント ハブに送信することもできます。

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified Event Hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

この記事では、リアルタイムでフォールト トレラントなストリーミング ソリューションを構築する Event Hubs コネクタのしくみについて簡単に説明します。 構造化ストリーミングおよび Event Hubs 統合コネクタの詳細を確認するには、以降の手順に従います。

## <a name="next-steps"></a>次の手順

* [構造化ストリーミングおよび Azure Event Hubs 統合ガイド](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming および Event Hubs 統合ガイド](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
