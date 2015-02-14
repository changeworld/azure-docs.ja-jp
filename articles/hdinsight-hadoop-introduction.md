<properties 
	pageTitle="HDInsight での Hadoop 入門:クラウドでのビッグ データ分析 | Azure" 
	description="Azure HDInsight がどのように Apache Hadoop クラスターをクラウドで使用して、ビッグ データの管理、分析、レポート生成を支援するソフトウェア フレームワークを提供するかについて説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="cgronlun"/>



# HDInsight での Hadoop 入門:クラウドでのビッグ データの処理と分析

HDInsight のコンポーネント、一般的な用語、シナリオについて概説し、Hadoop を HDInsight で使用するうえで役立つリソースを紹介します。

Azure HDInsight は、Apache Hadoop クラスターをクラウドにデプロイしてプロビジョニングするためのサービスです。ビッグ データの管理、分析、レポート生成を支援するソフトウェア フレームワークとなります。Hadoop コアには、データ ストレージの信頼性を高める Hadoop 分散ファイル システム (HDFS) と、この分散システムに格納されているデータの処理と分析を同時に行うシンプルな MapReduce プログラミング モデルが用意されています。 

### ビッグ データとは
"ビッグ データ" とは、絶えず急速なペースで増加し、さまざまな非構造化形式と意味的文脈から成る巨大なデータ群を指しています。 

ビッグ データは、Twitter フィードのテキストから、工業機器のセンサー情報、オンライン カタログでの消費者の閲覧および購入行動に関する情報に至るまでの、大規模なデジタル情報を表します。ビッグ データの種類には、履歴データ (つまり、保存されたデータ) とリアルタイム データ (つまり、ソースから直接ストリーミングされるデータ) があります。 

ビッグ データから実用的な情報や洞察を導くうえで必要なのは、適切な質問をして問題に関連するデータを収集することだけではありません。データは、アクセス可能であり、クリーンアップと分析を行った後実用的な方法で定義される必要があります。そこで、HDInsight の Hadoop の出番となります。


## この記事の内容

この記事では、HDInsight の Hadoop について概説します。この記事に含まれる内容を次に示します。

