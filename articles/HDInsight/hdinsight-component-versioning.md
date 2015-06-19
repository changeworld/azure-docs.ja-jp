<properties 
	pageTitle="HDInsight で提供される Hadoop クラスター バージョンの新機能 | Microsoft Azure" 
	description="HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。サポートされている Hadoop および Hortonworks Data Platform (HDP) ディストリビューションのバージョンを確認します。" 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="bradsev"/>


#HDInsight で提供される Hadoop クラスター バージョンの新機能

##HDInsight のバージョンと Hadoop コンポーネント
Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントがプロビジョニングされます。HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。Azure HDInsight で使用される既定のクラスター バージョンは、2014 年 11 月 7 日現在、HDP 2.1.7 を基盤とした 3.1 です。


<table border="1">
<tr><th>コンポーネント</th><th>HDInsight Version 3.2</th><th>HDInsight Version 3.1 (既定)</th><th>HDInsight Version 3.0</th><th>HDInsight Version 2.1</th></tr>
<tr><td>Hortonworks Data Platform</td><td>2.2</td><td>2.1.7</td><td>2.0</td><td>1.3</td></tr>
<tr><td>Apache Hadoop &amp; YARN</td><td>2.6.0</td><td>2.4.0</td><td>2.2.0</td><td>1.2.0</td></tr>
<tr><td>Tez</td><td>0.5.2</td><td>0.4.0</td><td></td><td></td></tr>
<tr><td>Apache Pig</td><td>0.14.0</td><td>0.12.1</td><td>0.12.0</td><td>0.11.0</td></tr>
<tr><td>Apache Hive &amp; HCatalog</td><td>0.14.0</td><td>0.13.1</td><td>0.12.0</td><td>0.11.0</td></tr>
<tr><td>HBase </td><td>0.98.4</td><td>0.98.0</td><td></td><td></td></tr>
<tr><td>Apache Sqoop</td><td>1.4.5</td><td>1.4.4</td><td>1.4.4</td><td>1.4.3</td></tr>
<tr><td>Apache Oozie</td><td>4.1.0</td><td>4.0.0</td><td>4.0.0</td><td>3.3.2</td></tr>
<tr><td>Zookeeper</td><td>3.4.6</td><td>3.4.5</td><td>3.4.5</td><td></td></tr>
<tr><td>Storm</td><td>0.9.3</td><td>0.9.1</td><td></td><td></td></tr>
<tr><td>Mahout</td><td>0.9.0</td><td>0.9.0</td><td></td><td></td></tr>
<tr><td>Phoenix</td><td>4.2.0</td><td>4.0.0.2.1.7.0-2162</td><td></td><td></td></tr>
</table>


**現在のコンポーネントのバージョンの取得**

HDInsight クラスターのバージョンに関連付けられたコンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。利用可能なコンポーネントを特定し、どのバージョンがクラスターに使用されているかどうかを確認する 1 つの方法は、Ambari REST API を使用することです。**GetComponentInformation** コマンドを使用すると、サービス コンポーネントに関する情報を取得できます。詳細については、「[Ambari のドキュメント][ambari-docs]」を参照してください。この情報を取得するもう 1 つの方法は、リモート デスクトップを使用してクラスターにログインし、"C:\\apps\\dist" ディレクトリの内容を直接確認することです。


**リリース ノート**

HDInsight の最新バージョンに関する追加のリリース ノートは、[HDInsight リリース ノート](hdinsight-release-notes.md)を参照してください。

### HDInsight クラスターのプロビジョニング時にバージョンを選択

HDInsight Windows PowerShell コマンドレットまたは HDInsight .NET SDK を使用してクラスターを作成するときは、"Version" パラメーターを使用して、HDInsight Hadoop クラスターのバージョンを選択することができます。

**[簡易作成]** オプションを使用すると、既定で Hadoop クラスターを作成する HDInsight バージョン 3.1 を取得します。Azure ポータルで **[カスタム作成]** オプションを使用する場合は、**[クラスターの詳細]** ページの **[HDInsight のバージョン]** ボックスで、デプロイされるクラスターのバージョンを選択できます。

