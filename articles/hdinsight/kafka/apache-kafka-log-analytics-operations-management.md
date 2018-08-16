---
title: Apache Kafka の Log Analytics - Azure HDInsight
description: Log Analytics を使用して、Azure HDInsight 上の Apache Kafka クラスターからログを分析します。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 9421217383f390630687d8416512401487ad3f7e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621390"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight で Apache Kafka のログを分析する

Log Analytics を使用して、HDInsight 上の Apache Kafka によって生成されたログを分析します。

## <a name="enable-log-analytics-for-kafka"></a>Kafka の Log Analytics を有効にする

HDInsight の Log Analytics を有効にする手順は、すべての HDInsight クラスターで同じです。 必要なサービスを作成して構成する方法を理解するために、次のリンクを使用してください。

1. Log Analytics ワークスペースを作成します。 詳細については、「[Log Analytics ワークスペースを使って作業を開始する](https://docs.microsoft.com/azure/log-analytics)」をご覧ください。

2. HDInsight クラスターに Kafka を作成します。 詳細については、「[HDInsight での Apache Kafka の開始](apache-kafka-get-started.md)」をご覧ください。

3. Log Analytics を使用する Kafka クラスターを構成します。 詳細については、「[Use Log Analytics to monitor HDInsight (Log Analytics を使用した HDInsight の監視)](../hdinsight-hadoop-oms-log-analytics-tutorial.md)」をご覧ください。

    > [!NOTE]
    > `Enable-AzureRmHDInsightOperationsManagementSuite` コマンドレットを使って、Log Analytics を使用するようにクラスターを構成することもできます。 このコマンドレットを使用するには、次の情報が必要です。
    >
    > * HDInsight クラスター名。
    > * Log Analytics のためのワークスペース ID。 Log Analytics ワークスペースのワークスペース ID を検索することができます。
    > * Log Analytics 接続のための主キー。 主キーを見つけるには、Azure portal のワークスペースを開き、左側のメニューから __[詳細設定]__ を選択します。 [詳細設定] から __[接続されたソース]__>__[Linux サーバー]__ を選択します。


> [!IMPORTANT]
> データを Log Analytics に使用できるようになるまで、20 分程度かかる場合があります。

## <a name="query-logs"></a>ログを照会する

1. [Azure Portal](https://portal.azure.com) から、Log Analytics のワークスペースを選択します。

2. __[ログ検索]__ を選択します。 ここでは、Kafka から収集されたデータを検索できます。 次に検索例をいくつか示します。

    * ディスク使用量:`Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * CPU 使用率: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * 1 秒あたりの受信メッセージ: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * 1 秒あたりの受信バイト数: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * 1 秒あたりの送信バイト数: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > クエリの値をクラスター固有の情報に置き換えます。 たとえば、`ClusterName_s` には、お使いのクラスターの名前を設定する必要があります。 `HostName_s` には、クラスター内のワーカー ノードのドメイン名を設定する必要があります。

    また、`*` を入力して、記録されているすべてのタイプを検索することもできます。 現在、クエリには、次のログを使用できます。

    | ログのタイプ | 説明 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka ブローカーの server.log |
    | log\_kafkacontroller\_CL | Kafka ブローカーの controller.log |
    | metrics\_kafka\_CL | Kafka JMX メトリック |

    ![CPU 使用率検索の画像](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>次の手順

Log Analytics の詳細については、「[Log Analytics ワークスペースを使って作業を開始する](../../log-analytics/log-analytics-get-started.md)」をご覧ください。

Kafka の操作の詳細については、次のドキュメントを参照してください。

 * [HDInsight クラスター間で Kafka をミラーリングする](apache-kafka-mirroring.md)
 * [HDInsight 上の Kafka の拡張性を改善する](apache-kafka-scalability.md)
 * [Kafka で Spark ストリーミングを (DStream) を使用する](../hdinsight-apache-spark-with-kafka.md)
 * [Kafka で Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
