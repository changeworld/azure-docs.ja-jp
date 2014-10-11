<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Hadoop in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# HDInsight での Hadoop 入門

## 概要

Azure HDInsight は、Apache™ Hadoop™ クラスターをクラウドに展開してプロビジョニングするためのサービスです。ビッグ データの管理、分析、レポート生成を支援するソフトウェア フレームワークとなります。

### ビッグ データ

"ビッグ データ" とは、絶えず急速なペースで増加し、さまざまな非構造化形式と意味的文脈から成る巨大なデータ群を指しています。収集されたビッグ データ自体は企業に価値をもたらしません。ビッグ データから有益な情報を得るには、適切な質問を用意し、問題に関連するデータを収集するだけでなく、それらのデータにアクセスし、整理、分析した上で、目的に合った方法で提示しなければなりません。さらに多くの場合は、さまざまな情報源のデータを組み合わせて視点と文脈を確立する必要があり、これは現在、マッシュアップと呼ばれています。

### Apache Hadoop

Apache Hadoop は、ビッグ データの管理と分析を容易にするためのソフトウェア フレームワークです。Apache Hadoop コアには、データ ストレージの信頼性を高める Hadoop 分散ファイル システム (HDFS)、およびこの分散システムに格納されているデータの処理と分析を同時に行う MapReduce プログラミング モデルが用意されています。このような高分散システムの展開時にハードウェア障害が発生した場合、HDFS ではデータ レプリケーションを使用して対処します。

### MapReduce および YARN

さまざまなデータ ソースから収集した非構造化データの分析処理を簡略化するため、MapReduce プログラミング モデルには、Map 操作と Reduce 操作を抽象化するしくみ (コア抽象化) が用意されています。MapReduce プログラミング モデルでは、すべてのジョブが "キーと値" の組み合わせから成るデータセットへの演算と見なされます。したがって、入力ファイルと出力ファイルの両方に、"キーと値" ペアのみで構成されたデータセットが含まれている必要があります。この制約による主な欠点は、この結果、MapReduce ジョブが構成可能になることです。

Pig や Hive などのその他の Hadoop 関連プロジェクトは、HDFS および MapReduce フレームワーク上に構築されます。このようなプロジェクトは、MapReduce プログラムを直接操作するよりも簡単にクラスターを管理する手段として使用されます。たとえば Pig では、Pig Latin と呼ばれる手続き型言語を使用してプログラムを記述し、それをクラスター上で MapReduce プログラムにコンパイルできます。"流れるような制御" でデータ フローを管理できることも大きな特長です。Hive は、クラスターに保存されたファイルのデータのテーブル抽象化を実現するデータ ウェアハウス インフラストラクチャで、そのデータを HiveQL と呼ばれる宣言型言語で SQL 風のステートメントを使用して照会できます。

### HDInsight

Azure HDInsight は、Apache Hadoop をクラウドのサービスとして利用するためのツールです。拡張性とコスト効率に優れたシンプルな環境で、Pig、Hive および Oozie のような HDFS/MapReduce ソフトウェア フレームワークと関連プロジェクトを利用できます。

サービスの可用性を高めるために、HDInsight によってデプロイされた Hadoop クラスターに 2 つ目のヘッドノードが追加されました。通常 Hadoop クラスターの標準的な実装には、ヘッドノードは 1 つだけあります。HDInsight はセカンダリ ヘッドノードを追加することで、この単一障害点をなくします。顧客が XL サイズのヘッドノードでクラスターをプロビジョニングしない限り、新しい HA クラスター構成に切り替えてもクラスターの価格は変わりません。

HDInsight がもたらす大きなメリットの 1 つは、データの管理と保存です。HDInsight は、既定のファイル システムとして Azure BLOB ストレージを使用します。BLOB ストレージと HDFS はそれぞれ特長が異なります。BLOB ストレージはデータの保管用として最適化されており、HDFS はそれらのデータを効率的に処理できるように最適化されています。

