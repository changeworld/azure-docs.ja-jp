<properties
	pageTitle="HDInsight で提供される Hadoop クラスター バージョンの新機能 | Microsoft Azure"
	description="HDInsight は、デプロイできる Hadoop クラスター バージョンを複数サポートしています。サポートされている Hadoop および Hortonworks Data Platform (HDP) ディストリビューションのバージョンを確認します。"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/29/2016"
	ms.author="jgao"/>


#HDInsight で提供される Hadoop クラスター バージョンの新機能

##HDInsight のバージョンと Hadoop コンポーネント
Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。Azure HDInsight で使用される既定のクラスター バージョンは、2015 年 12 月 3 日現在、HDP 2.2 を基盤とした 3.2 です。


コンポーネント|HDInsight Version 3.3 | HDInsight Version 3.2 (既定)|HDInsight Version 3.1 |HDInsight Version 3.0|
---|---|---|---|---
Hortonworks Data Platform|2\.3|2\.2|2\.1.7|2\.0|
Apache Hadoop & YARN|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive & HCatalog|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
Apache HBase |1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
Apache Zookeeper|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.9.3|0\.9.1||
Apache Mahout|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.5.2 (Linux のみ/試験的ビルド)|1\.3.1 (Windows のみ)|||


**現在のコンポーネントのバージョンの取得**

HDInsight クラスターのバージョンに関連付けられたコンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。利用可能なコンポーネントを特定し、どのバージョンがクラスターに使用されているかどうかを確認する 1 つの方法は、Ambari REST API を使用することです。**GetComponentInformation** コマンドを使用すると、サービス コンポーネントに関する情報を取得できます。詳細については、「[Ambari のドキュメント][ambari-docs]」を参照してください。この情報を取得するもう 1 つの方法は、リモート デスクトップを使用してクラスターにログインし、"C:\\apps\\dist\" ディレクトリの内容を直接確認することです。


**リリース ノート**

HDInsight の最新バージョンに関する追加のリリース ノートは、[HDInsight リリース ノート](hdinsight-release-notes.md)を参照してください。

### HDInsight クラスターの作成時にバージョンを選択

HDInsight Windows PowerShell コマンドレットまたは HDInsight .NET SDK を使用してクラスターを作成するときは、Azure ポータルの **[オプションの構成]** ブレードの **[HDInsight のバージョン]** ドロップダウンを使用して、HDInsight Hadoop クラスターのバージョンを選択することができます。

##機能概要
HDInsight プラットフォームの重要な機能には、次のようなものがあります。

- **Spark** - Apache Spark はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。Spark のメモリ内の計算能力により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。

	Spark を使用して、従来のディスク ベースのデータ処理を実行することもできます。Spark は、中間段階でのディスクへの書き込みを回避することで、従来の MapReduce フレームワークを向上しています。また、Spark は Hadoop Distributed File System (HDFS) と Azure BLOB ストレージと互換性があるため、既存のデータは Spark を通じて簡単に処理できます。

	Spark はスクリプト アクションを使用して追加することもできます。スクリプト アクションは、Spark 1.2.0 を HDInsight 3.2 クラスターに、または Spark 1.0.2 を HDInsight 3.1 クラスターに追加します。詳細については、「[HDInsight Hadoop クラスターで Spark をインストールして使用する](hdinsight-hadoop-spark-install.md)」を参照してください。


- **Storm** - 一般公開が開始された Azure HDInsight の Storm は、リアルタイム分析をすばやく簡単にデプロイできる機能で、数回クリックするだけで数分以内にデプロイが完了します。Azure HDInsight の Apache Storm は、Apache Hadoop エコシステムのオープン ソース プロジェクトの 1 つで、何百万ものイベントを信頼性の高い方法で処理できる分析プラットフォームへのアクセスを提供します。これにより、Hadoop ユーザーは、イベントが発生したときだけでなく、過去のイベントからも洞察を得ることができます。Microsoft では、Visual Studio とのビルトイン統合も提供しており、開発者は Storm を簡単に操作できます。Storm トポロジの開発、デプロイ、およびデバッグを、Visual Studio 内から実行できます。

- **Linux での HDInsight** - Azure HDInsight には、Linux (Ubuntu) 仮想マシン (VM) 上で実行される Hadoop クラスターを作成するオプションが用意されています。Linux または Unix に詳しい場合、既存の Linux ベースの Hadoop ソリューションから移行する場合、または Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、このオプションを使用して実行できます。Windows または Linux が実行されているクライアント コンピューターから、Azure ポータル、Azure CLI、または HDInsight .NET SDK (Windows のみ) を使用して Linux 上に HDInsight クラスターを作成できます。

- **追加の VM サイズ** - HDInsight クラスターを利用できる VM の種類とサイズが増えました。HDInsight クラスターは、汎用目的でビルドされた A2 ～ A7 サイズ、ソリッド ステート ドライブ (SSD) と 60% 高速のプロセッサを特徴とする D シリーズ ノード、高速ネットワーク用 InfiniBand をサポートする A8 と A9 サイズを利用できます。Azure HDInsight で Apache HBase を使用するお客様は、D シリーズの大容量メモリ構成を利用してパフォーマンスを向上させることができます。また、Azure HDInsight で Apache Storm を使用するお客様は、追加メモリを活用して大規模な参照データ セットを読み込んだり、高速な CPU によってスループットを向上させたりできます。

