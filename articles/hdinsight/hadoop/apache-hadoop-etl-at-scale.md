---
title: 大規模な抽出、変換、および読み込み (ETL) - Azure HDInsight
description: HDInsight で Apache Hadoop によって抽出、変換、および読み込みを使用する方法について説明します。
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: ee989ccbb2e441256bec71781c538c7761fc7b88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232242"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>大規模な抽出、変換、および読み込み (ETL)

抽出、変換、および読み込み (ETL) は、さまざまなソースからデータを取得するプロセスです。 標準の場所に収集され、クリーンアップおよび処理されます。 最終的にデータストアに読み込まれ、そこからクエリが可能になります。 従来の ETL プロセスは、データをインポートし、所定の場所でクリーニングした後、リレーショナル データ エンジンに格納します。 HDInsight では、さまざまな Apache Hadoop 環境コンポーネントで、大規模な ETL がサポートされます。

ETL プロセスでの HDInsight の使用は、次のパイプラインにまとめることができます。

![大規模な HDInsight ETL の概要](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

次のセクションで、ETL の各フェーズとその関連コンポーネントについて説明します。

## <a name="orchestration"></a>オーケストレーション

オーケストレーションは、ETL パイプラインのすべてのフェーズにまたがります。 HDInsight の ETL ジョブには、多くの場合、相互に連携して動作する複数の異なる製品が含まれます。  Hive を使用してデータの一部がクリーニングされ、Pig を使用して別の部分がクリーンアップされることがあります。  Azure Data Factory を使用して、Azure Data Lake Store から Azure SQL Database にデータを読み込むことがあります。

オーケストレーションは、適切なタイミングで適切なジョブを実行するために必要です。

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie は、Hadoop ジョブを管理するワークフロー調整システムです。 Oozie は HDInsight クラスター内で実行され、Hadoop スタックと統合されます。 Oozie は、Apache Hadoop MapReduce、Apache Pig、Apache Hive、および Apache Sqoop の Hadoop ジョブをサポートします。 Oozie は、Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。

詳細については、「[Apache Hadoop で Apache Oozie を使用して Linux ベースの Azure HDInsight でワークフローを定義して実行する](../hdinsight-use-oozie-linux-mac.md)」を参照してください。 「[データ分析パイプラインを運用化する](../hdinsight-operationalize-data-pipeline.md)」も参照してください。

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory では、オーケストレーション機能をサービスとしてのプラットフォームの形で提供します。 クラウドベースのデータ統合サービスであり、クラウドでデータ主導型のワークフローを作成できます。 データ移動とデータ変換を調整し、自動化するためのワークフローです。

Azure Data Factory を使用して、以下を実行できます。

1. 各種のデータ ストアからデータを取り込むデータ主導型のワークフロー (パイプライン) を作成し、スケジュールを設定する。
2. Azure HDInsight Hadoop などのコンピューティング サービスを使用してデータを処理および変換する。 または、Spark、Azure Data Lake Analytics、Azure Batch、Azure Machine Learning を使用する。
3. ビジネス インテリジェンス (BI) アプリケーションから利用できるよう、Azure SQL Data Warehouse などのデータ ストアに出力データを公開する。

Azure Data Factory の詳細については、[こちらのドキュメント](../../data-factory/introduction.md)を参照してください。

## <a name="ingest-file-storage-and-result-storage"></a>ファイル ストレージと結果ストレージの取り込み

ソース データ ファイルは、通常、Azure Storage または Azure Data Lake Storage 内の場所に読み込まれます。 ファイルの形式は任意ですが、通常は CSV などのフラット ファイルです。

### <a name="azure-storage"></a>Azure Storage

Azure Storage には、固有の適応性ターゲットがあります。 「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../../storage/blobs/scalability-targets.md)」を参照してください。 大半の分析ノードでは、Azure Storage は、多数の小さなファイルを処理する場合に最善のスケーリングを行います。  Azure Storage では、ファイルのサイズに関係なく (指定した制限内である限り)、同じパフォーマンスが保証されます。  この保証により、テラバイトのデータを格納し、そのデータのサブセットを使用する場合でもすべてを使用する場合でも、一貫性のあるパフォーマンスを得ることができます。

