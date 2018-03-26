---
title: HDInsight での Apache Kafka の概要 - Azure | Microsoft Docs
description: HDInsight での Apache Kafka について説明します。その機能と役割について説明し、例と概要情報の入手先を紹介します。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 35dc83dea7dc1310852437cd7eede32cfa890e8f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="introducing-apache-kafka-on-hdinsight"></a>HDInsight での Apache Kafka の概要

[Apache Kafka](https://kafka.apache.org) はオープン ソースの分散ストリーム プラットフォームで、リアルタイムのストリーミング データ パイプラインとアプリケーションの構築に使用できます。 Kafka は、名前付きデータ ストリームへの公開および購読ができる、メッセージ キューと同様のメッセージ ブローカー機能も提供しています。 HDInsight での Kafka の使用により、管理された、拡張性の高い、高可用性のサービスが Microsoft Azure クラウドで提供されます。

## <a name="why-use-kafka-on-hdinsight"></a>HDInsight で Apache Kafka を使用する理由

HDInsight における Kafka の機能は次のとおりです。

* __Kafka のアップタイムに関して 99.9% のサービス レベル アグリーメント (SLA)__: 詳細については、[HDInsight の SLA 情報](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/)に関するドキュメントを参照してください。

* __フォールト トレランスとラック アウェアネス__: Kafka は、いくつかの環境での稼働に適したラックの 1 次元ビューで設計されました。 しかし、Azure のような環境では、ラックは更新ドメイン (UD) と障害ドメイン (FD) の 2 次元に分割されます。 Microsoft は、複数の UD と FD 間で Kafka のパーティションとレプリカを再調整するツールを提供しています。 

    詳細については、[HDInsight 上の Kafka による高可用性](apache-kafka-high-availability.md)に関するページを参照してください。

* **Azure Managed Disks との統合**: Managed Disks は、HDInsight 上の Kafka で使用されるディスクのスケーリングとスループットを高める機能を提供します。クラスター内のノードあたり、最大 16 TB です。

    HDInsight 上の Kafka での Managed Disks の構成について詳しくは、「[HDInsight 上の Apache Kafka 用に記憶域とスケーラビリティを構成する](apache-kafka-scalability.md)」をご覧ください。

    Managed Disks について詳しくは、「[Azure Managed Disks の概要](../../virtual-machines/windows/managed-disks-overview.md)」をご覧ください。

* **アラート、監視、および予測メンテナンス**: HDInsight 上の Kafka を監視するために、Azure Log Analytics を使用することができます。 Log Analytics は、ディスクおよび NIC のメトリック、Kafka からの JMX メトリックなど、仮想マシン レベルの情報を表示します。

    詳細については、[HDInsight 上の Kafka のログの分析](apache-kafka-log-analytics-operations-management.md)に関するページを参照してください。

* **Kafka データのレプリケーション**: Kafka には、MirrorMaker ユーティリティが用意されています。このユーティリティは、Kafka クラスター間でデータをレプリケートします。

    MirrorMaker の使用方法については、[HDInsight 上の Kafka での Kafka トピックのレプリケート](apache-kafka-mirroring.md)に関するページを参照してください。

* **クラスターのスケーリング**: HDInsight を使用すると、クラスターの作成後に、ワーカー ノード (Kafka ブローカーをホストするノード) の数を変更することができます。 ワークロードの増加に応じてクラスターをスケールアップしたり、コストを削減するためにスケールダウンしたりします。 スケーリングは、Azure Portal、Azure PowerShell、およびその他の Azure 管理インターフェイスで実行できます。 Kafka では、スケーリング操作の後で、パーティションのレプリカを再調整する必要があります。 パーティションを再調整することで、Kafka は新しい数のワーカー ノードを活用することができます。

    詳細については、[HDInsight 上の Kafka による高可用性](apache-kafka-high-availability.md)に関するページを参照してください。

* **発行-購読のメッセージング パターン**: Kafka は、Kafka トピックにレコードを発行するためのプロデューサー API を提供します。 コンシューマー API は、トピックの購読に使用されます。

    詳細については、[HDInsight での Kafka の使用開始](apache-kafka-get-started.md)に関するページを参照してください。

* **ストリーム処理**: リアルタイムのストリーム処理には、通常、Kafka と共に Apache Storm または Spark が使用されます。 Kafka 0.10.0.0 (HDInsight バージョン 3.5 および 3.6) では、Storm や Spark を必要とせずに、ストリーミング ソリューションを構築できるストリーミング API が導入されました。

    詳細については、[HDInsight での Kafka の使用開始](apache-kafka-get-started.md)に関するページを参照してください。

* **水平スケール**: Kafka は、HDInsight クラスター内のノード間でストリームを分割します。 コンシューマー プロセスを各パーティションと関連付けることにより、レコード使用時の負荷分散を実現することができます。

    詳細については、[HDInsight での Kafka の使用開始](apache-kafka-get-started.md)に関するページを参照してください。

* **順番どおりに処理**: 各パーティション内で、レコードは、受信された順番にストリームに格納されます。 パーティションごとに 1 つのコンシューマー プロセスを関連付けることで、レコードが順番通りに確実に処理されるようになります。

    詳細については、[HDInsight での Kafka の使用開始](apache-kafka-get-started.md)に関するページを参照してください。

## <a name="use-cases"></a>ユース ケース

* **メッセージング**: Kafka は発行-購読のメッセージ パターンをサポートするため、メッセージ ブローカーとしてよく使用されます。

* **アクティビティの追跡**: Kafka ではレコードの受信順序のログ記録が提供されるため、アクティビティの追跡と再現に使用することができます。 たとえば、Web サイト上またはアプリケーション内のユーザー アクションです。

* **集計**: ストリーム処理を使用して異なるストリームからの情報を集計し、情報をまとめて運用データに一元化することができます。

* **変換**: ストリーム処理を使用して入力された複数のトピックからのデータを結合し、1 つまたは複数の出力トピックに変換することができます。

## <a name="architecture"></a>アーキテクチャ

![Kafka クラスターの構成](./media/apache-kafka-introduction/kafka-cluster.png)

この図は、コンシューマー グループ、パーティション分割、レプリケーションを使ってイベントの並列読み取りとフォールト トレランスを実現する標準的な Kafka の構成を示しています。 Apache ZooKeeper は、Kafka クラスターの状態管理におけるトランザクションの同時実行性、回復性、短い待ち時間の実現を目指して構築されています。 Kafka では、"*トピック*" 内にレコードが格納されます。 レコードは、*プロデューサー*によって生成され、*コンシューマー*によって消費されます。 プロデューサーは、Kafka の*ブローカー*からレコードを取得します。 HDInsight クラスターの各ワーカー ノードが、Kafka のブローカーです。 コンシューマーごとに 1 つのパーティションを作成することで、ストリーミング データの並列処理が可能となっています。 レプリケーションによって複数ノードにパーティションが分散されて、ノード (ブローカー) の機能不全に対する保護措置が講じられています。 *(L)* で示されたパーティションは、各パーティションのリーダーです。 プロデューサー トラフィックは、ZooKeeper によって管理された状態に基づいて、各ノードのリーダーにルーティングされます。

Kafka ブローカーにはそれぞれ、Azure Managed Disks が使用されます。 ディスクの数は、ユーザーが定義し、ブローカーごとに最大 16 TB の記憶域を確保できます。

> [!IMPORTANT]
> 基になる Azure データ センターのハードウェア (ラック) は、Kafka では認識されません。 基になるハードウェア間でパーティションを正しく調整する方法については、[データの高可用性構成 (Kafka)](apache-kafka-high-availability.md) に関するドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

次のリンクを使用することで、HDInsight で Apache Kafka を使用する方法を知ることができます。

* [HDInsight での Kafka の使用](apache-kafka-get-started.md)

* [MirrorMaker を使用した HDInsight での Kafka のレプリカの作成](apache-kafka-mirroring.md)

* [HDInsight での Kafka に Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)

* [HDInsight での Kafka に Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)

* [Azure Virtual Network 経由で Kafka に接続する](apache-kafka-connect-vpn-gateway.md)