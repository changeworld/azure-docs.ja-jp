---
title: "HDInsight、Hadoop、Spark テクノロジ スタックとは - Azure | Microsoft Docs"
description: "ビッグ データの分析用の Kafka、Hive、Storm、HBase を含む、HDInsight、Hadoop、Spark テクノロジ スタックおよびコンポーネントの概要。"
keywords: "Azure Hadoop, Hadoop Azure, Hadoop の紹介, Hadoop の概要, Hadoop テクノロジ スタック, Hadoop の概説, Hadoop 入門, Hadoop クラスターとは, Hadoop クラスターについて, Hadoop の使用目的"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/13/2017
ms.author: cgronlun
ms.openlocfilehash: 49ec2f305c1aef50fcff977d60ff3ce0079ff5af
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2018
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-and-spark-technology-stack"></a>Azure HDInsight、Hadoop、Spark テクノロジ スタックの概要
この記事では、完全に管理され、あらゆる領域をカバーするオープンソースのエンタープライズ向け分析サービス Azure HDInsight について紹介します。 Hadoop、Spark、Hive、LLAP、Kafka、Storm、R など、オープン ソースのフレームワークを使用することができます。 

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

[Apache Hadoop](http://hadoop.apache.org/) は本来、クラスターでのビッグ データ セットの分散処理および分析のためのオープンソース フレームワークでした。 Hadoop テクノロジ スタックには、Apache Hive、HBase、Spark、Kafka、その他の多くの関連するソフトウェアおよびユーティリティが含まれます。 HDInsight で利用可能な Hadoop テクノロジ スタック コンポーネントを確認するには、[HDInsight で利用可能なコンポーネントとバージョン][component-versioning]に関する記事を参照してください。 HDInsight の Hadoop については、[HDInsight 向けの Azure の機能](https://azure.microsoft.com/services/hdinsight/)に関するページを参照してください。

[Apache Spark](http://spark.apache.org) はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。 HDInsight の Spark について詳しくは、[Azure HDInsight の Spark の概要](../spark/apache-spark-overview.md)に関するページをご覧ください。 

<a href="https://ms.portal.azure.com/#create/Microsoft.HDInsightCluster" target="_blank"><img src="./media/apache-hadoop-introduction/deploy-to-azure.png" alt="Deploy an Azure HDInsight cluster"></a>

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>HDInsight および Hadoop テクノロジ スタックとは 
Azure HDInsight は、[Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/) の Hadoop コンポーネントのクラウド ディストリビューションです。  Azure HDInsight によって大量のデータを簡単かつ迅速に高いコスト効率で処理できるようになります。 Hadoop、Spark、Hive、LLAP、Kafka、Storm、R など、特に広く普及しているオープン ソースのフレームワークを使用して、ETL (抽出、変換、読み込み)、データ ウェアハウジング、機械学習、IoT をはじめとする広範なシナリオに対応できます。

## <a name="what-is-big-data"></a>ビッグ データとは

収集されたビッグ データは、膨れ上がっています。その勢いはしだいに増し、形式も多様化しています。 ビッグ データの種類には、履歴データ (つまり、保存されたデータ) とリアルタイム データ (つまり、ソースからストリーミングされるデータ) があります。 ビッグ データの最も一般的な使用例については、「[HDInsight を使用するシナリオ](#scenarios-for-using-hdinsight)」をご覧ください。

## <a name="why-should-i-use-hdinsight"></a>どのようなときに HDInsight を使用するか

このセクションでは、Azure HDInsight の一連の機能を紹介します。


|機能  |[説明]  |
|---------|---------|
|クラウド ネイティブ     |     Azure HDInsight を使用すると、[Hadoop](apache-hadoop-linux-tutorial-get-started.md)、 [Spark](../spark/apache-spark-jupyter-spark-sql.md)、 [Interactive query (LLAP)](../interactive-query/apache-interactive-query-get-started.md)、 [Kafka](../kafka/apache-kafka-get-started.md)、 [Storm](../storm/apache-storm-tutorial-get-started-linux.md)、 [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)、 [R Server](../r-server/r-server-get-started.md) 向けに最適化されたクラスターを Azure 上に作成することができます。 また、HDInsight は、あらゆる運用環境のワークロードについてエンド ツー エンドの SLA を提供します。  |
|低コストでスケーラブル     | ワークロードを[スケールアップまたはスケールダウン](../hdinsight-administer-use-portal-linux.md) することができます。  [クラスターをオンデマンドで作成](../hdinsight-hadoop-create-linux-clusters-adf.md) し、実際に使用する分にのみ投資することでコストを削減できます。 データ パイプラインを作成して、必要なジョブを運用化することもできます。 コンピューティングとストレージが分離され、より高いパフォーマンスと柔軟性が実現されています。 |
|セキュリティとコンプライアンス    | [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md)、[暗号化](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)、[Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md) との統合によって、企業のデータ資産を保護することができます。 また HDInsight は、業界や行政上の最も一般的な[コンプライアンス基準](https://azure.microsoft.com/overview/trusted-cloud)を満たしています。        |
|監視    | Azure HDInsight と [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) の統合によって、すべてのクラスターを監視する一元化されたインターフェイスが得られます。        |
|グローバル対応 | HDInsight は、他のあらゆるビッグ データ分析サービスより多くの [リージョン](https://azure.microsoft.com/regions/services/) で提供されています。 Azure HDInsight は、Azure Government、Azure China、Azure Germany でも提供されており、独自の法令が施行されている地域における企業のニーズに応えます。 |  
|生産性     |  Scala、Python、R、Java、.NET に対応した [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)、[Eclipse](../spark/apache-spark-eclipse-tool-plugin.md)、[IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) などのお好みの開発環境で、Hadoop および Spark 向けの豊富な生産性ツールを利用できます。 データ サイエンティストが、[Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) や [Zeppelin](../spark/apache-spark-zeppelin-notebook.md) など、広く使われている Notebook を使って共同作業を行うこともできます。    |
|機能拡張     |  [スクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md)を使ってコンポーネント (Hue、Presto など) をインストールしたり、[エッジ ノードを追加](../hdinsight-apps-use-edge-node.md)したり、[定評のある他のビッグ データ アプリケーションと連携](../hdinsight-apps-install-applications.md)したりすることで、HDInsight のクラスターを拡張することができます。 HDInsight は、特に普及率の高いビッグ データ ソリューションと[ワンクリック](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) デプロイでシームレスに連携します。|

## <a name="scenarios-for-using-hdinsight"></a>HDInsight を使用するシナリオ

Azure HDInsight は、ビッグ データ処理に関してさまざまなユース ケースに対応します。 収集されたビッグ データは、膨れ上がっています。その勢いはしだいに増し、形式も多様化しています。 ビッグ データは履歴データ (既に収集されて保存されているデータ) である場合もあれば、リアルタイム データ (ソースから直接ストリーミングされるデータ) である場合もあります。 そのようなデータの処理に関するユース ケースは、次のカテゴリに集約することができます。 

### <a name="batch-processing-etl"></a>バッチ処理 (ETL)

ETL (抽出、変換、読み込み) は、異種データ ソースから非構造化データまたは構造化データを抽出し、構造化された形式に変換して、データ ストアに読み込む処理です。 変換後のデータは、データ サイエンスやデータ ウェアハウジングに使用することができます。

### <a name="internet-of-things-iot"></a>モノのインターネット(IoT)

さまざまなデバイスからリアルタイムで受信したストリーミング データを HDInsight で処理することができます。 詳しくは、[こちらのブログ](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)をご覧ください。

![HDInsight のアーキテクチャ: モノのインターネット](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png) 

### <a name="data-science"></a>データ サイエンス

データから重要な洞察を得るアプリケーションを HDInsight で作成することができます。 また、その基盤の上に Azure Machine Learning を使用して、ビジネスの将来の傾向を予測することもできます。 詳しくは、[こちらの顧客事例](https://customers.microsoft.com/story/pros)をご覧ください。

![HDInsight のアーキテクチャ: データ サイエンス](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png)

### <a name="data-warehousing"></a>データ ウェアハウス

HDInsight を使用して、あらゆる形式の構造化データや非構造化データに対話型クエリをペタバイト規模で実行することができます。 それらを BI ツールに接続するモデルを作成することもできます。 詳しくは、[こちらの顧客事例](https://customers.microsoft.com/story/milliman)をご覧ください。 

![HDInsight のアーキテクチャ: データ ウェアハウジング](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png)

### <a name="hybrid"></a>ハイブリッド

HDInsight を使用して、既にあるオンプレミスのビッグ データ インフラストラクチャを Azure にまで拡張し、そのクラウドの高度な分析機能を活用することができます。

![HDInsight のアーキテクチャ: ハイブリッド](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png)

## <a name="overview"></a>HDInsight でのクラスターの種類
HDInsight には、特定のクラスターの種類のほか、コンポーネント、ユーティリティ、および言語の追加などのクラスター カスタマイズ機能が含まれています。

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark、Kafka、Interactive Query、HBase、カスタマイズ、その他のクラスターの種類
HDInsight は、次のクラスターの種類を提供します。

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: [HDFS](#hdfs)、[YARN](#yarn) によるリソース管理、およびシンプルな [MapReduce](#mapreduce) プログラミング モデルを使用して、バッチ データを同時に処理および分析します。
* **[Apache Spark](http://spark.apache.org/)**: ビッグ データ分析アプリケーションのパフォーマンスを向上させるメモリ内処理をサポートする並列処理フレームワークです。Spark は、SQL、ストリーミング データ、機械学習に対応します。 [HDInsight での Apache Spark の概要](../spark/apache-spark-overview.md)に関する記事を参照してください。
* **[Apache HBase](http://hbase.apache.org/)**: Hadoop 上に構築された NoSQL データベースです。数十億行 x 数百万列に達する可能性のある大量の非構造化データや半構造化データへのランダム アクセスと厳密な整合性を実現します。 [HDInsight での HBase の概要](../hbase/apache-hbase-overview.md)に関する記事を参照してください。
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: 並列分散 R プロセスをホストおよび管理するためのサーバーです。 データ サイエンティスト、統計学者、R プログラマーは、HDInsight でのスケーラブルで分散型の分析手法にオンデマンドでアクセスできるようになります。 [HDInsight の R Server の概要](../r-server/r-server-overview.md)に関するページを参照してください。
* **[Apache Storm](https://storm.incubator.apache.org/)**: 大量のデータ ストリームを高速処理するための分散型リアルタイム計算システムです。 Storm は、HDInsight で管理されるクラスターとして提供されます。 「 [HDInsight (Hadoop) での Storm と HBase を使ったセンサー データの分析](../storm/apache-storm-sensor-data-analysis.md)」を参照してください。
* **[Apache Interactive Query プレビュー (別名: Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: 対話型で高速な Hive クエリを実現するメモリ内キャッシュです。 [HDInsight での対話型クエリの使用](../interactive-query/apache-interactive-query-get-started.md)に関する記事を参照してください。
* **[Apache Kafka](https://kafka.apache.org/)**: ストリーミング データ パイプラインおよびアプリケーションを構築するためのオープン ソースのプラットフォームです。 Kafka には、データ ストリームの発行とサブスクライブを可能にするメッセージ キュー機能も用意されています。 「[HDInsight での Apache Kafka の概要](../kafka/apache-kafka-introduction.md)」を参照してください。

## <a name="open-source-components-in-hdinsight"></a>HDInsight のオープン ソース コンポーネント

Azure HDInsight では、Hadoop、Spark、Hive、LLAP、Kafka、Storm、HBase、R をはじめとするオープン ソースのフレームワークを使ってクラスターを作成できます。これらのクラスターには、[Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)、[Avro](http://avro.apache.org/docs/current/spec.html)、[Hive](http://hive.apache.org)、[HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/)、[Mahout](https://mahout.apache.org/)、[MapReduce](http://wiki.apache.org/hadoop/MapReduce)、[YARN](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)、[Phoenix](http://phoenix.apache.org/)、[Pig](http://pig.apache.org/)、[Sqoop](http://sqoop.apache.org/)、[Tez](http://tez.apache.org/)、[Oozie](http://oozie.apache.org/)、[ZooKeeper](http://zookeeper.apache.org/) など、各クラスターに備わっている他のオープン ソース コンポーネントが最初から付属しています。  


## <a name="programming-languages-on-hdinsight"></a>HDInsight のプログラミング言語
HDInsight クラスター (Spark、HBase、Kafka、Hadoop、およびその他のクラスター) は多数のプログラミング言語をサポートしますが、既定でインストールされないものがあります。 既定でインストールされないライブラリ、モジュール、またはパッケージは、[スクリプト アクションを使用してコンポーネントをインストール](../hdinsight-hadoop-script-actions-linux.md)します。 

### <a name="default-programming-language-support"></a>既定のプログラミング言語のサポート
既定では、HDInsight クラスターは以下をサポートします。

* Java
* Python

[スクリプト アクション](../hdinsight-hadoop-script-actions-linux.md)を使用して他の言語をインストールできます。

### <a name="java-virtual-machine-jvm-languages"></a>Java 仮想マシン (JVM) 言語
Java 以外の多くの言語を Java 仮想マシン (JVM) で実行できます。ただし、一部の言語では、クラスターに追加コンポーネントをインストールする必要があります。

次の JVM ベースの言語が HDInsight クラスターでサポートされます。

* Clojure
* Jython (Java 用の Python)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop 固有言語
HDInsight クラスターでは、Hadoop テクノロジ スタックに固有の次の言語をサポートしています。

* Pig ジョブ用の Pig Latin
* Hive ジョブ用の HiveQL と SparkSQL

## <a name="business-intelligence-on-hdinsight"></a>HDInsight のビジネス インテリジェンス
Power Query アドインまたは Microsoft Hive ODBC ドライバーを使用すれば、使い慣れたビジネス インテリジェンス (BI) ツールを HDInsight と連携して、データの取得、分析、レポート生成を行うことができます。

* [Azure HDInsight のデータ視覚化ツールを使用する Apache Spark BI](../spark/apache-spark-use-bi-tools.md)
* [Azure HDInsight の Microsoft Power BI で Hive データを視覚化する](apache-hadoop-connect-hive-power-bi.md)。 
* [Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Power Query を使用した Excel から Hadoop への接続](apache-hadoop-connect-excel-power-query.md): Microsoft Power Query for Excel を使用して、HDInsight クラスターのデータを格納する Azure ストレージ アカウントに Excel を接続する方法を説明します。 Windows ワークステーションが必要です。 
* [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](apache-hadoop-connect-excel-hive-odbc-driver.md): Microsoft Hive ODBC ドライバーを使用して、HDInsight からデータをインポートする方法について説明します。 Windows ワークステーションが必要です。 
* [Microsoft Cloud プラットフォーム](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx)- Power BI for Office 365 の詳細、SQL Server 評価版のダウンロード、SharePoint Server 2013 と SQL Server BI のセットアップについて説明します。
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>次の手順

* [HDInsight での Hadoop の使用](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight での Spark の使用](../spark/apache-spark-jupyter-spark-sql.md)
* [HDInsight での Kafka の使用](../kafka/apache-kafka-get-started.md)
* [HDInsight での Storm の使用](../storm/apache-storm-tutorial-get-started-linux.md)
* [HDInsight での HBase の使用](../hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight での対話型クエリ (LLAP) の使用](../interactive-query/apache-interactive-query-get-started.md)
* [HDInsight での R Server の使用](../r-server/r-server-get-started.md)
* [HDInsight クラスターの管理](../hdinsight-administer-use-portal-linux.md)
* [HDInsight クラスターのセキュリティ](../domain-joined/apache-domain-joined-introduction.md)
* [HDInsight クラスターの監視](../hdinsight-hadoop-oms-log-analytics-tutorial.md)


[component-versioning]: ../hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/