- **クラスター スケーリング** - クラスター スケーリングを使用すると、実行する HDInsight クラスターのノード数を、削除または再作成しなくても変更できます。現在この機能があるのは Hadoop クエリと Apache Storm のみですが、Apache HBase も近日中にサポートされます。

- **スクリプト操作** - このクラスター カスタマイズ機能により、カスタム スクリプトを使用して任意の方法で Hadoop クラスターを変更できます。この新機能により、ユーザーは、Apache Hadoop エコシステムから入手できるプロジェクトを検証し、Azure HDInsight クラスターにデプロイできます。このカスタマイズ機能は、Hadoop、HBase、Storm など、すべての種類の HDInsight クラスターで使用できます。

- **HBase** - HBase は、ビッグ データのオンライン トランザクション処理を可能にする遅延時間の短い NoSQL データベースです。HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよびテレメトリのデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。

- **Apache Phoenix** - Apache Phoenix は、HBase 上の構造化照会言語 (SQL) クエリ レイヤーです。セカンダリ インデックスのレポートなど、SQL クエリ言語仕様のサブセットを限定的にサポートします。また、HBase データ上で遅延時間が短いクエリを対象とする、クライアント内蔵の Java Database Connectivity (JDBC) ドライバーとして配信されます。Apache Phoenix は SQL クエリを引き継ぎ、一連の HBase スキャンおよびコプロセッサの呼び出しにコンパイルし、通常の JDBC 結果セットを生成します。Apache Phoenix は HBase 上のリレーショナル データベース レイヤーです。また、HBase データ上で遅延時間が短いクエリを対象とする、クライアント内蔵の JDBC ドライバーとして配信されます。Apache Phoenix は SQL クエリを引き継ぎ、一連の HBase スキャンにコンパイルし、それらのスキャンの実行を調整し、通常の JDBC 結果セットを生成します。

- **クラスター ダッシュボード** - HDInsight クラスターにデプロイする新しい Web アプリケーション。Hive クエリの実行、ジョブ ログの確認、Azure BLOB ストレージの表示に使用します。この Web アプリケーションにアクセスするための URL は <*クラスター名*>.azurehdinsight.net です。

- **Microsoft Avro ライブラリ** - このライブラリは、Microsoft.NET 環境向けに Apache Avro データ シリアル化システムを実装します。Apache Avro は、シリアル化のためのコンパクトなバイナリ データ交換形式を提供します。Apache Avro は、JavaScript Object Notation (JSON) を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。ある言語でシリアル化されたデータは、別の言語で読むことができます。現在、C、C++、C#、Java、PHP、Python、および Ruby がサポートされています。Apache Avro シリアル化形式は、Hadoop MapReduce ジョブ内の複雑なデータ構造を表すために Azure HDInsight で広く使用されています。

- **YARN** - Hadoop クラスターでのデータ処理に使用される従来の Apache Hadoop MapReduce フレームワークに代わる、新しい汎用的な分散アプリケーション管理フレームワーク。Hadoop オペレーティング システムとして効果的に機能し、単一の目的のバッチ処理用データ プラットフォームであった Hadoop を、バッチ、インタラクティブ、オンライン、ストリームの各処理が可能な多目的プラットフォームにします。この新しい管理フレームワークにより、容量の保証、公平性、サービス レベル アグリーメント (SLA) などの基準に従って、スケーラビリティおよびクラスター使用率が向上します。

- **Tez (HDInsight 3.1 以降のみ)** -Hadoop で小規模なワークロードと大規模なワークロードの両方にまたがる簡単なデータ処理タスクを作成する、カスタマイズ可能な汎用フレームワーク。1 つのジョブでタスクの複雑な無閉路有効グラフ (DAG) を実行できるため、Apache Hive や Apache Pig などの Apache Hadoop エコシステムのプロジェクトで、人間が介在する応答時間やペタバイト規模の高速スループットに関する要件を満たすことができます。Hive 0.13 では、MapReduce ではなく Tez 上で Hive クエリを実行できます。

- **高可用性 (HA)** - サービスの可用性を高めるために、HDInsight によってデプロイされた Hadoop クラスターに 2 つ目のヘッドノードが追加されました。通常 Hadoop クラスターの標準的な実装には、ヘッド ノードは 1 つしかありません。HDInsight はセカンダリ ヘッドノードを追加することで、この単一障害点をなくします。顧客が既定の L サイズ ノードを使わず、XL サイズのヘッドノードでクラスターを作成しない限り、新しい HA クラスター構成に切り替えてもクラスターの価格は変わりません。

- **Hive のパフォーマンス** - Hive クエリの応答時間 (最大 40 x) および、**Optimized Row Columnar** (ORC) 形式によるデータ圧縮 (最大 80%) への重要な機能強化の序列。

