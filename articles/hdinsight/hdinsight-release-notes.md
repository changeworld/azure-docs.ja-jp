---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 0481f92666c8ca547dc427e117f9917afb257629
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511876"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、**最近**の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

## <a name="release-date-07132020"></a>リリース日: 2020 年 7 月 13 日

このリリースは、HDInsight 3.6 と4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

## <a name="new-features"></a>新機能
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Microsoft Azure 用カスタマー ロックボックスのサポート
Azure HDInsight で Azure カスタマー ロックボックスがサポートされるようになりました。 ここでは、お客様が顧客データへのアクセス要求を承認または拒否するインターフェイスが用意されています。 これは、Microsoft のエンジニアがサポート リクエストの際に顧客データにアクセスする必要がある場合に使用されます。 詳細については、「[Microsoft Azure 用カスタマー ロックボックス](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview)」を参照してください。

### <a name="service-endpoint-policies-for-storage"></a>ストレージのサービス エンドポイント ポリシー
お客様は、HDInsight クラスター サブネットでサービス エンドポイント ポリシー (SEP) を使用できるようになりました。 [Azure サービス エンドポイント ポリシー](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)の詳細を確認してください。

## <a name="deprecation"></a>非推奨
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 3.6 Spark クラスターでの Spark 2.1 と 2.2 の非推奨化
2020 年 7 月 1 日以降、HDInsight 3.6 で Spark 2.1 と 2.2 を使用して新しい Spark クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断する可能性を回避するため、2020 年 6月 30 日までに HDInight 3.6 で Spark 2.3 に移行することを検討してください。
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark クラスターでの Spark 2.3 の非推奨化
2020 年 7 月 1 日以降、HDInsight 4.0 で Spark 2.3 を使用して新しい Spark クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断する可能性を回避するため、2020 年 6月 30 日までに HDInight 4.0 で Spark 2.4 に移行することを検討してください。
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka クラスターの Kafka 1.1 の廃止
2020 年 7 月 1 日以降、HDInsight 4.0 で Kafka 1.1 を使用して新しい Kafka クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断される可能性を回避するため、2020 年 6 月 30 日までに HDInsight 4.0 で Kafka 2.1 に移行することを検討してください。

## <a name="behavior-changes"></a>動作の変更
注意する必要がある動作変更はありません。

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Spark、Hadoop、および ML サービスのさまざまな Zookeeper SKU を選択する機能
現在、HDInsight では、Spark、Hadoop、および ML サービス クラスターの種類での Zookeeper SKU の変更はサポートされていません。 Zookeeper ノードでは A2_v2/A2 SKU が使用され、これには課金されません。 今後のリリースでは、必要に応じて、Spark、Hadoop、および ML サービスの Zookeeper SKU を変更できるようになります。 A2_v2/A2 以外の SKU を持つ Zookeeper ノードは課金されます。 既定の SKU は引き続き A2_v2/A2 であり、無料でご利用いただけます。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 
### <a name="fixed-hive-warehouse-connector-issue"></a>Hive Warehouse Connector の問題の修正
以前のリリースでは、Hive Warehouse Connector の操作性に問題がありました。 この問題は修正されました。 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Zeppelin Notebook で先頭の 0 が切り捨てられる問題の修正
Zeppelin では、文字列形式のテーブル出力の先頭の 0 が誤って切り捨てられていました。 この問題は、今回のリリースで修正されています。

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
このリリースでは、コンポーネントのバージョン変更はありません。 HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[このドキュメント](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)で確認できます。