Azure Storage には、さまざまな種類の BLOB があります。  "*追加 BLOB*" は、Web ログやセンサー データを格納するための優れたオプションです。  

複数の BLOB を数多くのサーバーに分散して、アクセスをスケールアウトすることができます。 ただし、単一の BLOB は単一サーバーでのみ提供できます。 BLOB は BLOB コンテナーに論理的にグループ化できますが、このグループ化によってパーティション分割は影響を受けません。

Azure Storage には、BLOB ストレージ用の WebHDFS API レイヤーもあります。  HDInsight のすべてのサービスから、データのクリーニングとデータ処理のために Azure Blob Storage 内のファイルにアクセスできます。 これらのサービスで Hadoop 分散ファイル システム (HDFS) を使用する方法と似ています。

データは、通常は、PowerShell、Azure Storage SDK、または AZCopy を使用して Azure Storage に取り込まれます。

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) は、マネージド ハイパースケール リポジトリです。 HDFS と互換性のある分析データのリポジトリです。  ADLS では、HDFS に似た設計パラダイムが使用されます。 ADLS では、容量の合計と個々のファイルのサイズに対して無制限で適応できます。 ADLS は、大きなファイルを複数のノードに格納できるため、大きなファイルの操作に適しています。  ADLS でのデータのパーティション分割は、バックグラウンドで行われます。  数百のテラバイトのデータに対する読み取りと書き込みを効率的に行う数千の同時実行される Executor によって、分析ジョブの実行で非常に高いスループットを得ることができます。

通常、データは Azure Data Factory を使用して ADLS に取り込まれます。 または、ADLS SDK、AdlCopy サービス、Apache DistCp、Apache Sqoop も使用できます。  どのサービスを使用するかは、データの場所に大きく依存します。  現在データが既存の Hadoop クラスターにある場合は、Apache DistCp、AdlCopy Service、または Azure Data Factory を使用します。  データが Azure Blob Storage にある場合は、Azure Data Lake Storage .NET SDK、Azure PowerShell、または Azure Data Factory を使用できます。

ADLS は、Azure Event Hub または Apache Storm を使用したイベントの取り込み用にも最適化されています。

#### <a name="considerations-for-both-storage-options"></a>両方のストレージ オプションに関する考慮事項

テラ バイトの範囲のデータセットのアップロードでは、ネットワークの待機時間が大きな問題になる可能性があります。これはデータがオンプレミスの場所から送信される場合に特に当てはまります。  このような場合は、次のオプションを使用できます。

* Azure ExpressRoute: Azure ExpressRoute を使用すると、Azure データセンターとオンプレミスのインフラストラクチャ間のプライベート接続を作成できます。 これらの接続により、大量のデータを転送するための信頼性の高いオプションが提供されます。 詳細については、 [Azure ExpressRoute のドキュメント](../../expressroute/expressroute-introduction.md)をご覧ください。

* データの "オフライン" アップロード。 [Azure Import/Export サービス](../../storage/common/storage-import-export-service.md)を使用して、データが格納されたハード ディスク ドライブを Azure データ センターに発送できます。 データはまず Azure Storage BLOB にアップロードされます。 その後、Azure Data Factory または AdlCopy ツールを使用して、Azure Storage BLOB から Data Lake Storage にデータをコピーできます。

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW は、準備された結果を格納するための最適な選択肢です。  Azure HDInsight を使用して、Azure SQL DW 用のサービスを実行できます。

Azure SQL Data Warehouse (SQL DW) は、分析ワークロード用に最適化されたリレーショナル データベース ストアです。  Azure SQL DW は、パーティション分割されたテーブルに基づいてスケーリングを行います。  テーブルは、複数のノードにパーティション分割できます。  Azure SQL DW ノードは作成時に選択されます。  それらは後でスケーリングできますが、データ移動が必要になるのはアクティブ プロセスです。 詳細については、[SQL Data Warehouse でのコンピューティングの管理](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)に関する記事を参照してください。

### <a name="apache-hbase"></a>Apache HBase

