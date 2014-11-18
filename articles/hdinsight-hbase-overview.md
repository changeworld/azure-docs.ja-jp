<properties urlDisplayName="HDInsight HBase overview" pageTitle="HDInsight での HBase の概要 | Azure" metaKeywords="" description="HDInsight の HBase の概要、使用事例、および他のデータベース ソリューションとの比較。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight HBase の概要" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev" />

# HDInsight HBase の概要

## HBase とは

HBase は、Hadoop 上に構築された Apache オープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。HBase は、Google の BigTable をモデルとした列ファミリ指向のデータベースです。データはテーブルの行内に格納され、行内のデータは列ファミリによってグループ化されます。HBase は、列も、列内に格納されるデータの型も使用前に定義する必要がないため、スキーマレス データベースと言うことができます。2007 年に Mike Cafarella によって、オープン ソース コードが初めてリリースされています。HBase は、直線的な拡張により何千ものノード上でペタバイト級のデータを扱うことができます。また、Hadoop エコシステムの分散アプリケーションの利点であるデータの冗長性、バッチ処理などの機能を利用できます。

## Azure HDInsight HBase とは

HDInsight HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよび利用統計情報のデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。HBase と Hadoop によって、Azure のビッグ データ プロジェクトは大きな一歩を踏み出しました。特に注目したいのは、リアルタイム アプリケーションが大規模なデータセットを処理できるようになったことです。

HDInsight の実装と HBase のスケールアウト アーキテクチャにより、テーブルの自動シャーディング、読み取りと書き込みの強力な一貫性、自動フェールオーバーなどが実現します。また、メモリ内キャッシュを利用した読み取りと高スループットのストリーミング書き込みとによって、パフォーマンスも拡張されています。HDInsight HBase では、仮想ネットワーク プロビジョニングも利用できます。詳細については、「[Azure Virtual Network での HDInsight クラスターのプロビジョニング][Azure Virtual Network での HDInsight クラスターのプロビジョニング]」を参照してください。

## HDInsight HBase 内データの管理方法

HBase 内のデータは、HBase シェルから `create` `get`、`put`、および `scan` というコマンドを使用して管理できます。データの書き込みは `put` コマンド、読み取りは `get` コマンドを使用します。`scan` コマンドは、テーブルの複数の行からデータを取得するために使用します。HBase の C\# API を使用してデータを管理することもできます。これにより、HBase REST API 最上層のクライアント ライブラリを利用できます。HBase データベースは、Hive を使用して照会することもできます。これらのプログラミング モデルの基本的な説明については、「[Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)][Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)]」を参照してください。コプロセッサを利用することにより、データベースをホストするノードでデータを処理することもできます。

## シナリオ: HBase の使用事例

BigTable が開発され、さらにその機能を拡張する形で HBase が開発された本来の目的は、Web 検索に利用するためでした。検索エンジンでは、言葉を、その言葉が含まれる Web ページにマッピングするインデックスを作成するために使用されています。しかし、HBase が適する用途は他にも数多くあります。このセクションでは、その中からいくつかの使用事例を紹介します。

### 使用事例 \#1: キー値ストア

HBase は、キー値ストアとして使用できるため、メッセージ システムの管理に適しています。Facebook は、メッセージング システムに HBase を使用しており、インターネット通信データの保存と管理に効果を発揮しています。WebTable は、HBase を使用して Web ページから抽出したテーブルを検索し管理します。

### 使用事例 \#2: センサー データ

HBase は、さまざまなソースから次々と収集されるデータの取り込みに適しています。たとえば、ソーシャル分析や時系列データベースに使用されており、対話型ダッシュボードに最新の傾向やカウンターを反映させたり、監査ログ システムを管理したりできます。また、ブルームバーグのトレーダー用端末や Open Time Series Database (OpenTSDB)
にも使用されています。OpenTSDB は、収集されたサーバー システムの健全性指標を格納してアクセスできるデータベースです。

### 使用事例 \#3: リアルタイム クエリ

[Phoenix][Phoenix] は、Apache HBase 用の SQL クエリ エンジンで、JDBC ドライバーとしてアクセスされます。SQL を使用して HBase テーブルの照会と管理ができます。

### 使用事例 \#4: プラットフォームとしての HBase

HBase をデータストアとして使用することで、アプリケーションは HBase の最上層で実行できます。たとえば、Phoenix、OpenTSDB、Kiji、Titan などがあります。アプリケーションを HBase と統合することもできます。Hive、Pig、Solr、Storm、Flume、Impala、Spark、Ganglia、Drill がその例です。

## <a name="next-steps"></a>次のステップ

[Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)][Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)]

[Azure Virtual Network での HDInsight クラスターのプロビジョニング][Azure Virtual Network での HDInsight クラスターのプロビジョニング]

[Analyze real-time Twitter sentiment with HBase in HDInsight (HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析)][Analyze real-time Twitter sentiment with HBase in HDInsight (HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析)]

[Use Maven to build Java applications that use HBase with HDInsight (Hadoop) (HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築)][Use Maven to build Java applications that use HBase with HDInsight (Hadoop) (HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築)]

[C\# HBase SDK][C\# HBase SDK]

## <a name="see-also"></a>関連項目

[Apache HBase][Apache HBase]

[Bigtable: A Distributed Storage System for Structured Data (Bigtable: 構造化データ用分散ストレージ システム)][Bigtable: A Distributed Storage System for Structured Data (Bigtable: 構造化データ用分散ストレージ システム)]

  [Azure Virtual Network での HDInsight クラスターのプロビジョニング]: ../hdinsight-hbase-provision-vnet/
  [Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hbase-get-started/
  [Phoenix]: http://phoenix.apache.org/
  [Analyze real-time Twitter sentiment with HBase in HDInsight (HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析)]: ../hdinsight-hbase-analyze-twitter-sentiment/
  [Use Maven to build Java applications that use HBase with HDInsight (Hadoop) (HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築)]: ../hdinsight-hbase-build-java-maven/
  [C\# HBase SDK]: https://github.com/hdinsight/hbase-sdk-for-net
  [Apache HBase]: https://hbase.apache.org/
  [Bigtable: A Distributed Storage System for Structured Data (Bigtable: 構造化データ用分散ストレージ システム)]: http://research.google.com/archive/bigtable.html