-   Azure BLOB ストレージは拡張性と可用性に優れ、共有可能であり、HDInsight で処理されるデータを低コストで長期間保存できます。
-   HDInsight によって HDFS 上にデプロイされる Hadoop クラスターは、データに対して MapReduce 計算タスクを効率的に実行できるように最適化されています。

HDInsight のクラスターは Azure の計算ノード上に展開され、MapReduce タスクを実行し、それらのタスクが完了した時点でユーザーによって削除されます。ジョブ完了後もデータを HDFS クラスターに保持しておくと、データの格納コストがかさんでしまいます。BLOB ストレージは、信頼性に優れた汎用的な Azure ストレージ ソリューションです。そのため BLOB ストレージにデータを格納した場合、計算で使用するクラスターを削除してもユーザー データは失われません。さらに、BLOB ストレージは単なる低コストのソリューションではありません。高度な HDFS ファイル システム インターフェイスを備えているため、その管理しているデータに対して Hadoop エコシステムのすべてのコンポーネントを直接実行できます (既定の設定)。ユーザーは、データがどこに保存されているかを意識する必要がありません。

HDInsight は、Azure PowerShell を使用して、Hadoop ジョブの構成、実行、後処理を行います。HDInsight には、Sqoop コネクタも用意されていて、Azure SQL データベースから HDFS にデータをインポートしたり、Azure SQL データベースに HDFS からデータをエクスポートするのに使用できます。

また HDInsight は、YARN も使用できるようにしました。これは、Hadoop クラスターでのデータ処理に使用される従来の Apache Hadoop MapReduce フレームワークに代わる、新しい汎用的な分散アプリケーション管理フレームワークです。Hadoop オペレーティング システムとして効果的に機能し、単一の目的のバッチ処理用データ プラットフォームであった Hadoop を、バッチ、インタラクティブ、オンライン、ストリームの各処理が可能な多目的プラットフォームにします。この新しい管理フレームワークにより、容量の保証、公平性、サービス レベル アグリーメントなどの基準に従って、スケーラビリティおよびクラスター使用率が向上します。

Microsoft Power Query for Excel は、Azure HDInsight または任意の HDFS から Excel にデータをインポートするときに使用できます。このアドオンは、各種データ ソースのデータ検出とアクセスを単純化することで、Excel でのセルフサービス BI 体験を強化します。Power Query に加えて、Microsoft Hive ODBC ドライバーを利用して、Excel、SQL Server Analysis Services、Reporting Services などのビジネス インテリジェンス (BI) ツールを統合できるため、エンド ツー エンドのデータ分析を容易に行うことができます。

### 概略

このトピックでは、HDInsight でサポートされている Hadoop エコシステム、HDInsight の主な使用シナリオ、およびその他の関連リソースについて説明します。ここで取り上げる内容は次のとおりです。

-   [HDInsight の Hadoop エコシステム][]: HDInsight には Pig、Hive、Sqoop、Oozie、Ambari が実装されています。また、その他の BI ツール (Excel、SQL Server Analysis Services、Reporting Services など) もサポートされており、Power Query または Microsoft Hive ODBC ドライバーを使用して、BLOB ストレージ/HDFS および MapReduce フレームワークと連携できます。このセクションでは、Hadoop エコシステムのこれらのプログラムがどのようなジョブを処理するかについて説明します。

-   [HDInsight のビッグ データ シナリオ][]: 「HDInsight はどのような種類のジョブに適しているのか」という疑問に答えます。

-   [HDInsight の関連リソース][]: その他の関連情報を紹介します。

## <a name="Ecosystem">Azure 環境での Hadoop エコシステム </a>

### はじめに

HDInsight は、Microsoft のクラウドベースのソリューションを利用してビッグ データを処理するためのフレームワークです。この連携エコシステムは、MapReduce プログラミング モデルの並列処理機能を利用して、膨大なデータを管理および分析します。次に、HDInsight で使用できる Apache Hadoop 技術について簡単に説明します。

