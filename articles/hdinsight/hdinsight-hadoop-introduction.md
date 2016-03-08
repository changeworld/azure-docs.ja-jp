<properties
	pageTitle="クラウドでの Hadoop とは HDInsight の概要 | Microsoft Azure"
	description="クラウドでの Hadoop と、HDInsight でのその管理方法について Hadoop コンポーネントとビッグ データ分析について紹介します。"
	keywords="ビッグ データの分析,hadoop の概要,hadoop とは,クラウドの hadoop"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/27/2016"
   ms.author="cgronlun"/>


# クラウドでの Hadoop とは HDInsight での Hadoop コンポーネントを使用したビッグ データ分析について説明します

ここでは、Azure HDInsight の Hadoop、エコシステム、ビッグ データについて概説します。HDInsight の Hadoop の概要、Hadoop のコンポーネント、一般的な用語、ビッグ データ分析のためのシナリオについて説明します。 また、HDInsight のクラウドで Hadoop を使用するためのチュートリアル、ドキュメント、およびリソースについても紹介します。

## HDInsight のクラウドでの Hadoop とは

Azure HDInsight は、管理された Apache Hadoop クラスターをクラウドにデプロイしてプロビジョニングするサービスです。ビッグ データの処理、分析、およびレポート生成を目的として設計された、信頼性と可用性の高いソフトウェア フレームワークを提供します。HDInsight は、**Hortonworks Data Platform (HDP)** Hadoop ディストリビューションを使用します。Hadoop という語は、多くの場合、複数のコンポーネントで構成された Hadoop のエコシステム全体を指し、このエコシステムには Apache HBase、Apache Spark、Apache Storm に加え、Hadoop という領域に関係するその他のテクノロジが含まれます。詳細については、「[HDInsight の Hadoop エコシステムの概要](#overview)」を参照してください。


## ビッグ データとは
ビッグ データとは、絶えず急速なペースで増加し、さまざまな非構造化形式と意味的文脈から成る巨大なデータ群を指しています。

ビッグ データは、Twitter フィードのテキストから、工業機器のセンサー情報、オンライン カタログでの消費者の閲覧、購入行動に関する情報に至るまでの、大規模なデジタル情報を表します。ビッグ データの種類には、履歴データ (つまり、保存されたデータ) とリアルタイム データ (つまり、ソースから直接ストリーミングされるデータ) があります。

ビッグ データから実用的な情報や洞察を導くうえで必要なのは、関連するデータを収集して適切な質問をすることだけではありません。データは、アクセス可能であり、クリーンアップと分析を行った後実用的な方法で定義される必要があります。そこで、HDInsight の Hadoop のビッグ データ分析の出番となります。


## <a name="overview"></a>HDInsight の Hadoop エコシステムの概要

HDInsight は、急激に拡大を続けている Apache Hadoop テクノロジ スタックの Microsoft Azure 上のクラウド実装であり、ビッグ データ分析用の信頼できるソリューションです。これには、Apache Spark、HBase、Storm、Pig、Hive、Sqoop、Oozie、Ambari などの実装が含まれます。また、HDInsight は、Power BI、Excel、SQL Server Analysis Services、SQL Server Reporting Services などのビジネス インテリジェンス (BI) ツールとも統合されます。

### Linux 上のクラスター

Azure HDInsight は、**Linux** のクラウドの Hadoop クラスターをデプロイおよびプロビジョニングします。詳細については、下の表を参照してください。

カテゴリ | Linux での Hadoop
---------| -------------------
**クラスターの OS** | Ubuntu 12.04 Long Term Support (LTS)
**クラスターの種類** | Hadoop、Spark、HBase、Storm
**デプロイメント** | Azure ポータル、Azure CLI、Azure PowerShell
**クラスター UI** | Ambari
**リモート アクセス** | Secure Shell (SSH)、REST API、ODBC、JDBC



### Hadoop、HBase、Spark、Storm、およびカスタマイズしたクラスター

HDInsight には、Apache Hadoop、Spark、HBase、または Storm というクラスター構成が用意されています。また、[スクリプト アクションを使用してクラスターをカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)することもできます。

* **Hadoop** ("クエリ" ワークロード): [HDFS](#HDFS) を使用した信頼できるデータ ストレージと、データの処理および分析を同時に行うシンプルな [MapReduce](#mapreduce) プログラミング モデルを利用できます。

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: ビッグ データ分析アプリケーションのパフォーマンスを向上させるメモリ内の処理をサポートする、並列処理のフレームワークです。Spark は SQL、ストリーミング データ、機械学習に対応します。「[Overview: What is Apache Spark in HDInsight? (概要: HDInsight での Apache Spark とは)](hdinsight-apache-spark-overview.md)」を参照してください。

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** ("NoSQL" ワークロード): Hadoop 上に構築される NoSQL データベースです。数十億行 x 数百万列に達するような大量の非構造化データと半構造化データへのランダム アクセスが可能で、強固な一貫性が提供されます。「[HDInsight HBase の概要](hdinsight-hbase-overview.md)」を参照してください。

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** ("ストリーム" ワークロード) は、大量のデータ ストリームを高速処理するための分散型リアルタイム計算システムです。Storm は、HDInsight で管理されるクラスターとして提供されます。「[HDInsight (Hadoop) での Storm と HBase を使ったセンサー データの分析](hdinsight-storm-sensor-data-analysis.md)」を参照してください。

#### カスタマイズ スクリプトの例

スクリプト アクションは、クラスターのプロビジョニング中に実行されるスクリプトです。スクリプト アクションを使用して、クラスターに追加コンポーネントをインストールできます。Linux ベースのクラスターでは Bash スクリプトです。

HDInsight チームによって提供されるスクリプトの例は次のとおりです。

* [Hue](hdinsight-hadoop-hue-linux.md): クラスターとの情報のやり取りに使用される一連の Web アプリケーション。Linux クラスターのみ。

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): 人またはモノ間の関係をモデル化するグラフの処理。

* [R](hdinsight-hadoop-r-scripts-linux.md): 機械学習で使用する統計計算用のオープン ソースの言語および環境です。

* [Solr](hdinsight-hadoop-solr-install-linux.md): データに対してフルテキスト検索ができるエンタープライズ規模の検索プラットフォームです。

独自のスクリプト アクションを開発する方法の詳細については、「[HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」を参照してください。

## Hadoop コンポーネントおよびユーティリティとは

HDInsight クラスターには、次のコンポーネントとユーティリティが含まれています。

* **[Ambari](#ambari)**: クラスターのプロビジョニング、管理、監視、およびユーティリティ。

* **[Avro](#avro)** (Microsoft .NET Library for Avro) - Microsoft.NET 環境向けのデータ シリアル化。

* **[Hive と HCatalog](#hive)**: 構造化照会言語 (SQL) に似たクエリ処理、テーブルおよびストレージの管理レイヤー。

* **[Mahout](#mahout)**: 機械学習。

* **[MapReduce](#mapreduce)**: Hadoop 分散処理およびリソース管理用のレガシー フレームワーク。次世代リソース フレームワークの [YARN](#yarn) を参照してください。

* **[Oozie](#oozie)**: ワークフロー管理。

* **[Phoenix](#phoenix)**: HBase の上のリレーショナル データベース レイヤー。

* **[Pig](#pig)** - MapReduce 変換のためのシンプルなスクリプト作成。

* **[Sqoop](#sqoop)** - データのインポートとエクスポート。

* **[Tez](#tez)**: データ量の多い処理を効率的かつ大規模に実行可能。

* **[YARN](#yarn)**: Hadoop コア ライブラリの一部であり、次世代の MapReduce ソフトウェア フレームワーク。

* **[ZooKeeper](#zookeeper)** - 分散システムにおけるプロセスの調整。

> [AZURE.NOTE] 特定のコンポーネントとバージョン情報については、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][component-versioning]」をご覧ください。

### <a name="ambari"></a>Ambari

Apache Ambari は、Apache Hadoop クラスターのプロビジョニング、管理、および監視を目的としています。演算子ツールの直観的なコレクションおよび Hadoop の複雑さが見えないようにする信頼性の高い、一連の API が含まれており、クラスターの操作を単純化しています。Linux ベースの HDInsight クラスターは、Ambari Web UI と Ambari REST API の両方を提供し、Windows ベースのクラスターは、REST API のサブセットを提供します。HDInsight クラスターの Ambari ビューでは、プラグイン UI 機能を使用できます。

「[Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」、「[Ambari API を使用した HDInsight の Hadoop クラスターの監視](hdinsight-monitor-use-ambari-api.md)」、「<a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API リファレンス</a>」をご覧ください。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)

Microsoft .NET Library for Avro は、Microsoft.NET 環境向けのシリアル化のために、Apache Avro のコンパクトなバイナリ データ交換形式を実装します。Avro は、<a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。これは、ある言語でシリアル化されたデータを別の言語で読むことができることを意味します。この形式の詳細については、「<a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro の仕様</a>」をご覧ください。Avro ファイルの形式は、分散 MapReduce プログラミング モデルをサポートしています。ファイル内の任意のポイントを検索し、特定のブロックから読み取りを開始できるという意味で、ファイルは "分割可能" です。詳細については、「[Microsoft .NET Library for Avro を使用したデータのシリアル化](hdinsight-dotnet-avro-serialization.md)」を参照してください。


### <a name="hdfs"></a>HDFS

Hadoop 分散ファイル システム (HDFS) は、MapReduce と YARN と共に Hadoop エコシステムの中核を構成する分散ファイル システムです。HDFS は、HDInsight の Hadoop クラスターの標準ファイル システムです。

### <a name="hive"></a>Hive と HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> は、Hadoop に組み込まれているデータ ウェアハウス ソフトウェアで、HiveQL と呼ばれる SQL に似た言語を使って分散ストレージ内の大量のデータセットに対するクエリと管理を可能にします。Hive は、Pig と同様、MapReduce 上の抽象化部分です。Hive を実行すると、クエリを一連の MapReduce ジョブに変換します。Hive は、概念的には Pig よりもリレーショナル データベース管理システムに近いため、より構造化されたデータでの使用に適しています。非構造化データを操作する場合は Pig が適しています。「[HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)」をご覧ください。

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> は、Hadoop のテーブルおよびストレージを管理するレイヤーであり、ユーザーにデータのリレーショナル ビューを提供します。HCatalog では、Hive SerDe (シリアライザー - デシリアライザー) を書き込める任意の形式で、ファイルの読み取りおよび書き込みを実行できます。

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> は、Hadoop 上で実行される機械学習アルゴリズムのスケーラブルなライブラリです。統計の原則を使用して、機械学習アプリケーションは、データから学習すること、および過去の結果から将来の動作を決めることをシステムに教えます。「[HDInsight (Hadoop) で Apache Mahout を使用して映画のリコメンデーションを生成する](hdinsight-mahout.md)」を参照してください。

### <a name="mapreduce"></a>MapReduce
Hadoop MapReduce は、大規模なデータセットを並列でバッチ処理するアプリケーションを記述するための Hadoop 用のレガシー ソフトウェア フレームワークです。MapReduce ジョブは、大規模なデータセットを分割し、データを処理のためにキーと値のペアに整理します。

[YARN](#yarn) は、Hadoop の次世代リソース マネージャーおよびアプリケーション フレームワークであり、MapReduce 2.0 と呼ばれます。MapReduce ジョブは YARN で実行されます。

MapReduce の詳細については、Hadoop Wiki で「<a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a>」をご覧ください。

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> は Hadoop ジョブを管理するワークフローと調整システムです。Hadoop スタックと統合されていて、MapReduce、Pig、Hive、Sqoop の Hadoop ジョブをサポートしています。Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。[Hadoop での時間ベースの Oozie コーディネーターの使用方法](hdinsight-use-oozie-coordinator-time.md)を参照してください。

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> は、HBase 上のリレーショナル データベース レイヤーです。Phoenix には JDBC ドライバーが含まれており、ユーザーはこれを使用して SQL テーブルを直接クエリおよび管理できます。Phoenix は、クエリおよびその他のステートメントをネイティブ NoSQL の API 呼び出しに変換します。MapReduce を使用しないため、NoSQL ストア上でアプリケーションを高速化できます。「[HBase クラスターでの Apache Phoenix および SQuirrel の使用](hdinsight-hbase-phoenix-squirrel.md)」を参照してください。


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> は、Pig Latin と呼ばれるシンプルなスクリプト言語を使って大規模なデータセットに対して複雑な MapReduce 変換を実行することを可能にする高レベル プラットフォームです。Pig は、Hadoop 内で実行できるように Pig Latin スクリプトを変換します。ユーザー定義関数 (UDF) を作成して Pig Latin を拡張できます。「[HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)」を参照してください。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> は、リレーショナル データベース (SQL など) やその他の構造化データ ストアと Hadoop との間でデータを効率的に一括転送するためのツールです。「[HDInsight の Hadoop での Sqoop の使用](hdinsight-use-sqoop.md)」を参照してください。

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> は、一般的なデータ処理で構成される複雑な非循環グラフを実行する Hadoop YARN 上に構築されたアプリケーション フレームワークです。これは、MapReduce の後を継ぐ柔軟で強力なフレームワークであり、Hive などのデータ量の多い処理を、より効率的かつ大規模に実行することができます。[Hive と HiveQL の使用に関するページの『パフォーマンスを改善するための Apache Tez の使用』](hdinsight-use-hive.md#usetez)を参照してください。

### <a name="yarn"></a>YARN
Apache YARN は次世代の MapReduce (MapReduce 2.0、または MRv2) であり、優れたスケーラビリティとリアルタイム処理によって MapReduce のバッチ処理を超えるデータ処理シナリオをサポートします。YARN は、リソース管理および分散アプリケーション フレームワークを提供します。MapReduce ジョブは YARN で実行されます。

YARN の詳細については、「<a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN) (Apache Hadoop の次世代の MapReduce (YARN))</a>」をご覧ください。


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> は、データ レジスタ (znode) の共有階層名前空間を使用して、大規模な分散システムのプロセスを調整します。znode には、状態、場所、構成など、プロセスを調整するのに必要な少量のメタ情報が含まれます。

## HDInsight のプログラミング言語

HDInsight クラスター (Hadoop、HBase、Storm、および Spark クラスター) は多数のプログラミング言語をサポートしますが、既定でインストールされないものがあります。既定でインストールされないライブラリ、モジュール、またはパッケージ コンポーネントは、スクリプト操作を使用してインストールします。[HDInsight でのスクリプト アクションの開発](hdinsight-hadoop-script-actions-linux.md)

### 既定のプログラミング言語のサポート

既定では、HDInsight クラスターは以下をサポートします。

* Java

* Python

スクリプト アクションを使用して他の言語をインストールできます。[ HDInsight でスクリプト アクションを開発します](hdinsight-hadoop-script-actions-linux.md)。

### Java 仮想マシン (JVM) 言語

Java 以外の多くの言語を Java 仮想マシン (JVM) を使用して実行できます。ただし、一部の言語では、クラスターに追加コンポーネントをインストールする必要があります。

次の JVM ベースの言語が HDInsight クラスターでサポートされます。

* Clojure

* Jython (Java 用の Python)

* Scala

### Hadoop 固有言語

HDInsight クラスターでは、Hadoop エコシステムに固有の次の言語をサポートします。

* Pig ジョブ用の Pig Latin

* Hive ジョブ用の HiveQL と SparkSQL


## <a name="advantage"></a>クラウドで Hadoop を使用するメリット

HDInsight の Hadoop は、Azure クラウド エコシステムの一部としていくつかのメリットを提供します。その一部を次に示します。

* Hadoop クラスターの自動プロビジョニング。HDInsight クラスターは、手動で Hadoop クラスターを構成するよりも作成がはるかに簡単です。詳細については、「[HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」に関するページをご覧ください。

* 最新の Hadoop コンポーネント。詳細については、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][component-versioning]」を参照してください。

* クラスターの高い可用性と信頼性。「[HDInsight における Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)」を参照してください。

* Hadoop 互換オプションの Azure BLOB ストレージによって実現される効率的で経済的なデータ ストレージ。詳細については、「[HDInsight の Hadoop での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」を参照してください。

* [Web アプリ](../documentation/services/app-service/web/) や [SQL Database](../documentation/services/sql-database/) などの他の Azure サービスとの統合。

* 低い導入コスト。[無料評価版](/pricing/free-trial/)から始めることができます。「[HDInsight の料金詳細](/pricing/details/hdinsight/)」も参照してください。


HDInsight の Hadoop を使うその他のメリットについては、[HDInsight 向けの Azure の機能のページ][marketing-page]を参照してください。



## <a id="resources"></a>ビッグ データ分析、Hadoop、HDInsight に関する情報の収集に役立つリソース

クラウドおよびビッグ データ分析での Hadoop への入門は、次のリソースを使用して作成されました。


### HDInsight の Hadoop ドキュメント

* [HDInsight ドキュメント](https://azure.microsoft.com/documentation/services/hdinsight/) - Azure HDInsight のドキュメント ページ。記事やビデオなどのリソースへのリンクも記載しています。

* [Linux での HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md) - Linux に HDInsight Hadoop クラスターをプロビジョニングし、サンプルの Hive クエリを実行するためのクイック スタート チュートリアル。

* [HDInsight での Linux ベースの Storm の概要](hdinsight-apache-storm-tutorial-get-started-linux.md): HDInsight クラスターに Storm をプロビジョニングし、サンプルの Storm トポロジを実行するためのクイック スタート チュートリアル。

* [Linux での HDInsight のプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md): Azure ポータル、Azure CLI、または Azure PowerShell を使用して、Linux に HDInsight Hadoop クラスターをプロビジョニングする方法について説明します。

* [Linux での HDInsight の使用](hdinsight-hadoop-linux-information.md) - Azure でプロビジョニングされた Hadoop Linux クラスターの使用に関するクイック ヒントを示します。

* [Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md) - Ambari Web または Ambari REST API を使用して、HDInsight クラスターの Linux ベースの Hadoop を監視および管理する方法を説明します。


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a> - 大規模なデータ セットを複数のコンピューター クラスターで分散処理するためのフレームワークとなる Apache Hadoop ソフトウェア ライブラリについて説明します。

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a> - Hadoop アプリケーションで使用する主要なストレージ システムである Hadoop 分散ファイル システムのアーキテクチャと設計について説明します。

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce チュートリアル</a> - 複数の大規模な計算ノード クラスターを使用して大量のデータを高速で並列処理する Hadoop アプリケーションを作成するためのプログラミング フレームワークについて説明します。

### Azure 上の SQL Database:

* [Azure SQL Database](/documentation/services/sql-database/): SQL Database に関するドキュメント、チュートリアル、ビデオです。

* [Azure ポータル上の SQL Database](sql-database-manage-portal.md) - クラウドの SQL Database を管理するための軽量で操作しやすいデータベース管理ツール。

* [Adventure Works for SQL Database](http://msftdbprodsamples.codeplex.com/releases/view/37304) - SQL Database のサンプル データベースのダウンロード ページです。

### Microsoft ビジネス インテリジェンス (Windows 上の HDInsight 用)

Power Query アドインまたは Microsoft Hive ODBC ドライバーを使用すれば、Excel、PowerPivot、SQL Server Analysis Services、SQL Server Reporting Services などの使い慣れたビジネス インテリジェンス (BI) ツールを HDInsight と連携して、データの取得、分析、レポート生成を行うことができます。

これらの BI ツールは、ビッグ データ分析に有用です。

* [Power Query を使用した Excel から Hadoop への接続](hdinsight-connect-excel-power-query.md) - Microsoft Power Query for Excel を使用して、HDInsight クラスターに関連付けられたデータを格納する Azure Storage アカウントに Excel を接続する方法を説明します。

* [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](hdinsight-connect-excel-hive-ODBC-driver.md) - Microsoft Hive ODBC ドライバーを使用して、HDInsight からデータをインポートする方法を説明します。

* [Microsoft Cloud プラットフォーム](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx) - Power BI for Office 365 の詳細、SQL Server 評価版のダウンロード、SharePoint Server 2013 と SQL Server BI のセットアップについて説明します。

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">SQL Server Analysis Services の詳細</a>。

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">SQL Server Reporting Services の詳細</a>。


### Hadoop のビッグ データ分析のソリューションを試してみる (Windows 上の HDInsight 用)

組織のビッグ データ分析を使用して、ビジネスの洞察を得ましょう。次に例をいくつか示します。

* [Analyze HVAC センサー データ](hdinsight-hive-analyze-sensor-data.md) - HDInsight (Hadoop) で Hive を使用してセンサー データを分析し、Microsoft Excel でデータを視覚化する方法について説明します。このサンプルでは、Hive を使用して、HVAC システムによって生成された履歴データを処理し、設定した温度を正確に維持できないシステムを識別する方法について説明します。

* [Hive を HDInsight と共に使用して Web サイトのログを分析する](hdinsight-hive-analyze-website-log.md) - HDInsight で HiveQL を使用して Web サイトのログを分析し、外部 Web サイトからのアクセス数/日を調べ、ユーザーが経験した Web サイト エラーの概要を把握する方法について説明します。

* [Storm と HBase を HDInsight (Hadoop) で使用してリアルタイムでセンサー データを分析する](hdinsight-storm-sensor-data-analysis.md) - HDInsight で Storm クラスターを使用して、Azure Event Hubs からのセンサー データを処理し、処理したセンサー データをほぼリアルタイムの情報として Web ベースのダッシュボードに表示するソリューションを作成する方法について説明します。


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0302_2016-->