<properties urlDisplayName="HDInsight Introduction" pageTitle="HDInsight での Hadoop 入門: クラウドでのビッグ データ分析 | Azure" metaKeywords="" description="Azure HDInsight がどのように Apache Hadoop クラスターをクラウドで使用して、ビッグ データの管理、分析、レポート生成を支援するソフトウェア フレームワークを提供するかについて説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight での Hadoop 入門: クラウドでのビッグ データの処理と分析" authors="cgronlun" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="cgronlun" />

# HDInsight での Hadoop 入門: クラウドでのビッグ データの処理と分析

HDInsight のコンポーネント、一般的な用語、シナリオについて概説し、Hadoop を HDInsight で使用するうえで役立つリソースを紹介します。

Azure HDInsight は、Apache Hadoop クラスターをクラウドにデプロイしてプロビジョニングするためのサービスです。ビッグ データの管理、分析、レポート生成を支援するソフトウェア フレームワークとなります。Hadoop コアには、データ ストレージの信頼性を高める Hadoop 分散ファイル システム (HDFS) と、この分散システムに格納されているデータの処理と分析を同時に行うシンプルな MapReduce プログラミング モデルが用意されています。

### ビッグ データとは

"ビッグ データ" とは、絶えず急速なペースで増加し、さまざまな非構造化形式と意味的文脈から成る巨大なデータ群を指しています。

ビッグ データは、Twitter フィードのテキストから、工業機器のセンサー情報、オンライン カタログでの消費者の閲覧および購入行動に関する情報に至るまでの、大規模なデジタル情報を表します。ビッグ データの種類には、履歴データ (つまり、保存されたデータ) とリアルタイム データ (つまり、ソースから直接ストリーミングされるデータ) があります。

ビッグ データから実用的な情報や洞察を導くうえで必要なのは、適切な質問をして問題に関連するデータを収集することだけではありません。データは、アクセス可能であり、クリーンアップと分析を行った後実用的な方法で定義される必要があります。そこで、HDInsight の Hadoop の出番となります。

## この記事の内容

この記事では、HDInsight の Hadoop について概説します。この記事に含まれる内容を次に示します。

-   **[HDInsight の Hadoop エコシステムの概要:][HDInsight の Hadoop エコシステムの概要:]** HDInsight は、Azure 上に実装された Hadoop ソリューションで、Storm、HBase、Pig、Hive、Sqoop、Oozie、Ambari などの実装を提供します。また、HDInsight は、Excel、SQL Server Analysis Services、SQL Server Reporting Services などのビジネス インテリジェンス (BI) ツールとも統合されます。
-   **[クラウドで Hadoop を使用するメリット:][クラウドで Hadoop を使用するメリット:]** HDInsight による Hadoop のクラウド実装をお勧めする理由。
-   **[ビッグ データ分析のための HDInsight ソリューション:][ビッグ データ分析のための HDInsight ソリューション:]** Twitter での評判の分析から HVAC システムの有効性の分析まで、組織に役立つ HDInsight のいくつかの実用的な使用例。
-   **[ビッグ データ分析、Hadoop、HDInsight に関する情報の収集に役立つリソース:][ビッグ データ分析、Hadoop、HDInsight に関する情報の収集に役立つリソース:]** 関連情報へのリンク。

## <a name="overview"></a>HDInsight の Hadoop エコシステムの概要

Hadoop は、急激に拡大を続けているテクノロジ スタックであり、ビッグ データ分析用の信頼できるソリューションです。HDInsight は、Hadoop の Microsoft Azure クラウド実装のためのフレームワークです。

HDInsight の Hadoop クラスターは、Hortonworks Data Platform (HDP) ディストリビューションのバージョンと、このディストリビューション内の Hadoop コンポーネントのセットを使用します。HDInsight クラスターのコンポーネントとそのバージョンについては、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][HDInsight で提供される Hadoop クラスター バージョンの新機能]」を参照してください。