データの処理機能とウェアハウジング機能を統合するため、HDInsight には Hive と Pig が実装されています。Microsoft のビッグ データ ソリューションには、SQL Server Analysis Services、Reporting Services、PowerPivot、Excel などの Microsoft BI ツールが統合されています。これにより、BLOB ストレージで HDInsight によって保存され管理されているデータに対して直接 BI を実行できます。

Hadoop エコシステムの一部であり、Hadoop クラスター上で実行されるその他の Apache 対応技術や関連技術をダウンロードして、HDInsight と共に使用することもできます。これにはリレーショナル データ ストアを HDFS に統合する Sqoop などのオープン ソース技術も含まれます。

### Pig

Pig は、Hadoop クラスター上でビッグ データを処理するための高レベル プラットフォームです。"Pig Latin" と呼ばれるデータ フロー言語で構成されます。大規模なデータセットに対するクエリを記述したり、プログラムをコンソールから実行したりできます。Pig Latin プログラムは一連のデータセット命令から成り、これらは MapReduce プログラムに変換されます。Pig Latin 抽象化は MapReduce より高度なデータ構造を提供し、SQL でリレーショナル データベース管理システム (RDBMS) を操作する場合と同じように Hadoop を操作できます。Pig Latin は完全に拡張可能です。データを分析する際、Java、Python、Ruby、C#、または JavaScript で記述されたユーザー定義関数 (UDF) を呼び出して、処理パス ステージをカスタマイズできます。詳細については、[Apache Pig に関するページ][]を参照してください。

### Hive

HDFS に格納されたデータを管理する分散データ ウェアハウスであり、Hadoop クエリ エンジンです。Hive は高度な SQL スキルを必要とする分析に適しており、SQL と同様のインターフェイスとリレーショナル データ モデルを提供します。Hive では、HiveQL と呼ばれる言語 (SQL の方言) を使用します。Pig と同様、Hive は MapReduce 上の抽象コンポーネントであり、クエリを一連の MapReduce ジョブに変換します。Hive のシナリオは概念が RDBMS と似ていることから、構造化されたデータの操作に適しています。非構造化データを操作する場合は Pig が適しています。詳細については、[Apache Hive に関する Web ページ][]を参照してください。

### Sqoop

Sqoop は、リレーショナル データベース (SQL など) やその他の構造化データ ストアと Hadoop との間でデータを効率的に一括転送するためのツールです。外部の構造化データ ストアから HDFS や関連システム (Hive など) にデータをインポートするときは、Sqoop を使用します。また、Hadoop からデータを抽出し、抽出したデータを外部のリレーショナル データベース、エンタープライズ データ ウェアハウス、またはその他の構造化データ ストアへエクスポートするときにも Sqoop を使用できます。詳細については、[Apache Sqoop に関する Web ページ][]を参照してください。

### Oozie

Apache Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。Hadoop スタックと統合されていて、Apache MapReduce、Apache Pig、Apache Hive、Apache Sqoop の Hadoop ジョブをサポートしています。Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。

### Ambari

Apache Ambari は、Apache Hadoop クラスターのプロビジョニング、管理、および監視を目的としています。演算子ツールの直観的なコレクションおよび Hadoop の複雑さが見えないようにする信頼性の高い、一連の API が含まれており、クラスターの操作を単純化しています。API の詳細については、「[Ambari API リファレンス][]」を参照してください。HDInsight は現在、Ambari の監視機能のみをサポートしています。Ambari API Version 1.0 は HDInsight クラスター Version 2.1 および 3.0 によってサポートされています。Ambari の詳細については、[Apache Ambari に関する Web ページ][]を参照してください。

### Microsoft Avro ライブラリ

Microsoft Avro ライブラリは、Microsoft.NET 環境向けに Apache Avro データ シリアル化システムを実装します。Apache Avro は、シリアル化のためのコンパクトなバイナリ データ交換形式を提供します。Apache Avro は、[JSON][] を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。ある言語でシリアル化されたデータは、別の言語で読むことができます。現在、C、C++、C#、Java、PHP、Python、および Ruby がサポートされています。この形式の詳細については、[Apache Avro の仕様][]を参照してください。現在のバージョンの Microsoft Avro ライブラリでは、このシリアル化のリモート プロシージャ コール (RPC) の部分をサポートしていないことに注意してください。