* **[HDInsight の Hadoop エコシステムの概要:](#overview)**  HDInsight は、Azure 上に実装された Hadoop ソリューションで、Storm、HBase、Pig、Hive、Sqoop、Oozie、Ambari などの実装を提供します。また、HDInsight は、Excel、SQL Server Analysis Services、SQL Server Reporting Services などのビジネス インテリジェンス (BI) ツールとも統合されます。
* **[クラウドで Hadoop を使用するメリット:](#advantage)** HDInsight による Hadoop のクラウド実装をお勧めする理由。
* **[ビッグ データ分析のための HDInsight ソリューション:](#solutions)** Twitter での評判の分析から HVAC システムの有効性の分析まで、組織に役立つ HDInsight のいくつかの実用的な使用例。
* **[ビッグ データ分析、Hadoop、HDInsight に関する情報の収集に役立つリソース:](#resources)** 関連情報へのリンク。


## <a name="overview"></a>HDInsight の Hadoop エコシステムの概要

Hadoop は、急激に拡大を続けているテクノロジ スタックであり、ビッグ データ分析用の信頼できるソリューションです。HDInsight は、Hadoop の Microsoft Azure クラウド実装のためのフレームワークです。
 
HDInsight の Hadoop クラスターは、Hortonworks Data Platform (HDP) ディストリビューションのバージョンと、このディストリビューション内の Hadoop コンポーネントのセットを使用します。HDInsight クラスターのコンポーネントとそのバージョンについては、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][component-versioning]」を参照してください。

Apache Hadoop は、ビッグ データの管理と分析に役立つソフトウェア フレームワークです。Apache Hadoop コアには、データ ストレージの信頼性を高める Hadoop 分散ファイル システム (HDFS)、およびこの分散システムに格納されているデータの処理と分析を同時に行う MapReduce プログラミング モデルが用意されています。このような高分散システムの展開時にハードウェア障害が発生した場合、HDFS ではデータ レプリケーションを使用して対処します。

HDInsight の Hadoop テクノロジを次に示します。

* **[Ambari](#ambari):** クラスターのプロビジョニング、管理、監視 
* **[Avro](#avro)** (Microsoft .NET Library for Avro):Microsoft .NET 環境向けのデータのシリアル化 
* **[HBase](#hbase):**巨大なテーブル向けの非リレーショナル データベース
* **[HDFS](#hdfs):**Hadoop 分散ファイル システム
* **[Hive](#hive):**SQL に似たクエリ
* **[Mahout](#mahout):**機械学習
* **[MapReduce および YARN](#mapreduce):**分散処理およびリソース管理
* **[Oozie](#oozie):**ワークフロー管理
* **[Pig](#pig):**MapReduce 変換のためのシンプルなスクリプト作成
* **[Sqoop](#sqoop):**データのインポートおよびエクスポート 
* **[Storm](#storm):**高速で大規模なデータ ストリームのリアルタイム処理
* **[Zookeeper](#zookeeper):**分散システムにおけるプロセスの調整
 
HDInsight と使用することができる**[ビジネス インテリジェンス ツール](#bi)**についても参照してください。

###<a name="ambari"></a>Ambari

Apache Ambari は、Apache Hadoop クラスターのプロビジョニング、管理、および監視を目的としています。演算子ツールの直観的なコレクションおよび Hadoop の複雑さが見えないようにする信頼性の高い、一連の API が含まれており、クラスターの操作を単純化しています。詳細については、  「[Ambari API を使用した HDInsight の Hadoop クラスターの監視](../hdinsight-monitor-use-ambari-api/)」および  <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API リファレンス</a>に関するページを参照してください。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)

Microsoft .NET Library for Avro は、Microsoft.NET 環境向けのシリアル化のために、Apache Avro のコンパクトなバイナリ データ交換形式を実装します。Apache Avro は、 <a target="_blank" href="http://www.json.org/">JSON</a>  を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。これは、ある言語でシリアル化されたデータを別の言語で読むことができることを意味します。この形式の詳細については、 <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro Specification を参照してください</a>。 
Avro ファイルの形式は、分散 MapReduce プログラミング モデルをサポートしています。ファイル内の任意のポイントを検索し、特定のブロックから読み取りを開始できるという意味で、ファイルは "分割可能" です。詳細については、[Microsoft .NET Library for Avro を使用したデータのシリアル化](../hdinsight-dotnet-avro-serialization/)に関するページを参照してください。

### <a name="hbase"></a>HBase

<a target="_blank" href="http://hbase.apache.org/">Apache HBase</a>  は、数十億行 x 数百万列という規模の巨大な非構造化および半構造化データを処理するための、Hadoop に組み込み済みの非リレーショナル データベースです。HDInsight 上の HBase クラスターはデータを直接 Azure BLOB ストレージに格納するように構成されているため、短い遅延時間と優れた弾力性が実現されます。「[HDInsight HBase の概要](../hdinsight-hbase-overview/)」を参照してください。

### <a name="hdfs"></a>HDFS

Hadoop 分散ファイル システム (HDFS) は、MapReduce と YARN と共に Hadoop エコシステムの中核を構成する分散ファイル システムです。HDFS は、HDInsight の Hadoop クラスターの標準ファイル システムです。

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a>  は、Hadoop に組み込まれているデータ ウェアハウス ソフトウェアで、HiveQL と呼ばれる SQL に似た言語を使って分散ストレージ内の大量のデータセットに対するクエリと管理を可能にします。Pig と同様、Hive は MapReduce 上の抽象コンポーネントであり、クエリを一連の MapReduce ジョブに変換します。Hive は、概念的には Pig よりもリレーショナル データベース管理システムに近いため、より構造化されたデータでの使用に適しています。非構造化データを操作する場合は Pig が適しています。「[HDInsight での Hive と Hadoop の使用](../hdinsight-use-hive/)」を参照してください。

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a>  は、Hadoop 上で実行される機械学習アルゴリズムのスケーラブルなライブラリです。統計の原則を使用して、機械学習アプリケーションは、データから学習すること、および過去の結果から将来の動作を決めることをシステムに教えます。「[HDInsight (Hadoop) で Apache Mahout を使用して映画のリコメンデーションを生成する](../hdinsight-mahout/)」を参照してください。

### <a name="mapreduce"></a>MapReduce および YARN
Hadoop MapReduce は、大規模なデータセットを並列処理するアプリケーションを記述するためのソフトウェア フレームワークです。MapReduce ジョブは、大規模なデータセットを分割し、データを処理のためにキーと値のペアに整理します。 

Apache YARN は、次世代の MapReduce (MapReduce 2.0、または MRv2) で、リソース管理とジョブ スケジューリング/監視という JobTracker の 2 つの主要なタスクを別個のエンティティに分けたものです。

MapReduce の詳細については、Hadoop Wiki の  <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a>  を参照してください。YARN の詳細については、 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop の次世代 MapReduce (YARN)</a> に関するページを参照してください。

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a>  は Hadoop ジョブを管理するワークフローおよび調整システムです。Hadoop スタックと統合されていて、MapReduce、Pig、Hive、Sqoop の Hadoop ジョブをサポートしています。Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。「[HDInsight の Hadoop での時間ベースの Oozie コーディネーターの使用](../hdinsight-use-oozie-coordinator-time/)」を参照してください。

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a>  は、Pig Latin と呼ばれるシンプルなスクリプト言語を使って大規模なデータセットに対して複雑な MapReduce 変換を実行することを可能にする高レベル プラットフォームです。Pig は、Hadoop 内で実行できるように Pig Latin スクリプトを変換します。ユーザー定義関数 (UDF) を作成して Pig Latin を拡張できます。[Hadoop で Pig を使用した Apache ログ ファイルの分析](../hdinsight-use-pig/)に関するページを参照してください。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a>  は、リレーショナル データベース (SQL など) やその他の構造化データ ストアと Hadoop との間でデータを効率的に一括転送するためのツールです。[Hadoop での Sqoop の使用](../hdinsight-use-sqoop/)に関するページを参照してください。

### <a name="storm"></a>Storm
<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>  は、大量のデータ ストリームを高速に処理するための分散型リアルタイム計算システムです。Storm は、HDInsight で管理されるクラスターとして提供されます。「[HDInsight (Hadoop) での Storm と HBase を使ったセンサー データの分析](../hdinsight-storm-sensor-data-analysis/)」を参照してください。

### <a name="zookeeper"></a>Zookeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a>  は、データ レジスタ (znode) の共有階層名前空間を使用して、大規模な分散システムのプロセスを調整します。Znodes には、状態、場所、構成など、プロセスの調整に必要なメタ情報が少量含まれます。 

### <a name="bi"></a>ビジネス インテリジェンス ツール 
Power Query アドインまたは Microsoft Hive ODBC ドライバーを使用すれば、Excel、PowerPivot、SQL Server Analysis Services、Reporting Services などの使い慣れたビジネス インテリジェンス (BI) ツールを HDInsight と連携して、データの取得、分析、レポート生成を行うことができます。 

これらの BI ツールは、ビッグ データ分析に有用です。

* <a target="_blank" href="http://www.microsoft.com/ja-jp/download/details.aspx?id=39379">Microsoft Power Query for Excel のダウンロード</a> 
* <a target="_blank" href="http://www.microsoft.com/ja-jp/download/details.aspx?id=40886">Microsoft Hive ODBC ドライバーのダウンロード</a>
* <a target="_blank" href="http://www.microsoft.com/ja-jp/server-cloud/solutions/business-intelligence/analysis.aspx">SQL Server Analysis Services の詳細</a>
* <a target="_blank" href="http://msdn.microsoft.com/ja-jp/library/ms159106.aspx">SQL Server Reporting Services の詳細</a>

## <a name="advantage"></a>クラウドで Hadoop を使用するメリット

HDInsight の Hadoop は、Azure クラウド エコシステムの一部としていくつかのメリットを提供します。その一部を次に示します。

* 最新の Hadoop コンポーネント。詳細については、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][component-versioning]」を参照してください。
* クラスターの高い可用性と信頼性。詳細については、「[HDInsight における Hadoop クラスターの可用性と信頼性](../hdinsight-high-availability/)」を参照してください。
* Hadoop 互換オプションの Azure BLOB ストレージによって実現される効率的で経済的なデータ ストレージ。詳細については、[HDInsight の Hadoop での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage/)に関するページを参照してください。
* [Websites](../documentation/services/websites/)、[SQL Database](../documentation/services/sql-database/) などの他の Azure サービスとの統合。
* 低い導入コスト。[無料評価版](/pricing/free-trial/)から始めることができます。[HDInsight の料金詳細](../pricing/details/hdinsight/)に関するページも参照してください。

HDInsight の Hadoop を使うその他のメリットについては、[HDInsight 向けの Azure の機能][marketing-page]に関するページを参照してください。

## <a name="solutions"></a>ビッグ データ分析のための HDInsight ソリューションを試す

組織のデータを分析して、ビジネスの洞察を得ましょう。次に例をいくつか示します。 

* [HVAC センサー データの分析](../hdinsight-hive-analyze-sensor-data/): Hive を HDInsight (Hadoop) と共に使用してセンサー データを分析し、Microsoft Excel を使ってデータを視覚化する方法について説明します。このサンプルでは、Hive を使用して、HVAC システムによって生成された履歴データを処理し、設定した温度を正確に維持できないシステムを識別する方法について説明します。
* [Web サイトのログ分析のための HDInsight での Hive の使用](../hdinsight-hive-analyze-website-log/): HDInsight で HiveQL を使用して、Web サイトのログを分析する方法について説明します。このサンプルでは、1 日の間に発生した外部 Web サイトからのアクセス数を調べ、ユーザーが経験した Web サイト エラーの概要を取得します。
* [HDInsight (Hadoop) での Storm と HBase を使用したリアルタイムでのセンサー データの分析](../hdinsight-storm-sensor-data-analysis/): HDInsight で Storm クラスターを使用して、Azure Event Hubs からのセンサー データを処理し、処理したセンサー データをほぼリアルタイムの情報として Web ベースのダッシュボードに表示するソリューションを作成する方法について説明します。

HDInsight の Hadoop を試す場合は、[HDInsight のドキュメント ページ](../documentation/services/hdinsight/)の「さらに詳しく」セクションにある各記事を参照してください。より高度な例を試す場合は、下へスクロールして「Analyze (分析)」セクションを参照してください。



## <a name="resources"></a>ビッグ データ分析、Hadoop、HDInsight に関する情報の収集に役立つリソース 

### HDInsight での Hadoop	

* [HDInsight のドキュメント](../documentation/services/hdinsight/): Azure HDInsight のドキュメント ページ。記事やビデオなどのリソースに対するリンクが記載されています。

* [HDInsight の学習マップ](../hdinsight-learn-map): HDInsight の Hadoop ドキュメントのガイド付きツアー。

* [Azure HDInsight の概要](../hdinsight-get-started/): HDInsight の Hadoop を使用するためのクイック スタート チュートリアル。

* [HDInsight でのサンプルの実行](../hdinsight-run-samples/): HDInsight に付属するサンプルの実行方法に関するチュートリアル。
	
* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx): HDinsight SDK のリファレンス ドキュメント。


### Azure 上の SQL Database:	
		
* [Azure SQL データベース](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336279.aspx): SQL データベースの MSDN ドキュメント。
	
* [SQL データベースの管理ポータル](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg442309.aspx): 軽量で使いやすいデータベース管理ツール。クラウド環境で SQL データベースを管理する際に役立ちます。

* [SQL データベース用の Adventure Works](http://msftdbprodsamples.codeplex.com/releases/view/37304): SQL データベースのサンプル データベースのダウンロード ページ。	

### Microsoft ビジネス インテリジェンス		

* [Power Query を使用した Excel から Hadoop への接続](../hdinsight-connect-excel-power-query/): Microsoft Power Query for Excel を使用して、HDInsight クラスターに関連付けられたデータを格納する Azure ストレージ アカウントに Excel を接続する方法を説明します。 

* [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](../hdinsight-connect-excel-hive-ODBC-driver/): Microsoft Hive ODBC ドライバーを使用して、HDInsight からデータをインポートする方法を説明します。

* [Microsoft ビジネス インテリジェンス (BI)](http://www.microsoft.com/ja-jp/server-cloud/solutions/business-intelligence/default.aspx): Power BI for Office 365 の詳細、SQL Server 評価版のダウンロード、SharePoint Server 2013 と SQL Server BI のセットアップについて説明します。
			

### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>:大規模なデータ セットを複数のコンピューターで分散処理するためのフレームワークとなる Apache Hadoop ソフトウェア ライブラリについて説明します。	

*  <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html">HDFS</a>:Hadoop 分散ファイル システム (HDFS) は、Hadoop アプリケーションで使用する主要なストレージ システムで、そのアーキテクチャと設計について説明します。		

* <a target="_blank" href="http://mapreduce.org/">MapReduce</a>:複数の計算ノードから成る大規模なクラスターを利用して、膨大なデータを迅速に並列処理する Hadoop アプリケーションを記述するためのプログラミング モデルとソフトウェア フレームワークについて説明します。	


[marketing-page]: ../services/hdinsight/
[component-versioning]: ../hdinsight-component-versioning/

[zookeeper]: http://zookeeper.apache.org/ 
<!--HONumber=42-->