##機能概要
HDInsight プラットフォームの重要な機能には、次のようなものがあります。

- **Storm** - 一般公開が開始された Azure HDInsight の Storm は、リアルタイム分析をすばやく簡単にデプロイできる機能で、数回クリックするだけで数分以内にデプロイが完了します。Azure HDInsight の Apache Storm は、Apache Hadoop エコシステムのオープン ソース プロジェクトの 1 つで、何百万ものイベントを信頼性の高い方法で処理できる分析プラットフォームへのアクセスを提供します。これにより、Hadoop ユーザーは、イベントが発生したときだけでなく、過去のイベントからも洞察を得ることができます。Microsoft では、Visual Studio とのビルトイン統合も提供しており、開発者は Storm を簡単に操作できます。Storm トポロジの開発、デプロイ、およびデバッグを、Visual Studio 内から実行できます。

- **Linux での HDInsight (プレビュー)** - Azure HDInsight には、Linux (Ubuntu) 仮想マシン (VM) 上で実行される Hadoop クラスターをプロビジョニングするオプションが用意されています。Linux または Unix に詳しい場合、既存の Linux ベースの Hadoop ソリューションから移行する場合、または Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、このオプションを使用して実行できます。Windows または Linux が実行されているクライアント コンピューターから、Azure ポータル、Azure クロス プラットフォーム コマンド ライン インターフェイス (CLI)、または HDInsight .NET SDK (Windows のみ) を使用して、Linux 上に HDInsight クラスターをプロビジョニングできます。

- **追加の VM サイズ** - HDInsight クラスターを利用できる VM の種類とサイズが増えました。HDInsight クラスターは、汎用目的でビルドされた A2 ～ A7 サイズ、ソリッド ステート ドライブ (SSD) と 60% 高速のプロセッサを特徴とする D シリーズ ノード、高速ネットワーク用 InfiniBand をサポートする A8 と A9 サイズを利用できます。Azure HDInsight で Apache HBase を使用するお客様は、D シリーズの大容量メモリ構成を利用してパフォーマンスを向上させることができます。また、Azure HDInsight で Apache Storm を使用するお客様は、追加メモリを活用して大規模な参照データ セットを読み込んだり、高速な CPU によってスループットを向上させたりできます。

- **クラスター スケーリング** - クラスター スケーリングを使用すると、実行する HDInsight クラスターのノード数を、削除または再作成しなくても変更できます。現在この機能があるのは Hadoop クエリと Apache Storm のみですが、Apache HBase も近日中にサポートされます。

- **スクリプト操作** - このクラスター カスタマイズ機能により、カスタム スクリプトを使用して任意の方法で Hadoop クラスターを変更できます。この新機能により、ユーザーは、Apache Hadoop エコシステムから入手できるプロジェクトを検証し、Azure HDInsight クラスターにデプロイできます。このカスタマイズ機能は、Hadoop、HBase、Storm など、すべての種類の HDInsight クラスターで使用できます。

- **HBase** - HBase は、ビッグ データのオンライン トランザクション処理を可能にする遅延時間の短い NoSQL データベースです。HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよび利用統計情報のデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。

- **Apache Phoenix** - Apache Phoenix は、HBase 上の構造化照会言語 (SQL) クエリ レイヤーです。セカンダリ インデックスのレポートなど、SQL クエリ言語仕様のサブセットを限定的にサポートします。また、HBase データ上で遅延時間が短いクエリを対象とする、クライアント内蔵の Java Database Connectivity (JDBC) ドライバーとして配信されます。Apache Phoenix は SQL クエリを引き継ぎ、一連の HBase スキャンおよびコプロセッサの呼び出しにコンパイルし、通常の JDBC 結果セットを生成します。Apache Phoenix は HBase 上のリレーショナル データベース レイヤーです。また、HBase データ上で遅延時間が短いクエリを対象とする、クライアント内蔵の JDBC ドライバーとして配信されます。Apache Phoenix は SQL クエリを引き継ぎ、一連の HBase スキャンにコンパイルし、それらのスキャンの実行を調整し、通常の JDBC 結果セットを生成します。