Apache Hadoop は、ビッグ データの管理と分析に役立つソフトウェア フレームワークです。Apache Hadoop コアには、データ ストレージの信頼性を高める Hadoop 分散ファイル システム (HDFS)、およびこの分散システムに格納されているデータの処理と分析を同時に行う MapReduce プログラミング モデルが用意されています。このような高分散システムの展開時にハードウェア障害が発生した場合、HDFS ではデータ レプリケーションを使用して対処します。

HDInsight の Hadoop テクノロジを次に示します。

-   **[Ambari][Ambari]:**クラスターのプロビジョニング、管理、監視
-   **[Avro][Avro]** (Microsoft .NET Library for Avro):Microsoft .NET 環境向けのデータのシリアル化
-   **[HBase][HBase]:**巨大なテーブル向けの非リレーショナル データベース
-   **[HDFS][HDFS]:**Hadoop 分散ファイル システム
-   **[Hive][Hive]:**SQL に似たクエリ
-   **[Mahout][Mahout]:**機械学習
-   **[MapReduce および YARN][MapReduce および YARN]:**分散処理およびリソース管理
-   **[Oozie][Oozie]:**ワークフロー管理
-   **[Pig][Pig]:**MapReduce 変換のためのシンプルなスクリプト作成
-   **[Sqoop][Sqoop]:**データのインポートおよびエクスポート
-   **[Storm][Storm]:**高速で大規模なデータ ストリームのリアルタイム処理
-   **[Zookeeper][Zookeeper]:**分散システムにおけるプロセスの調整

これに加えて、HDInsight と共に使用できる**[ビジネス インテリジェンス ツール][ビジネス インテリジェンス ツール]**もあります。

### <a name="ambari"></a>Ambari

Apache Ambari は、Apache Hadoop クラスターのプロビジョニング、管理、および監視を目的としています。演算子ツールの直観的なコレクションおよび Hadoop の複雑さが見えないようにする信頼性の高い、一連の API が含まれており、クラスターの操作を単純化しています。「[Ambari API を使用した HDInsight の Hadoop クラスターの監視][Ambari API を使用した HDInsight の Hadoop クラスターの監視]」および [Apache Ambari API リファレンス][Apache Ambari API リファレンス]を参照してください。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)

Microsoft .NET Library for Avro は、Microsoft.NET 環境向けのシリアル化のために、Apache Avro のコンパクトなバイナリ データ交換形式を実装します。Avro は、[JSON][JSON] を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。これは、ある言語でシリアル化されたデータを別の言語で読むことができることを意味します。この形式の詳細については、[Apache Avro の仕様][Apache Avro の仕様]を参照してください。
Avro ファイルの形式は、分散型 MapReduce プログラミング モデルをサポートします。ファイル内の任意のポイントを検索し、特定のブロックから読み取りを開始できるという意味で、ファイルは "分割可能" です。詳細については、「[Microsoft .NET Library for Avro を使用したデータのシリアル化][Microsoft .NET Library for Avro を使用したデータのシリアル化]」を参照してください。

### <a name="hbase"></a>HBase

[Apache HBase][Apache HBase] は、数十億行 x 数百万列という規模の巨大な非構造化および半構造化データを処理するための、Hadoop に組み込み済みの非リレーショナル データベースです。HDInsight 上の HBase クラスターはデータを直接 Azure BLOB ストレージに格納するように構成されているため、短い遅延時間と優れた弾力性が実現されます。「[HDInsight HBase の概要][HDInsight HBase の概要]」を参照してください。

### <a name="hdfs"></a>HDFS

Hadoop 分散ファイル システム (HDFS) は、MapReduce と YARN と共に Hadoop エコシステムの中核を構成する分散ファイル システムです。HDFS は、HDInsight の Hadoop クラスターの標準ファイル システムです。

### <a name="hive"></a>Hive