- **Pig、Sqoop、Oozie、Ambari** -HDInsight クラスター Version 2.1 (HDP 1.3/Hadoop 1.2) でのパリティを提供する、HDInsight クラスター Version 3.0 (HDP 2.0/Hadoop 2.2) のコンポーネント バージョンのアップグレード。詳細については、以下のバージョンの各表を参照してください。

- **Mahout** - このスケーラブルな機械学習アルゴリズムのライブラリは、HDInsight 3.1 (以降) の Hadoop クラスターにプレインストールされています。そのため、他のクラスター構成を追加することなく、Mahout ジョブを実行できます。

- **Virtual Network のサポート** - HDInsight クラスターと Azure Virtual Network を組み合わせることにより、クラウド リソースの分離、つまり、クラウドのリソースとご利用のデータセンター内のリソースとをリンクさせたハイブリッド ソリューションを実現できます。


## サポートされているバージョン
次の表に、現在使用できる HDInsight のバージョン、該当する Hortonworks Data Platform のバージョン、そのリリース日を示します。非推奨となった場合は、サポート有効期限とその日付も記載されます。以下の点に注意してください。

* ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight 2.1 以降では既定でデプロイされています。HDInsight 1.6 クラスターでは利用できません。
* 特定のバージョンのサポート期限が切れると、Azure ポータルから利用できなくなる可能性があります。次の表に、Azure クラシック ポータルで利用できるバージョンを示します。クラスター バージョンは、非推奨となるまでは、Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) コマンドの `Version` パラメーターと .NET SDK で利用できます。

HDInsight のバージョン|HDP のバージョン|高可用性|リリース日|Azure ポータルでの利用|サポート有効期限|非推奨となる日
---|---|---|---|---|---|---
HDI 3.3|HDP 2.3|あり|2015 年 12 月 2 日|あり||
HDI 3.2|HDP 2.2|あり|2015 年 2 月 18 日|あり||
HDI 3.1|HDP 2.1|あり|2014 年 6 月 24 日|あり||
HDI 3.0|HDP 2.0|あり|02/11/2014|あり|09/17/2014|06/30/2015
HDI 2.1|HDP 1.3|あり|10/28/2013|あり|05/12/2014|05/31/2015
HDI 1.6|HDP 1.1|いいえ|10/28/2013|あり|04/26/2014|05/31/2015

**既定以外のクラスターのデプロイメント**

### HDInsight クラスター バージョンのサービス レベル アグリーメント

SLA は、"サポート ウィンドウ" の条件で定義されます。サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サービス & サポートによってサポートされる期間を指しています。HDInsight クラスターは、そのバージョンの**サポート有効期限**が現在の日付を過ぎている場合、サポート対象外となります。サポートされている HDInsight クラスターのバージョンの一覧は上記のテーブルに示されています。所定の HDInsight Version X (新しい X+1 バージョンが利用可能なった後) のサポート有効期限は、次の数式で計算した日付のうち、遅い方とされます。

- 数式 1: HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
- 数式 2: HDInsight クラスター バージョン X+1 (X の次のバージョン) がポータルで使用可能になった日付に 90 日を加える。

**非推奨となる日**は、それ以降 HDInsight でそのクラスター バージョンが作成できなくなる日付です。

> [AZURE.NOTE] HDInsight 2.1 と 3.0 クラスターはともに、64 ビット版 Windows Server 2012 R2 を使用し、.NET Framework 4.0、4.5、4.5.1 をサポートする Azure ゲスト OS [ファミリ 4](../cloud-services/cloud-services-guestos-update-matrix.md) で実行されます。

## HDInsight バージョンに対応する Hortonworks リリース ノート##

* HDInsight クラスター Version 3.3 は、[Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) を基盤とする Hadoop ディストリビューションを使用します。
	* Apache Storm リリース ノートは、[こちら](https://storm.apache.org/2015/11/05/storm0100-released.html)で入手できます。
	* Apache Hive リリース ノートは、[こちら](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)で入手できます。

* HDInsight クラスター Version 3.2 は、[Hortonworks Data Platform 2.2][hdp-2-2] を基盤とする Hadoop ディストリビューションを使用します。これが、ポータルの使用時に作成される**既定**の Hadoop クラスターです。

	* 各 Apache コンポーネントのリリース ノート - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)、[Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。


* HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2.1.7][hdp-2-1-7] を基盤とする Hadoop ディストリビューションを使用します。2014 年 11 月 7 日より前に作成された HDInsight 3.1 クラスターは、[Hortonworks Data Platform 2.1.1][hdp-2-1-1] に基づいています。

* HDInsight クラスター Version 3.0 は、[Hortonworks Data Platform 2.0][hdp-2-0-8] を基盤とする Hadoop ディストリビューションを使用します。

* HDInsight クラスター Version 2.1 は、[Hortonworks Data Platform 1.3][hdp-1-3-0] を基盤とする Hadoop ディストリビューションを使用します。

* HDInsight クラスター Version 1.6 は、[Hortonworks Data Platform 1.1][hdp-1-1-0] を基盤とする Hadoop ディストリビューションを使用します。


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0302_2016-->