Apache Avro のシリアル化形式は、Azure HDInsight やその他の Apache Hadoop 環境で広く使用されています。Avro は、Hadoop MapReduce ジョブ内の複雑なデータ構造を表すのに便利です。Avro ファイルの形式は、分散型 MapReduce プログラミング モデルをサポートするように設計されています。分散を可能にする重要な機能として、ファイル内の任意のポイントを検索し、特定のブロックから読み取りを開始できるという意味で、ファイルが "分割可能" であることがあります。詳細については、「[Serialize data with the Microsoft Avro Library (Microsoft Avro ライブラリを使用したデータのシリアル化)][]」を参照してください。

### ビジネス インテリジェンス ツールとコネクタ

Power Query アドインまたは Microsoft Hive ODBC ドライバーを使用すれば、Excel、PowerPivot、SQL Server Analysis Services、Reporting Services などの使い慣れたビジネス インテリジェンス (BI) ツールを HDInsight と連携して、データの取得、分析、レポート生成を行うことができます。

-   Microsoft Power Query for Excel は [Microsoft ダウンロード センター][]からダウンロードできます。

-   Microsoft Hive ODBC ドライバーは、[ダウンロード センター][]からダウンロードできます。

-   Analysis Services の詳細については、[SQL Server 2012 Analysis Services][] の Web ページを参照してください。

-   Reporting Services の詳細については、[SQL Server 2012 Reporting][] の Web ページを参照してください。

## <a name="Scenarios"></a>HDInsight のビッグ データ シナリオ

HDInsight の 1 つの利用例として、"複数の Azure ノードに保存され、頻繁に更新する必要がない非構造化データセット全体をリアルタイムで一括分析する" ケースがあります。

このシナリオは、ビジネス分野、科学分野、管理分野のさまざまな活動にあてはまります。たとえば、小売業でサプライ チェーンを管理する、金融業で不審な取り引きパターンを監視する、公益事業で需要パターンを分析する、一連の環境センサーで収集した大気環境と水質データを分析する、都市部の犯罪パターンを明らかにする、などの利用方法があります。

HDInsight (および Hadoop 技術全般) は、いったん書き込んだ後はほとんど更新せず、分析の段階でデータ全体を読み取る必要がある膨大なログ データやアーカイブ データを処理する場合に適しています。一方、RDBMS は、データ量がそれほど膨大ではなく (ペタバイト単位ではなくギガバイト単位)、データセット内の特定のデータ ポイントを頻繁に更新または照会する必要がある場合に適しています。また、決まったスキーマに基づいて管理および保存される構造化データであることも RDBMS の条件となります。MapReduce の場合、処理する時点でデータを解釈するので、スキーマが事前定義されていない非構造化データを効率的に処理できます。

## <a name="Resources"></a>次の手順:HDInsight の関連リソース

**Microsoft: HDInsight**

-   [HDInsight のドキュメント][]: Azure HDInsight のドキュメント ページ。記事やビデオなどのリソースに対するリンクが記載されています。

-   [HDInsight Release Notes (HDInsight リリース ノート)][]: 最新リリースに関する留意事項。

-   [Azure HDInsight の概要][]: HDInsight の使用方法のクイック スタートとなるチュートリアル。

-   [HDInsight のサンプルの実行][]: HDInsight に付属するサンプルの実行方法に関するチュートリアル。

-   [Big Data と Azure][]: Azure を使用して構築できるビッグ データ シナリオを紹介します。

-   [Azure HDInsight SDK][]: HDinsight SDK のリファレンス ドキュメント。

**Microsoft: Windows および SQL データベース**

-   [Azure のホーム ページ][]: アプリケーションを初めて構築する場合に役立つ各種シナリオ、無料評価版、開発用ツール、ドキュメントが用意されています。

-   [Azure SQL データベース][]: SQL データベースの MSDN ドキュメント。

-   [SQL データベースの管理ポータル][]: 軽量で使いやすいデータベース管理ツール。クラウド環境で SQL データベースを管理する際に役立ちます。