[Apache Hive][Apache Hive] は、Hadoop に組み込まれているデータ ウェアハウス ソフトウェアで、HiveQL と呼ばれる SQL に似た言語を使って分散ストレージ内の大量のデータセットに対するクエリと管理を可能にします。Pig と同様、Hive は MapReduce 上の抽象コンポーネントであり、クエリを一連の MapReduce ジョブに変換します。Hive は、概念的には Pig よりもリレーショナル データベース管理システムに近いため、より構造化されたデータでの使用に適しています。非構造化データを操作する場合は Pig が適しています。「[HDInsight での Hive と Hadoop の使用][HDInsight での Hive と Hadoop の使用]」を参照してください。

### <a name="mahout"></a>Mahout

[Apache Mahout][Apache Mahout] は、Hadoop 上で実行される機械学習アルゴリズムのスケーラブルなライブラリです。統計の原則を使用して、機械学習アプリケーションは、データから学習すること、および過去の結果から将来の動作を決めることをシステムに教えます。「[HDInsight (Hadoop) で Apache Mahout を使用して映画のリコメンデーションを生成する][HDInsight (Hadoop) で Apache Mahout を使用して映画のリコメンデーションを生成する]」を参照してください。

### <a name="mapreduce"></a>MapReduce および YARN

Hadoop MapReduce は、大規模なデータセットを並列処理するアプリケーションを記述するためのソフトウェア フレームワークです。MapReduce ジョブは、大規模なデータセットを分割し、データを処理のためにキーと値のペアに整理します。

Apache YARN は、次世代の MapReduce (MapReduce 2.0、または MRv2) で、リソース管理とジョブ スケジューリング/監視という JobTracker の 2 つの主要なタスクを別個のエンティティに分けたものです。

MapReduce の詳細については、Hadoop Wiki で「[MapReduce][MapReduce]」を参照してください。YARN の詳細については、「[Apache Hadoop NextGen MapReduce (YARN) (Apache Hadoop の次世代の MapReduce (YARN))][Apache Hadoop NextGen MapReduce (YARN) (Apache Hadoop の次世代の MapReduce (YARN))]」を参照してください。

### <a name="oozie"></a>Oozie

[Apache Oozie][Apache Oozie] は Hadoop ジョブを管理するワークフローおよび調整システムです。Hadoop スタックと統合されていて、MapReduce、Pig、Hive、Sqoop の Hadoop ジョブをサポートしています。Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。「[HDInsight の Hadoop での時間ベースの Oozie コーディネーターの使用][HDInsight の Hadoop での時間ベースの Oozie コーディネーターの使用]」を参照してください。

### <a name="pig"></a>Pig

[Apache Pig][Apache Pig] は、Pig Latin と呼ばれるシンプルなスクリプト言語を使って大規模なデータセットに対して複雑な MapReduce 変換を実行することを可能にする高レベル プラットフォームです。Pig は、Hadoop 内で実行できるように Pig Latin スクリプトを変換します。ユーザー定義関数 (UDF) を作成して Pig Latin を拡張できます。「[HDInsight の Hadoop での Pig の使用][HDInsight の Hadoop での Pig の使用]」を参照してください。

### <a name="sqoop"></a>Sqoop

[Apache Sqoop][Apache Sqoop] は、リレーショナル データベース (SQL など) やその他の構造化データ ストアと Hadoop との間でデータを効率的に一括転送するためのツールです。「[HDInsight の Hadoop での Sqoop の使用][HDInsight の Hadoop での Sqoop の使用]」を参照してください。

### <a name="storm"></a>Storm

[Apache Storm][Apache Storm] は、大量のデータ ストリームを高速に処理するための分散型リアルタイム計算システムです。Storm は、HDInsight で管理されるクラスターとして提供されます。「[HDInsight (Hadoop) での Storm と HBase を使ったセンサー データの分析][HDInsight (Hadoop) での Storm と HBase を使ったセンサー データの分析]」を参照してください。