- **クラスター ダッシュボード** - HDInsight クラスターにデプロイする新しい Web アプリケーション。Hive クエリの実行、ジョブ ログの確認、Azure BLOB ストレージの表示に使用します。この Web アプリケーションにアクセスするための URL は <*クラスター名*>.azurehdinsight.net です。

- **Microsoft Avro ライブラリ** - このライブラリは、Microsoft.NET 環境向けに Apache Avro データ シリアル化システムを実装します。Apache Avro は、シリアル化のためのコンパクトなバイナリ データ交換形式を提供します。Apache Avro は、JavaScript Object Notation (JSON) を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。ある言語でシリアル化されたデータは、別の言語で読むことができます。現在、C、C++、C#、Java、PHP、Python、および Ruby がサポートされています。Apache Avro シリアル化形式は、Hadoop MapReduce ジョブ内の複雑なデータ構造を表すために Azure HDInsight で広く使用されています。

- **YARN** - Hadoop クラスターでのデータ処理に使用される従来の Apache Hadoop MapReduce フレームワークに代わる、新しい汎用的な分散アプリケーション管理フレームワーク。Hadoop オペレーティング システムとして効果的に機能し、単一の目的のバッチ処理用データ プラットフォームであった Hadoop を、バッチ、インタラクティブ、オンライン、ストリームの各処理が可能な多目的プラットフォームにします。この新しい管理フレームワークにより、容量の保証、公平性、サービス レベル アグリーメント (SLA) などの基準に従って、スケーラビリティおよびクラスター使用率が向上します。

- **Tez (HDInsight 3.1 以降のみ)** -Hadoop で小規模なワークロードと大規模なワークロードの両方にまたがる簡単なデータ処理タスクを作成する、カスタマイズ可能な汎用フレームワーク。1 つのジョブでタスクの複雑な無閉路有効グラフ (DAG) を実行できるため、Apache Hive や Apache Pig などの Apache Hadoop エコシステムのプロジェクトで、人間が介在する応答時間やペタバイト規模の高速スループットに関する要件を満たすことができます。Hive 0.13 では、MapReduce ではなく Tez 上で Hive クエリを実行できます。

- **高可用性 (HA)** - サービスの可用性を高めるために、HDInsight によってデプロイされた Hadoop クラスターに 2 つ目のヘッドノードが追加されました。通常 Hadoop クラスターの標準的な実装には、ヘッド ノードは 1 つしかありません。HDInsight はセカンダリ ヘッドノードを追加することで、この単一障害点をなくします。顧客が既定の L サイズ ノードを使わず、XL サイズのヘッドノードでクラスターをプロビジョニングしない限り、新しい HA クラスター構成に切り替えてもクラスターの価格は変わりません。

- **Hive のパフォーマンス** - Hive クエリの応答時間 (最大 40 x) および、**Optimized Row Columnar** (ORC) 形式によるデータ圧縮 (最大 80%) への重要な機能強化の序列。

- **Pig、Sqoop、Oozie、Ambari** -HDInsight クラスター Version 2.1 (HDP 1.3/Hadoop 1.2) でのパリティを提供する、HDInsight クラスター Version 3.0 (HDP 2.0/Hadoop 2.2) のコンポーネント バージョンのアップグレード。詳細については、以下のバージョンの各表を参照してください。

- **Mahout** - このスケーラブルな機械学習アルゴリズムのライブラリは、HDInsight 3.1 (以降) の Hadoop クラスターにプレインストールされています。そのため、他のクラスター構成を追加することなく、Mahout ジョブを実行できます。

- **Virtual Network のサポート** - HDInsight クラスターと Azure Virtual Network を組み合わせることにより、クラウド リソースの分離、つまり、クラウドのリソースとご利用のデータセンター内のリソースとをリンクさせたハイブリッド ソリューションを実現できます。


## サポートされているバージョン
次の表に、現在使用できる HDInsight のバージョン、該当する Hortonworks Data Platform のバージョン、そのリリース日を示します。非推奨となった場合は、サポート有効期限とその日付も記載されます。以下の点に注意してください。

* ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight 2.1 以降では既定でデプロイされています。HDInsight 1.6 クラスターでは利用できません。
* 特定のバージョンのサポート期限が切れると、Azure ポータルから利用できなくなる可能性があります。次の表に、Azure ポータルで利用できるバージョンを示します。クラスター バージョンは、非推奨となるまでは、Windows PowerShell [New-AzureHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) コマンドの `Version` パラメーターと .NET SDK で利用できます。 

<table border="1"> <tr><th>HDInsight のバージョン</th><th>HDP のバージョン</a><th>高可用性</th></th><th>リリース日</th><th>Azure ポータルでの利用</th><th>サポート有効期限</th><th>非推奨となる日</th></tr> <tr><td>HDI 3.2</td><td>HDP 2.2</td><td>あり</td><td>2015 年 2 月 18 日</td><td>あり</td><td></td><td></td></tr> <tr><td>HDI 3.1</td><td>HDP 2.1</td><td>あり</td><td>2014 年 6 月 24 日</td><td>あり</td><td></td><td></td></tr> <tr><td>HDI 3.0</td><td>HDP 2.0</td><td>あり</td><td>2014 年 2 月 11 日</td><td>あり</td><td>2014 年 9 月 17 日</td><td>2015 年 6 月 30 日</td></tr> <tr><td>HDI 2.1</td><td>HDP 1.3</td><td>あり</td><td>2013 年 10 月 28 日</td><td>なし</td><td>2014 年 5 月 12 日</td><td>2015 年 5 月 31 日</td></tr> <tr><td>HDI 1.6</td><td>HDP 1.1</td><td>なし</td><td>2013 年 10 月 28 日</td><td>なし</td><td>2014 年 4 月 26 日</td><td>2015 年 5 月 31 日</td></tr> </table><br>

**既定以外のクラスターのデプロイメント**

Hadoop 2.4 では、既定では HDInsight 3.1 クラスターが作成されるため、他のバージョンの HDInsight クラスターを作成する場合は、ポータルで **[カスタム作成]** オプションを使用してバージョンを指定する必要があります。

### HDInsight クラスター バージョンのサービス レベル アグリーメント

SLA は、"サポート ウィンドウ" の条件で定義されます。サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サービス & サポートによってサポートされる期間を指しています。HDInsight クラスターは、そのバージョンの**サポート有効期限**が現在の日付を過ぎている場合、サポート対象外となります。サポートされている HDInsight クラスターのバージョンの一覧は上記のテーブルに示されています。所定の HDInsight Version X (新しい X+1 バージョンが利用可能なった後) のサポート有効期限は、次の数式で計算した日付のうち、遅い方とされます。

- 数式 1: HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
- 数式 2: HDInsight クラスター バージョン X+1 (X の次のバージョン) が Azure ポータルで使用可能になった日付に 90 日を加える。

**非推奨となる日**は、それ以降 HDInsight でそのクラスター バージョンが作成できなくなる日付です。

> [AZURE.NOTE]HDInsight 2.1 と 3.0 クラスターはともに、64 ビット版 Windows Server 2012 R2 を使用し、.NET Framework 4.0、4.5、4.5.1 をサポートする Azure ゲスト OS [ファミリ 4](../cloud-services-guestos-update-matrix.md) で実行されます。

## HDInsight バージョンに対応する Hortonworks リリース ノート##

* HDInsight クラスター Version 3.2 は、[Hortonworks Data Platform 2.2][hdp-2-2] を基盤とする Hadoop ディストリビューションを使用します。

	* 各 Apache コンポーネントのリリース ノート - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)、[Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。


* HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2.1.7][hdp-2-1-7] を基盤とする Hadoop ディストリビューションを使用します。2014 年 11 月 7 日以降は、これが Azure HDInsight ポータルの使用時に作成される**既定の** Hadoop クラスターです。2014 年 11 月 7 日より前に作成された HDInsight 3.1 クラスターは、[Hortonworks Data Platform 2.1.1][hdp-2-1-1] に基づいています。

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

<!--HONumber=54--> 