-   [Adventure Works for SQL Database][]: SQL データベースのサンプル データベースのダウンロード ページ。

**Microsoft: ビジネス インテリジェンス**

-   [Power Query を使用した Excel から HDInsight への接続][]: Microsoft Power Query for Excel を使用して、HDInsight クラスターに関連付けられたデータを格納する Azure ストレージ アカウントに Excel を接続する方法を説明します。

-   [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][]: Microsoft Hive ODBC ドライバーを使用して、Azure HDInsight からデータをインポートする方法を説明します。

-   [Microsoft BI PowerPivot][]: 高度なデータ マッシュアップおよびデータ探索ツールをダウンロードし関連情報を入手します。

-   [SQL Server 2012 Analysis Services][]: SQL Server 2012 の評価版をダウンロードでき、エンタープライズ規模の総合的な分析ソリューションを構築して、収集したデータから価値ある情報を得る方法が説明されています。

-   [SQL Server 2012 Reporting][1]: SQL Server 2012 の評価版をダウンロードでき、エンタープライズ規模でのリアルタイムな意思決定をサポートする、包括的で拡張性に優れたソリューションを作成する方法が説明されています。

**Apache Hadoop**:

-   [Apache Hadoop][]: 大規模なデータ セットを複数のコンピューターで分散処理するためのフレームワークとなる Apache Hadoop ソフトウェア ライブラリについて説明します。

-   [HDFS][]: Hadoop 分散ファイル システム (HDFS) は、Hadoop アプリケーションで使用する主要なストレージ システムで、そのアーキテクチャと設計について説明します。

-   [MapReduce][]: 複数の計算ノードから成る大規模なクラスターを利用して、膨大なデータを迅速に並列処理する Hadoop アプリケーションを記述するためのプログラミング モデルとソフトウェア フレームワークについて説明します。

  [HDInsight の Hadoop エコシステム]: #Ecosystem
  [HDInsight のビッグ データ シナリオ]: #Scenarios
  [HDInsight の関連リソース]: #Resources
  [Apache Pig に関するページ]: http://pig.apache.org/
  [Apache Hive に関する Web ページ]: http://hive.apache.org/
  [Apache Sqoop に関する Web ページ]: http://sqoop.apache.org/
  [Ambari API リファレンス]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Apache Ambari に関する Web ページ]: http://ambari.apache.org/
  [JSON]: http://www.json.org
  [Apache Avro の仕様]: http://avro.apache.org/docs/current/spec.html
  [Serialize data with the Microsoft Avro Library (Microsoft Avro ライブラリを使用したデータのシリアル化)]: ../hdinsight-dotnet-avro-serialization/
  [Microsoft ダウンロード センター]: http://go.microsoft.com/fwlink/?LinkID=286689
  [ダウンロード センター]: http://go.microsoft.com/fwlink/?LinkID=286698
  [SQL Server 2012 Analysis Services]: http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx
  [SQL Server 2012 Reporting]: http://www.microsoft.com/en-us/sqlserver/solutions-technologies/business-intelligence/reporting.aspx
  [HDInsight のドキュメント]: http://go.microsoft.com/fwlink/?LinkID=285601
  [HDInsight Release Notes (HDInsight リリース ノート)]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-release-notes/
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight のサンプルの実行]: ../hdinsight-run-samples/
  [Big Data と Azure]: http://azure.microsoft.com/en-us/solutions/big-data/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
  [Azure のホーム ページ]: http://azure.microsoft.com/en-us/
  [Azure SQL データベース]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx
  [SQL データベースの管理ポータル]: http://msdn.microsoft.com/en-us/library/windowsazure/gg442309.aspx
  [Adventure Works for SQL Database]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Power Query を使用した Excel から HDInsight への接続]: ../hdinsight-connect-excel-power-query/
  [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft BI PowerPivot]: http://www.microsoft.com/en-us/bi/PowerPivot.aspx
  [1]: http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
  [HDFS]: http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html
  [MapReduce]: http://mapreduce.org/