### <a name="zookeeper"></a>Zookeeper

[Apache Zookeeper][Apache Zookeeper] は、データ レジスタ (znode) の共有階層名前空間を使用して、大規模な分散システムのプロセスを調整します。Znodes には、状態、場所、構成など、プロセスの調整に必要なメタ情報が少量含まれます。

### <a name="bi"></a>ビジネス インテリジェンス ツール

Power Query アドインまたは Microsoft Hive ODBC ドライバーを使用すれば、Excel、PowerPivot、SQL Server Analysis Services、Reporting Services などの使い慣れたビジネス インテリジェンス (BI) ツールを HDInsight と連携して、データの取得、分析、レポート生成を行うことができます。

これらの BI ツールは、ビッグ データ分析に有用です。

-   [Microsoft Power Query for Excel のダウンロード][Microsoft Power Query for Excel のダウンロード]
-   [Microsoft Hive ODBC ドライバーのダウンロード][Microsoft Hive ODBC ドライバーのダウンロード]
-   [SQL Server Analysis Services の詳細][SQL Server Analysis Services の詳細]
-   [SQL Server Reporting Services の詳細][SQL Server Reporting Services の詳細]

## <a name="advantage"></a>クラウドで Hadoop を使用するメリット

HDInsight の Hadoop は、Azure クラウド エコシステムの一部としていくつかのメリットを提供します。その一部を次に示します。

-   最新の Hadoop コンポーネント。詳細については、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][HDInsight で提供される Hadoop クラスター バージョンの新機能]」を参照してください。
-   クラスターの高い可用性と信頼性。「[HDInsight における Hadoop クラスターの可用性と信頼性][HDInsight における Hadoop クラスターの可用性と信頼性]」を参照してください。
-   Hadoop 互換オプションの Azure BLOB ストレージによって実現される効率的で経済的なデータ ストレージ。詳細については、「[HDInsight の Hadoop での Azure BLOB ストレージの使用][HDInsight の Hadoop での Azure BLOB ストレージの使用]」を参照してください。
-   [Websites][Websites]、[SQL Database][SQL Database] などの他の Azure サービスとの統合。
-   低い導入コスト。[無料評価版][無料評価版]から始めることができます。「[HDInsight の料金詳細][HDInsight の料金詳細]」も参照してください。

HDInsight の Hadoop を使うその他のメリットについては、[HDInsight 向けの Azure の機能のページ][HDInsight 向けの Azure の機能のページ]を参照してください。

## <a name="solutions"></a>ビッグ データ分析のための HDInsight ソリューションを試す

組織のデータを分析して、ビジネスの洞察を得ましょう。次に例をいくつか示します。

-   [HVAC センサー データを分析する][HVAC センサー データを分析する]:Hive を HDInsight (Hadoop) と共に使用してセンサー データを分析し、Microsoft Excel を使ってデータを視覚化する方法について説明します。このサンプルでは、Hive を使用して、HVAC システムによって生成された履歴データを処理し、設定した温度を正確に維持できないシステムを識別する方法について説明します。
-   [Hive を HDInsight と共に使用して Web サイトのログを分析する][Hive を HDInsight と共に使用して Web サイトのログを分析する]:HDInsight で HiveQL を使用して、Web サイトのログを分析する方法について説明します。このサンプルでは、1 日の間に発生した外部 Web サイトからのアクセス数を調べ、ユーザーが経験した Web サイト エラーの概要を取得します。
-   [Storm と HBase を HDInsight (Hadoop) で使用してリアルタイムでセンサー データを分析する][HDInsight (Hadoop) での Storm と HBase を使ったセンサー データの分析]:HDInsight で Storm クラスターを使用して、Azure Event Hubs からのセンサー データを処理し、処理したセンサー データをほぼリアルタイムの情報として Web ベースのダッシュボードに表示するソリューションを作成する方法について説明します。