Apache HBase は、Azure HDInsight で利用可能なキー値ストアです。  Apache HBase は、オープン ソースの NoSQL データベースで、Hadoop 上に構築され、Google BigTable を模範にしています。 HBase では、大量の非構造化データと半構造化データに対する効率の良いランダム アクセスと強力な一貫性が提供されます。 列ファミリ別に編成されたスキーマレス データベース内のデータです。

データはテーブルの行内に格納され、行内のデータは列ファミリによってグループ化されます。 HBase はスキーマレス データベースです。 列および列に格納されるデータ型は、使用する前に定義する必要はありません。 オープン ソース コードは、直線的な拡張により何千ものノード上でペタバイト級のデータを扱うことができます。 HBase は、Hadoop 環境の分散アプリケーションによって提供されるデータ冗長性やバッチ処理などの機能を利用できます。

HBase は、今後の分析のためにセンサー データとログ データを格納するための優れた宛先です。

HBase の適応性は、HDInsight クラスター内のノードの数に依存します。

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database と Azure Database

Azure では、サービスとしてのプラットフォーム (PAAS) として、次の 3 つの異なるリレーショナル データベースを用意しています。

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) は、Microsoft SQL Server の実装です。 パフォーマンスの詳細については、「[Azure SQL Database のパフォーマンスのチューニング](../../sql-database/sql-database-performance-guidance.md)」を参照してください。
* [Azure Database for MySQL](../../mysql/overview.md)  は、Oracle MySQL の実装です。
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) は、PostgreSQL の実装です。

これらの製品はスケールアップされます。つまり、CPU とメモリを追加することによってスケーリングされます。  I/O パフォーマンスを向上させるため、製品と共にプレミアム ディスクを使用することも選択できます。

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services (AAS) は、意思決定支援とビジネス分析に使用される分析データ エンジンです。 AAS では、Power BI などのビジネス レポートとクライアント アプリケーション用に分析データを提供します。 また、Excel、Reporting Services レポート、およびその他のデータ視覚化ツール用にも提供します。

分析キューブは、個別のキューブの階層を変更することによって拡張できます。  詳しくは、「[Azure Analysis Services の価格](https://azure.microsoft.com/pricing/details/analysis-services/)」をご覧ください。

## <a name="extract-and-load"></a>抽出と読み込み

Azure 内にデータが存在すれば、多数のサービスを使用してデータを抽出し、他の製品に読み込むことができます。  HDInsight では、Sqoop と Flume をサポートします。

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop は、構造化データ ソース、半構造化データ ソース、および非構造化データ ソース間でデータを効率的に転送するように設計されたツールです。

Sqoop では、MapReduce を使用してデータのインポートとエクスポートを実行し、並列操作とフォールト トレランスを提供しています。

### <a name="apache-flume"></a>Apache Flume

`Apache Flume` は、大量のログ データを効率的に収集、集計、および移動するために使用できる信頼性の高い分散サービスです。 Flume は、ストリーミング データ フローに基づく柔軟なアーキテクチャを備えています。 Flume は、堅牢でフォールト トレラントであり、チューニング可能な信頼性メカニズムと多数のフェールオーバーと回復メカニズムを備えています。 Flume は、オンライン分析アプリケーションで使用できるシンプルな拡張可能データ モデルを使用しています。

Apache Flume は、Azure HDInsight では使用できません。  オンプレミスの Hadoop インストールでは、Flume を使用して、Azure Storage Blob または Azure Data Lake Storage にデータを送信できます。  詳細については、[HDInsight での Apache Flume の使用](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/)に関する記事を参照してください。

## <a name="transform"></a>変換

選択した場所にデータが存在すれば、データのクリーニング、結合、または特定の使用パターンに合わせた準備を行う必要があります。  Hive、Pig、および Spark SQL は、すべてがその種の作業を行うための適切な選択肢です。  それらはすべて HDInsight でサポートされています。

## <a name="next-steps"></a>次のステップ

* [ETL ツールとして Apache Hive を使用する](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Azure SQL Database から Apache Hive テーブルにデータを移動する](./apache-hadoop-use-sqoop-mac-linux.md)