HDInsight の Hadoop を試す場合は、[HDInsight のドキュメント ページ][HDInsight のドキュメント ページ]の「さらに詳しく」セクションにある各記事を参照してください。より高度な例を試す場合は、下へスクロールして「Analyze (分析)」セクションを参照してください。

## <a name="resources"></a>ビッグ データ分析、Hadoop、HDInsight に関する情報の収集に役立つリソース

### HDInsight での Hadoop

-   [HDInsight のドキュメント][HDInsight のドキュメント ページ]: Azure HDInsight のドキュメント ページ。記事やビデオなどのリソースに対するリンクが記載されています。

-   [HDInsight の学習マップ][HDInsight の学習マップ]:HDInsight の Hadoop ドキュメントのガイド付きツアー。

-   [Azure HDInsight の概要][Azure HDInsight の概要]: HDInsight の Hadoop を使用するためのクイック スタート チュートリアル。

-   [HDInsight のサンプルの実行][HDInsight のサンプルの実行]: HDInsight に付属するサンプルの実行方法に関するチュートリアル。

-   [Azure HDInsight SDK][Azure HDInsight SDK]: HDinsight SDK のリファレンス ドキュメント。

### Azure 上の SQL Database:

-   [Azure SQL データベース][Azure SQL データベース]: SQL データベースの MSDN ドキュメント。

-   [SQL データベースの管理ポータル][SQL データベースの管理ポータル]: 軽量で使いやすいデータベース管理ツール。クラウド環境で SQL データベースを管理する際に役立ちます。

-   [Adventure Works for SQL Database][Adventure Works for SQL Database]: SQL データベースのサンプル データベースのダウンロード ページ。

### Microsoft ビジネス インテリジェンス

-   [Power Query を使用した Excel から Hadoop への接続][Power Query を使用した Excel から Hadoop への接続]Microsoft Power Query for Excel を使用して、HDInsight クラスターに関連付けられたデータを格納する Azure ストレージ アカウントに Excel を接続する方法を説明します。

-   [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続][Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続]:Microsoft Hive ODBC ドライバーを使用して、HDInsight からデータをインポートする方法を説明します。

-   [Microsoft ビジネス インテリジェンス (BI)][Microsoft ビジネス インテリジェンス (BI)]:Power BI for Office 365 の詳細、SQL Server 評価版のダウンロード、SharePoint Server 2013 と SQL Server BI のセットアップについて説明します。

### Apache Hadoop

-   [Apache Hadoop][Apache Hadoop]: 大規模なデータ セットを複数のコンピューターで分散処理するためのフレームワークとなる Apache Hadoop ソフトウェア ライブラリについて説明します。

-   [HDFS][1]: Hadoop 分散ファイル システム (HDFS) は、Hadoop アプリケーションで使用する主要なストレージ システムで、そのアーキテクチャと設計について説明します。

-   [MapReduce][2]: 複数の計算ノードから成る大規模なクラスターを利用して、膨大なデータを迅速に並列処理する Hadoop アプリケーションを記述するためのプログラミング モデルとソフトウェア フレームワークについて説明します。

  [HDInsight の Hadoop エコシステムの概要:]: #overview
  [クラウドで Hadoop を使用するメリット:]: #advantage
  [ビッグ データ分析のための HDInsight ソリューション:]: #solutions
  [ビッグ データ分析、Hadoop、HDInsight に関する情報の収集に役立つリソース:]: #resources
  [HDInsight で提供される Hadoop クラスター バージョンの新機能]: ../hdinsight-component-versioning/
  [Ambari]: #ambari
  [Avro]: #avro
  [HBase]: #hbase
  [HDFS]: #hdfs
  [Hive]: #hive
  [Mahout]: #mahout
  [MapReduce および YARN]: #mapreduce
  [Oozie]: #oozie
  [Pig]: #pig
  [Sqoop]: #sqoop
  [Storm]: #storm
  [Zookeeper]: #zookeeper
  [ビジネス インテリジェンス ツール]: #bi
  [Ambari API を使用した HDInsight の Hadoop クラスターの監視]: ../hdinsight-monitor-use-ambari-api/
  [Apache Ambari API リファレンス]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [JSON]: http://www.json.org/
  [Apache Avro の仕様]: http://avro.apache.org/docs/current/spec.html
  [Microsoft .NET Library for Avro を使用したデータのシリアル化]: ../hdinsight-dotnet-avro-serialization/
  [Apache HBase]: http://hbase.apache.org/
  [HDInsight HBase の概要]: ../hdinsight-hbase-overview/
  [Apache Hive]: http://hive.apache.org/
  [HDInsight での Hive と Hadoop の使用]: ../hdinsight-use-hive/
  [Apache Mahout]: https://mahout.apache.org/
  [HDInsight (Hadoop) で Apache Mahout を使用して映画のリコメンデーションを生成する]: ../hdinsight-mahout/
  [MapReduce]: http://wiki.apache.org/hadoop/MapReduce
  [Apache Hadoop NextGen MapReduce (YARN) (Apache Hadoop の次世代の MapReduce (YARN))]: http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html
  [Apache Oozie]: http://oozie.apache.org/
  [HDInsight の Hadoop での時間ベースの Oozie コーディネーターの使用]: ../hdinsight-use-oozie-coordinator-time/
  [Apache Pig]: http://pig.apache.org/
  [HDInsight の Hadoop での Pig の使用]: ../hdinsight-use-pig/
  [Apache Sqoop]: http://sqoop.apache.org/
  [HDInsight の Hadoop での Sqoop の使用]: ../hdinsight-use-sqoop/
  [Apache Storm]: https://storm.incubator.apache.org/
  [HDInsight (Hadoop) での Storm と HBase を使ったセンサー データの分析]: ../hdinsight-storm-sensor-data-analysis/
  [Apache Zookeeper]: http://zookeeper.apache.org/
  [Microsoft Power Query for Excel のダウンロード]: http://www.microsoft.com/ja-jp/download/details.aspx?id=39379
  [Microsoft Hive ODBC ドライバーのダウンロード]: http://www.microsoft.com/ja-jp/download/details.aspx?id=40886
  [SQL Server Analysis Services の詳細]: http://www.microsoft.com/ja-jp/server-cloud/solutions/business-intelligence/analysis.aspx
  [SQL Server Reporting Services の詳細]: http://msdn.microsoft.com/ja-jp/library/ms159106.aspx
  [HDInsight における Hadoop クラスターの可用性と信頼性]: ../hdinsight-high-availability/
  [HDInsight の Hadoop での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [Websites]: ../documentation/services/websites/
  [SQL Database]: ../documentation/services/sql-database/
  [無料評価版]: /pricing/free-trial/
  [HDInsight の料金詳細]: ../pricing/details/hdinsight/
  [HDInsight 向けの Azure の機能のページ]: ../services/hdinsight/
  [HVAC センサー データを分析する]: ../hdinsight-hive-analyze-sensor-data/
  [Hive を HDInsight と共に使用して Web サイトのログを分析する]: ../hdinsight-hive-analyze-website-log/
  [HDInsight のドキュメント ページ]: ../documentation/services/hdinsight/
  [HDInsight の学習マップ]: ../hdinsight-learn-map
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight のサンプルの実行]: ../hdinsight-run-samples/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
  [Azure SQL データベース]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336279.aspx
  [SQL データベースの管理ポータル]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg442309.aspx
  [Adventure Works for SQL Database]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Power Query を使用した Excel から Hadoop への接続]: ../hdinsight-connect-excel-power-query/
  [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft ビジネス インテリジェンス (BI)]: http://www.microsoft.com/ja-jp/server-cloud/solutions/business-intelligence/default.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
